---
title: 'Landing Zones як код: Terraform-блюпринт для Azure Databricks і pipelines-as-code'
url: '/ua/blog/2026-08-17-terraform-blueprint-azure-databricks-landing-zones'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-12/landing-zones-as-code-concept.jpg'
summary_image: '/blog-images/post-12/landing-zones-as-code-short.jpg'
sharing_image: '/static-blog-images/post-12/landing-zones-as-code-share.jpg'
twitter_sharing_image: '/static-blog-images/post-12/landing-zones-as-code-twitter-share.jpg'
alt: 'Банер до статті Landing Zones як код: Terraform-блюпринт для Azure Databricks і pipelines-as-code'
keywords: [ 'Terraform', 'Azure Databricks', 'IaC', 'data landing zone', 'Databricks Asset Bundles', 'Unity Catalog', 'DevOps' ]
date: 2026-08-17
blog_tags: [ 'Terraform', 'Azure', 'IaC', 'Databricks', 'DevOps' ]
draft: false
---

## {{< color-text text="Проблема" >}}

У
{{< color-link link_title="Частині 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}}
 ми зібрали FinOps-пайплайн на **Azure Databricks**, який зводить рахунки за інфраструктуру Azure та DBU Databricks
в один cost lakehouse у форматі FOCUS. Це працює. Але якщо середовище, в якому він працює, зібрали вручну через Azure
portal, ви отримуєте платформу, яку ніхто не може перевірити, відтворити або відкотити.

Платформи, зібрані через portal, ламаються тихо. Мережева політика, яку хтось додав під час інциденту, не задокументована.
Другий workspace відрізняється від першого в деталях, які ніхто не може назвати. А неатрибутовані кластери, що
наповнили bucket "unmatched costs" у {{< color-link link_title="Частині 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}},
з'явилися саме тому, що тегування залишили на ручну дисципліну, а не на механізм.

Рішення просте: описати все як код, від landing zones, які тримають платформу, до governance навколо неї і pipeline-ів
усередині неї. У цій статті ми розбираємо цей план на **Terraform** та **Databricks Asset Bundles**, використовуючи ті
самі open-source модулі, які ми обіцяли в {{< color-link link_title="Частині 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}}.
У підсумку кожен шар стає придатним для рев'ю в pull request.

## {{< color-text text="Що таке Data Landing Zone?" >}}

**Data Landing Zone (DLZ)** -- це робоче середовище для data-сервісів, таких як Azure Databricks workspace-и, storage
accounts і ingestion-сервіси. У нашій реалізації воно працює як application landing zone під **Azure Platform Landing Zone**,
централізованою основою, яка керує спільними можливостями для всіх workload-ів. Це відповідає
{{< color-link link_title="Cloud Adoption Framework для єдиної data-платформи" path="https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/data/architecture-azure-landing-zones-unify-data-platform" target="_blank" >}}
.

Розділення важливе, бо воно відокремлює delivery workload-ів від організаційного governance. Data engineering-команди
ітеративно працюють усередині свого DLZ, тоді як Platform team змінює спільну основу під суворішим рев'ю. Саме запис
цієї межі в Terraform state layout, а не лише на діаграмі, робить governance реальним.

## {{< color-text text="Landing zones у Terraform" >}}

### Топологія platform і data landing zone, яку ми розгортаємо

Platform foundation ми розгортаємо за допомогою Azure Landing Zones Accelerator і його
{{< color-link link_title="platform landing zone template" path="https://github.com/Azure/alz-terraform-accelerator/tree/main/templates/platform_landing_zone" target="_blank" >}}
. Він створює ієрархію management group-ів і налаштовує central management та connectivity subscriptions. Модуль
{{< color-link link_title="data landing zone" path="https://github.com/PerfectThymeTech/terraform-azurerm-data-landing-zone" target="_blank" >}}
розгортає кожен workload subscription: Databricks workspace-и, ADLS Gen2 storage, Key Vault і мережеве з'єднання
з hub-spoke fabric платформи (Рисунок 1).

{{< img src="/blog-images/post-12/dmlz-dlz-terraform-topology.png" alt="Рисунок 1. Основа governance у Platform Landing Zone та workload subscription у Data Landing Zone, з management group-ами, hub-spoke networking і private endpoints." >}}

Фіксація на релізному tag робить оновлення свідомими:

```hcl
module "platform_landing_zone" {
  source = "github.com/Azure/alz-terraform-accelerator.git//templates/platform_landing_zone?ref=v17.4.0"

  subscription_ids                = var.subscription_ids
  starter_locations               = var.starter_locations
  management_groups_enabled       = true
  management_group_settings       = var.management_group_settings
  management_resources_enabled    = true
  management_resource_settings    = var.management_resource_settings
  connectivity_type               = "hub_and_spoke_vnet"
  connectivity_resource_groups    = var.connectivity_resource_groups
  hub_and_spoke_networks_settings = var.hub_and_spoke_networks_settings
  hub_virtual_networks            = var.hub_virtual_networks
}
```

### Azure organization: platform landing zone керує workload-ами

{{< color-link link_title="Azure landing zone reference architecture" path="https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/?tabs=conceptual#azure-landing-zone-architecture" target="_blank" >}}
 Microsoft починає з одного platform landing zone. Наш підхід той самий: management group-и впорядковують subscription-и,
Azure Policy застосовується на потрібному management-group scope, а спільні management та connectivity сервіси живуть
у виділених platform subscriptions. DLZ -- це workload, або application, landing zone під цією основою.

Спільний data-platform
{{< color-link link_title="management group" path="https://learn.microsoft.com/en-us/azure/governance/management-groups/overview" target="_blank" >}}
 дає межу для команд. Кожен subscription нижче успадковує однакові guardrail-і без окремого налаштування, а RBAC
на рівні subscription дозволяє Data engineering-команді тримати Contributor на власному DLZ без зміни ресурсів
Platform team.

### Мережа: hub-spoke і private endpoints як входи модуля

**Platform Landing Zone** надає hub-and-spoke network. **Data Landing Zone** споживає цю спільну основу як input-и:
virtual network, network security group і route-table IDs. Потім DLZ module під'єднує свої subnets, private endpoints
і private DNS records до platform network. Databricks workspace-и підіймаються з secure cluster connectivity і без
public storage paths, бо дефолти модуля виходять із
{{< color-link link_title="Azure landing zone design areas" path="https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/ready/landing-zone/" target="_blank" >}}
 baseline, а не поводяться так, ніби безпека з'явиться сама собою.

### Unity Catalog працює на рівні account scope і охоплює workspace-и

Для нових Azure Databricks workspace-ів Unity Catalog вмикається автоматично. Metastore -- це account-level,
regional container, який може обслуговувати кілька workspace-ів, а Azure Databricks керує його створенням і
призначенням workspace-ам у нашому розгортанні.

## {{< color-text text="Хто за що відповідає" >}}

Платформою керують дві команди, і контракт між ними -- це набір Terraform input-ів та output-ів, а не wiki-сторінка.
Перш ніж ділити роботу, варто пам'ятати, що в самого Azure Databricks є два scope-и, які теж потрібно розділяти.

### Databricks account scope проти workspace scope у Terraform providers

Azure Databricks має два API scope-и, і для кожного потрібен окремий provider block. **Account scope**, який працює
через
{{< color-link link_title="account console" path="https://learn.microsoft.com/en-us/azure/databricks/admin/" target="_blank" >}}
 і адресований через account ID, керує account-level identities і показує Unity Catalog metastore, спільний для
workspace-ів у своєму регіоні. Його users, groups і service principals
{{< color-link link_title="identity federation" path="https://learn.microsoft.com/en-us/azure/databricks/admin/users-groups/" target="_blank" >}}
 синхронізуються з Entra ID. У нього ширший scope.
**Workspace scope** керує clusters, jobs і warehouses усередині одного workspace і відповідає DLZ. У Terraform цей
розподіл виглядає прямо:

```hcl
provider "databricks" {
  alias      = "account"
  host       = "https://accounts.azuredatabricks.net"
  account_id = var.databricks_account_id
}

provider "databricks" {
  alias = "workspace"
  host  = module.data_landing_zone.databricks_workspace_url
}
```

Workspace-scoped resources належать до DLZ-коду або до bundles. Account-scoped data-governance ресурси залишаються
відповідальністю Platform team.

### Контракт Platform team: input-и та Azure Policy guardrail-и

Platform team споживає організаційні факти й видає платформу у відповідь. Його input-и -- management-group і
subscription IDs, CIDR allocations, Entra ID group object IDs, naming-and-tagging standard та Databricks account ID.
Platform Landing Zone повертає hub VNet, NSG і route-table IDs, які DLZ потрібні для розгортання мережево-пов'язаних
ресурсів.

**Azure Policy** -- це шар обмежень для всього, що створюють після цього, ким завгодно.
{{< color-link link_title="Built-in policies" path="https://learn.microsoft.com/en-us/azure/governance/policy/samples/built-in-policies" target="_blank" >}}
 на management group забороняють ресурси поза регіоном, обмежують cluster node VM SKU до дозволеного списку,
відхиляють ресурси без тега `cost_center`, забороняють public network access на storage accounts і автоматично
розгортають diagnostic settings. Політиці байдуже, чи прийшов запит з Terraform, через клік у portal, чи від Databricks
cluster, який створює VM: порушники просто не створюються.

### Контракт data engineering team: що вони отримують і чим володіють

Data engineering team отримує робочу поверхню як Terraform output-и: workspace URL-и, Unity Catalog catalog і schema
names, external locations, cluster policy IDs, SQL warehouse IDs, service principal IDs і storage paths. Вони володіють
усім, що пов'язане з workload: jobs, Lakeflow pipelines, notebooks, bundle configs і SQL. Межі тримаються конструктивно, бо
Azure Policy і cluster policies роблять неатрибутовані або out-of-region ресурси неможливими, а не просто забороненими:

| Контракт | Platform team | Data engineering team |
|---|---|---|
| Input-и | Management-group/subscription IDs, CIDR plan, Entra ID group IDs, tagging standard, Databricks account ID; Platform Landing Zone outputs: hub VNet/NSG/route-table IDs для DLZ deployment | Workspace URL-и, UC catalog-и/schema-и, external locations, cluster policy IDs, warehouse IDs, service principal IDs |
| Володіє | Management group-и, platform subscriptions, networking, workspaces, cluster policies, Azure Policy assignments | Jobs, Lakeflow pipelines, notebooks, bundle configs, SQL |
| Обмежено | Tenant limits, CAF baseline, platform approval gate | Azure Policy і cluster policies: regions, VM SKU, обов'язкові теги |

Секція про trade-offs нижче відповідає на те саме запитання для інструментів, а ця таблиця відповідає за команди, і
ці дві межі навмисно збігаються.

## {{< color-text text="Terraform-дисципліна" >}}

### Remote state isolation для кожної зони та середовища

Правило тут одне: один state file на шар і на environment. Platform landing zone і кожен DLZ отримують свій `azurerm`
backend key, щоб невдалий apply у workload zone не зламав організаційну основу, а доступ до state, який містить secrets
у відкритому вигляді, ішов тією самою межею, що й самі subscriptions:

```hcl
terraform {
  backend "azurerm" {
    storage_account_name = "rstfstateprd"
    container_name       = "tfstate"
    key                  = "dlz01.prd.tfstate"
    use_azuread_auth     = true
  }
}
```

### Plan/apply CI/CD з approval gates

З laptop-а нічого не застосовується. Pipeline запускає `terraform plan` на кожен pull request і публікує diff для
рев'ю; `terraform apply` виконується лише після merge, від service principal, чиї права обмежені лише тим шаром, який
він розгортає, і тільки після manual approval. Отже, кожен Terraform-шар отримує людське рев'ю після автоматичного
рев'ю і до того, як зміни потрапляють в Azure. Це та сама двошвидкісна межа з розділу з визначенням, тільки вже
закріплена CI/CD automation замість звички.

### Теги, enforced під час розгортання: закриваємо unmatched bucket з Частини 1

Тегування під час розгортання -- це місце, де IaC платить FinOps-рахунок. У
{{< color-link link_title="Частині 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}}
 кожен ресурс, створений поза Databricks control plane, потрапляв в "unmatched costs" bucket, бо не мав тегів
атрибуції. З landing zone в Terraform теги `cost_center` і `team_owner` стають обов'язковими input-ами модуля, а
Databricks cluster policies, які теж є Terraform resources, примушують `custom_tags` на кожному cluster, який створює
користувач. Unmatched bucket не зникає, але перестає рости за замовчуванням і починає зменшуватися через policy.

## {{< color-text text="Pipelines як код" >}}

### Databricks Asset Bundles: структура та environment targets

**Databricks Asset Bundles (DABs)** описують артефакти workspace, такі як jobs, Lakeflow pipelines і notebooks, у YAML,
який живе поруч із кодом, що він розгортає. Один
{{< color-link link_title="bundle" path="https://learn.microsoft.com/en-us/azure/databricks/dev-tools/bundles/" target="_blank" >}}
 визначає кілька targets, тож dev і prod -- це одна й та сама дефініція з різними workspace-ами та ідентичностями запуску
(Рисунок 2).

{{< img src="/blog-images/post-12/asset-bundle-cicd-flow.png" alt="Рисунок 2. CI/CD flow Databricks Asset Bundle, який проходить із dev target у prod target через pull-request validation." >}}

### FOCUS transform job як workflow, яким керує bundle

Запланована SQL-трансформація з
{{< color-link link_title="Частини 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}},
 та сама, що мапить `system.billing.usage` у FOCUS-колонки, стає job resource у bundle замість job-а, який хтось
колись налаштував у UI:

```yaml
bundle:
  name: focus-cost-pipeline

resources:
  jobs:
    focus_transform:
      name: focus-transform-${bundle.target}
      schedule:
        quartz_cron_expression: "0 0 3 * * ?"
        timezone_id: UTC
      tasks:
        - task_key: system_tables_to_focus
          sql_task:
            file:
              path: ../sql/system_tables_to_focus.sql
            warehouse_id: ${var.warehouse_id}
```

```yaml
targets:
  dev:
    default: true
    workspace:
      host: https://adb-dev.azuredatabricks.net
  prod:
    workspace:
      host: https://adb-prod.azuredatabricks.net
    run_as:
      service_principal_name: ${var.pipeline_sp}
```

`databricks bundle deploy -t prod` стає єдиним шляхом у production, а CI pipeline, який його запускає, -- це та сама
pull-request-gated механіка, яку використовують Terraform-шари.

### Unity Catalog objects через Terraform Databricks provider

Catalog-и, schema-и та grants залишаються в Terraform, яким керують через
{{< color-link link_title="Databricks Terraform provider" path="https://registry.terraform.io/providers/databricks/databricks/latest" target="_blank" >}}
 (ми pin-имо _1.127.0_). Це governance objects з ширшим blast radius, ніж окремий job, тому їхній дизайн і рев'ю
належать Platform team. Bundles деплоять workload-и в цю структуру, але не визначають її.

## {{< color-text text="Компроміси" >}}

### DABs проти Terraform: хто володіє workspace objects

Обидва інструменти можуть створити Databricks job, і саме так команди отримують drift і подвійну відповідальність. Наш
розподіл іде за lifecycle і blast radius:

| Питання | Власник | Чому |
|---|---|---|
| Workspaces, networking, storage | Terraform (landing zone modules) | Lifecycle інфраструктури, blast radius на рівні subscription |
| Catalog-и, schema-и, grants | Terraform (Databricks provider) | Governance objects, спільні для кількох команд |
| Cluster policies, warehouses | Terraform | Platform guardrails, змінює Platform team |
| Jobs, Lakeflow pipelines, notebooks | Asset Bundles | Lifecycle workload-у, який деплоїть код разом із ним |

Правило просте: якщо зміну мав би рев'ювати Platform team, це Terraform; якщо workload команда володіє цим end to end,
це bundle.

### Коли upstream-модулів достатньо, а коли їх форкати

Upstream-модулів достатньо, коли ваша топологія збігається з їхньою, і це обмеження є перевагою: воно тримає вас близько
до Cloud Adoption Framework baseline і дозволяє отримувати оновлення простим bump-ом tag-а. Ми форкнули лише тоді,
коли натрапили на input-и, які модулі не експонували, і ставимося до форка як до технічного боргу з постійним завданням
повернути різницю upstream. Не спрацював проміжний варіант, який ми спочатку й спробували, wrapper навколо модуля з
patched outputs через `azapi` resources: wrapper ламався на кожному minor release і виглядав гірше за будь-який з
чистих варіантів.

## {{< color-text text="Висновок" >}}

Чесна ціна спочатку: pinned module versions старіють без cadence на рев'ю, data landing zone module ще pre-1.0 і може
міняти інтерфейси між minor releases, а межа між DAB і Terraform -- жива лінія, яка з різними CLI releases рухалася,
тому її треба перевіряти під час upgrade. Але це не перекреслює виграш. Platform foundation, workload landing zone і
FOCUS transform job можуть існувати як код, який колега бачить у рев'ю ще до того, як він з'явиться в Azure. Якщо ви
вже запускаєте pipeline з {{< color-link link_title="Частини 1" path="/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus" target="_blank" >}},
почніть із того, що дає найшвидший ефект: покладіть FOCUS transform job у bundle, додайте cluster policy для
обов'язкового тегування і подивіться, як unmatched bucket зменшиться вже в наступному billing cycle.

DMLZ тут навмисно не показаний як реалізований шар. Ми плануємо окремо дослідити його модель відповідальності для
org-wide data governance і catalog capabilities, включно з Microsoft Purview і shared Microsoft Fabric capacity, перш
ніж додавати його до платформи.

## {{< color-text text="FAQ" >}}

### Чи може Azure Databricks нативно експортувати FOCUS data?

Лише зі сторони Azure. Azure Cost Management може за розкладом експортувати інфраструктурні витрати в нативному
форматі FOCUS в ADLS Gen2, але споживання DBU Databricks живе в system tables (`system.billing.usage` і
`system.billing.list_prices`), які не відповідають FOCUS. Цю прогалину закриває запланована SQL-трансформація;
опублікований запит Databricks "FOCUS 1.3" -- добра відправна точка.

### Як з'єднати білінгові дані Azure з DBU Databricks?

Через теги. Azure Databricks додає `ClusterId` та `WorkspaceId` до кожної VM, яку створює, тож pipeline парсить
колонку `Tags` у білінговому потоці Azure та зіставляє ці значення з `usage_metadata.cluster_id` у system tables
Databricks. Кошик unmatched costs лишайте для ресурсів, створених поза Databricks control plane, бо вони таких тегів
не мають.

### Чому витрати на serverless Databricks обліковуються двічі?

Бо ціна serverless DBU вже включає обчислення на VM. Якщо pipeline зіставить інфраструктурні рядки Azure з
serverless workload-ом і додасть їх до вартості DBU, те саме обладнання буде оплачено двічі. ETL має розгалужуватися
за джерелом білінгу: додавати інфраструктурні витрати лише для classic compute, а для serverless workload-ів
вважати вартість DBU повною.

### Як обробляти ретроспективні перерахунки білінгу Azure?

Через ідемпотентні `upsert`-операції. Гіперскейлери перераховують білінгові дані протягом усього місяця, тож pipeline
застосовує кожне оновлення через Delta Lake `MERGE INTO` з ключами `ChargePeriodStart`, `ResourceId` і `SkuId`.
Змаплені рядки оновлюються, нові вставляються, а дублікати ніколи не накопичуються. Delta `time travel` зберігає
аудиторський слід того, що й коли змінилося.
