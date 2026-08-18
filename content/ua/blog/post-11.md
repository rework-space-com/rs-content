---
title: 'Поза межами роздільного білінгу: поєднання інфраструктури Azure та Databricks DBU за допомогою FOCUS'
url: '/ua/blog/2026-08-12-merging-azure-databricks-costs-with-focus'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-11/rework_space_blog_banner_v2.jpg'
summary_image: '/blog-images/post-11/unified-cost-lakehouse-short.jpg'
sharing_image: '/static-blog-images/post-11/unified-cost-lakehouse-share.jpg'
twitter_sharing_image: '/static-blog-images/post-11/unified-cost-lakehouse-twitter-share.jpg'
alt: 'Банер до статті Поза межами роздільного білінгу: поєднання інфраструктури Azure та Databricks DBU за допомогою FOCUS'
keywords: [ 'FinOps', 'FOCUS', 'Azure Databricks', 'ITFM', 'cost management', 'lakehouse', 'DBU' ]
date: 2026-08-12
blog_tags: [ 'FinOps', 'Azure Databricks', 'DevOps' ]
draft: false
---

## {{< color-text text="Проблема" >}}

Щомісяця ваша платформа Databricks генерує два рахунки. Azure виставляє рахунок за віртуальні машини, локальні SSD,
преміум-сховище та мережевий трафік, на яких працюють ваші кластери, тоді як Databricks обліковує споживання {{< color-link link_title="DBU" path="https://www.databricks.com/product/pricing" target="_blank" >}} у власних системних таблицях.
Числа, яке всім насправді потрібне, справжньої вартості окремого робочого навантаження, немає в жодному з них.

Тож команди роблять те, що роблять завжди: експортують обидва набори даних в Excel і звіряють їх вручну. Це повільно,
це схильне до помилок, і це доводиться повторювати щомісяця, бо гіперскейлери заднім числом коригують білінгові дані.
У межах **IT Financial Management ({{< color-link link_title="ITFM" path="https://www.apptio.com/topics/it-financial-management/" target="_blank" >}})**, ключової дисципліни Technology Business Management, цей патерн має назву:
IT-керівники кажуть, що більшість свого часу працюють
{{< color-link link_title="data janitors" path="https://www.techdogs.com/td-dictionary/word/data-janitor" target="_blank" >}}
 ("прибиральниками даних"), очищаючи та звіряючи сирі білінгові записи замість того, щоб їх аналізувати.

Є кращий шлях, і він не вимагає купівлі ще однієї платформи. У цій статті ми розбираємо FinOps MVP, який ми побудували
для наших клієнтів: пайплайн, що працює на самому **Azure Databricks**, приймає обидва білінгові потоки та об'єднує їх
в єдиний "lakehouse" витрат у форматі **FOCUS**. Ми розглянемо формати сирих даних, ключі з'єднання, підводні камені та
те, як готовий продукт виглядає для бізнесу.

## {{< color-text text="Що таке FOCUS?" >}}

**FinOps Open Cost and Usage Specification (FOCUS)** -- це відкритий стандарт від
{{< color-link link_title="FinOps Foundation" path="https://focus.finops.org/" target="_blank" >}}
 , який визначає єдину спільну схему даних про витрати та використання для всіх вендорів. Замість підтримки окремих
мапінгів для білінгового експорту кожного провайдера ви отримуєте уніфіковані колонки, як-от `BilledCost`,
`EffectiveCost`, `ChargePeriodStart` і `ResourceId`, з однаковою семантикою всюди.

Для пайплайна об'єднання це змінює характер роботи. Пайплайн більше не витрачає свої цикли на перейменування
вендорських колонок і вгадування їхнього значення. Валідація стає легкою, а інженерні зусилля переміщуються туди, де
вони заробляють гроші: на з'єднання, збагачення та надання даних (Рисунок 1).

{{< img src="/blog-images/post-11/traditional-vs-focus-based.png" alt="Рисунок 1. Традиційний Medallion-пайплайн витрат з ручним мапінгом схем проти пайплайна на основі FOCUS з легкою валідацією." >}}

## {{< color-text text="Два потоки білінгу" >}}

### Витрати на інфраструктуру Azure: нативний експорт FOCUS в ADLS Gen2

Сторона Azure -- це проста половина. Azure Cost Management підтримує
{{< color-link link_title="scheduled exports" path="https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/tutorial-improved-exports" target="_blank" >}}
 ("заплановані експорти") наборів даних про витрати безпосередньо в обліковий запис сховища ADLS Gen2, і одним із
підтримуваних вихідних форматів є нативний набір даних
{{< color-link link_title="Cost and Usage Details (FOCUS)" path="https://learn.microsoft.com/en-us/azure/cost-management-billing/dataset-schema/cost-usage-details-focus" target="_blank" >}}
 . Цей потік охоплює все, що споживають обчислення Databricks на нижньому рівні: віртуальні машини, диски та мережу.
Ви налаштовуєте його один раз, і файли у форматі FOCUS щодня з'являються у вашому сховищі.

### Databricks DBU: системні таблиці, які не розмовляють мовою FOCUS

Сторона Databricks потребує роботи. Споживання платформи доступне через
{{< color-link link_title="system tables" path="https://learn.microsoft.com/en-us/azure/databricks/admin/system-tables/" target="_blank" >}}
 ("системні таблиці"), насамперед `system.billing.usage` та `system.billing.list_prices`. Вони деталізовані та
надійні, але не відповідають FOCUS "з коробки". Вони містять сирі погодинні записи використання, тарифи DBU та
регіональні SKU у власній схемі Databricks.

Тому MVP запускає заплановану SQL-трансформацію, яка мапить системні таблиці в колонки FOCUS. Databricks публікує
запит
{{< color-link link_title="system tables to FOCUS 1.3 query" path="https://github.com/databricks-solutions/cloud-infra-costs/tree/main/focus" target="_blank" >}}
 , який ми використовуємо як відправну точку. Ядро мапінгу виглядає так:

| Джерело Databricks | Колонка FOCUS | Трансформація |
|---|---|---|
| `usage_start_time` / `usage_end_time` | `ChargePeriodStart` / `ChargePeriodEnd` | Обрізати до `YYYY-MM-DDTHH:MM:SSZ`, відкинути опціональні мілісекунди |
| `usage_quantity` з'єднане з `list_prices` | `EffectiveCost` | З'єднати за SKU та вікном чинності ціни, помножити кількість на тариф |
| `billing_origin_product` | `ServiceName` | Змапити коди продуктів (JOBS, SQL, DLT) у читабельні назви сервісів |
| `usage_metadata.cluster_id` | `ResourceId` | Нормалізувати відповідно до значень тегів, які Azure записує на VM |
| `sku_name` | `SkuId` | Передати без змін, зберегти префікс регіону |

Рядок з часовими мітками важливіший, ніж здається. Специфікація FOCUS вимагає точності до секунд
(`YYYY-MM-DDTHH:mm:ssZ`), але системні таблиці Databricks видають мітки з мілісекундною точністю, тож трансформація
обрізає їх до того, як два потоки взагалі зустрінуться. Пропуск цього кроку -- найпоширеніша причина тихих збоїв
завантаження даних, які ми бачимо.

## {{< color-text text="Побудова пайплайна" >}}

Коли обидва потоки визначено, завдання "Data Engineer" -- змусити два джерела білінгу, які ніколи одне про одне не чули,
говорити в межах одного lakehouse. Databricks документує загальний підхід у своєму рішенні
{{< color-link link_title="cloud infrastructure costs field solution" path="https://github.com/databricks-solutions/cloud-infra-costs" target="_blank" >}}
 , а стаття
{{< color-link link_title="Getting the Full Picture" path="https://www.databricks.com/blog/getting-full-picture-unifying-databricks-and-cloud-infrastructure-costs" target="_blank" >}}
 пояснює, чому об'єднання варте зусиль. Ось що насправді забирає інженерний час.

### Потокове завантаження з Auto Loader

**Auto Loader** стрімить сирі файли Azure FOCUS (Parquet або CSV) з ADLS Gen2 в Bronze Delta-таблицю в міру їхнього
надходження. Шар "Bronze" зберігає файли байт у байт, що забезпечує можливість аудиту: коли фінвідділ запитає, чому
березнева цифра змінилася в травні, ви зможете показати точні вихідні записи.

### Проблема ключів з'єднання: пошук ClusterId в тегах Azure

Теги -- це ключі з'єднання. Azure виставляє рахунки на рівні ресурсів, тож рядок VM знає свою групу ресурсів і свої теги;
Databricks веде облік на рівні кластерів, тож рядок використання знає лише свій `cluster_id` та робочий простір. Жодна
зі сторін не посилається на іншу безпосередньо.

Azure Databricks додає теги `ClusterId` та `WorkspaceId` до кожної VM, яку він створює, тож пайплайн парсить
висококардинальну колонку `Tags` потоку Azure та витягує обидва значення. Два застереження з продакшену:
поширення тегів може відставати від створення ресурсу на кілька хвилин, а ресурси, створені поза "control plane"
Databricks (jump-сервери, власні сховища), таких тегів не мають. Передбачте кошик "unmatched" для обох випадків
замість того, щоб вдавати, ніби їх не буде.

### Розбіжність часових поясів і гранулярності

Експорти Azure FOCUS агрегуються та оновлюються щодня. Системні таблиці Databricks фіксують використання щогодини.
Наївне з'єднання множить рядки та завищує витрати, тож пайплайн обрізає часові мітки Databricks до денної
гранулярності `usage_date` перед об'єднанням. Ви втрачаєте внутрішньоденну деталізацію в об'єднаній таблиці, і це
прийнятний компроміс для MVP; погодинна деталізація залишається доступною в Silver-таблиці на стороні Databricks.

### Serverless проти класичних обчислень: як уникнути подвійного обліку

Плутанина в тому, як тарифікуються "serverless" та класичні обчислення, -- це логічна помилка, яка непомітно нищить
об'єднані таблиці витрат. За **класичні обчислення** Azure виставляє рахунок за VM безпосередньо, тож справжня
вартість кластера -- це DBU плюс змаплені інфраструктурні рядки. За **serverless-обчислень** Databricks сам керує VM
і включає їх у ціну DBU, тож та сама операція додавання рахує обладнання двічі.

ETL мусить розгалужуватися за джерелом білінгу: змапити та додати інфраструктурні витрати для класичних навантажень,
а для serverless вважати вартість DBU повною (сховище в обох моделях обліковується окремо). Помиліться в будь-який бік --
і ви відвантажите дашборди, які впевнено брешуть.

### Ретроспективні коригування білінгу та MERGE INTO

Гіперскейлери перераховують білінгові дані протягом усього місяця, тож вчорашні рядки -- не незмінні факти. Додавання
кожного оновлення створює дублікати; натомість пайплайн виконує "upsert":

```sql
MERGE INTO silver.unified_costs AS t
USING staging_focus_updates AS s
  ON  t.ChargePeriodStart = s.ChargePeriodStart
  AND t.ResourceId        = s.ResourceId
  AND t.SkuId             = s.SkuId
WHEN MATCHED THEN UPDATE SET *
WHEN NOT MATCHED THEN INSERT *
```

Delta Lake `MERGE INTO` зберігає ідемпотентність таблиці при перерахунках, а "time travel" зберігає аудиторський слід
того, що й коли змінилося.

## {{< color-text text="Від витрат до відповідальності" >}}

Об'єднана FOCUS-таблиця -- це інженерний артефакт. "Data Analyst" перетворює її на те, на що зреагує CFO.

### Переклад хмарної термінології на центри витрат

`db-cluster-101` нічого не означає для фінвідділу. Аналітик з'єднує об'єднану таблицю з даними HR та CMDB, щоб додати
`team_owner`, `cost_center` і `business_unit` до кожного рядка. Це збагачення відбувається в шарі "Gold", і саме воно
взагалі робить можливими "showback" та "chargeback".

### TCO-дашборди без подвійного обліку

Gold-таблиця живить дашборди, які показують сукупну вартість володіння за робочим простором, навантаженням і
бізнес-підрозділом, де витрати на VM-інфраструктуру, сховище та DBU залишаються окремими компонентами, які можна
складати. Саме розділення компонентів дозволяє команді побачити, що її "batch"-задачі працюють на "All-Purpose"
кластерах за приблизно подвійним тарифом DBU порівняно з "Jobs Compute", і відреагувати, не даючи нікому хибно
прочитати змішаний тариф (Рисунок 2).

{{< img src="/blog-images/post-11/tco-dashboard-unified-costs.png" alt="Рисунок 2. Об'єднаний дашборд витрат, що показує витрати Databricks і Azure в розрізі провайдера та сервісу." >}}

### Запити про витрати природною мовою з AI/BI Genie

Оскільки Gold-таблиця живе в Unity Catalog, її підключення до **AI/BI Genie** займає лічені хвилини. Бізнес-керівники
тоді ставлять запитання на кшталт "скільки коштував нам IoT-пайплайн минулого місяця, платформа та інфраструктура
разом?" звичайною мовою, не створюючи тікет на новий дашборд. Якість відповідей напряму залежить від якості кроку
збагачення вище, що є ввічливим способом сказати: Genie не врятує вас, якщо ви його пропустите.

### Нативна видимість витрат у Databricks Governance Hub

Хоча повну картину дає саме власний FOCUS-пайплайн, Databricks також має вбудований інструмент для роботи з витратами,
який варто згадати.
{{< color-link link_title="Governance Hub" path="https://docs.databricks.com/aws/en/admin/governance-hub/" target="_blank" >}}
 -- це централізований інтерфейс на рівні облікового запису, який з'являється в розділі **Governance** консолі акаунта.
Наразі він у статусі "Beta", і адміністратор акаунта має увімкнути його на сторінці **Previews**.

{{< img src="/blog-images/post-11/governance-hub-overview.png" alt="Рисунок 3. Governance Hub у консолі акаунта Databricks зі сторінками Data, AI та Cost у лівій бічній панелі." >}}

Сторінка **Cost** усередині Governance Hub -- найрелевантніша для FinOps-команди. Вона показує загальні витрати DBU та
витрати з початку місяця, середні денні витрати та графік тренду за 30 днів. Плитка **Top groups** показує
найдорожчі продукти та робочі простори з деталізацією до окремих ресурсів: кластерів, DLT-пайплайнів, задач і
SQL-"warehouses".

**Tagged spend** показує відсоток витрат, покритих тегами, з фільтром в один клік для пошуку нетегованих ресурсів
без атрибуції витрат. **Budgets** показує, скільки активних бюджетних порогів перевищено. Панель
**Cost recommendations** праворуч пропонує дії від самого Databricks (Рисунок 4).

{{< img src="/blog-images/post-11/governance-hub-cost-page.png" alt="Рисунок 4. Сторінка Cost у Governance Hub з метриками витрат, плиткою Top groups, відсотком тегованих витрат і панеллю рекомендацій." >}}

Одне важливе обмеження: усі суми в Governance Hub показані за **прайс-листом** ("list price"), і огляд охоплює лише
витрати DBU Databricks. Він не включає витрати на Azure VM, сховище чи мережу з Azure-частини рахунку.
Governance Hub -- корисне доповнення до об'єднаного FOCUS-пайплайна, а не його заміна.

## {{< color-text text="Ширша архітектура" >}}

У повноцінному корпоративному розгортанні цей MVP не існує у вакуумі. Microsoft у своєму
{{< color-link link_title="Cloud Adoption Framework for a unified data platform" path="https://learn.microsoft.com/en-us/azure/cloud-adoption-framework/data/architecture-azure-landing-zones-unify-data-platform" target="_blank" >}}
 розміщує його всередині **"Data Landing Zone" (DLZ)** -- середовища, що розміщує робочі простори Databricks і сховища,
під керуванням центральної **"Data Management Landing Zone" (DMLZ)**, яка надає загальнотенантні сервіси, як-от каталог даних.
Ідентичність -- ще один із таких сервісів, і про нього ми вже писали:
{{< color-link link_title="концепція проєкту RS-DataPlatform" path="/ua/blog/2025-12-23-rs-dataplatform-project-concept" target="_blank" >}}
 описує суверенне ядро ідентичності, до якого підключаються подібні платформи.

Ми свідомо зупиняємося тут. Мережевий дизайн, гавернанс і Terraform-архітектура цих "landing zones" заслуговують окремої
статті, і вона вже готується: у другій частині ми опублікуємо повний архітектурний план на основі відкритих Terraform-модулів
{{< color-link link_title="data management zone" path="https://github.com/PerfectThymeTech/terraform-azurerm-data-management-zone" target="_blank" >}}
 та
{{< color-link link_title="data landing zone" path="https://github.com/PerfectThymeTech/terraform-azurerm-data-landing-zone" target="_blank" >}}
 .

## {{< color-text text="Висновок" >}}

Описаний тут патерн не теоретичний. Опубліковане
{{< color-link link_title="FinOps case study" path="https://www.cloudnuro.ai/blog/databricks-finops-cost-reduction-case-study" target="_blank" >}}
 , що слідує тій самій дисципліні, звітує про скорочення простою "All-Purpose" обчислень на 70% та понад $2.1M річної
економії від реархітектури високочастотних задач -- економії, яка стала видимою лише після стандартизації даних про
витрати та появи видимості на рівні робочих навантажень.

Чесні обмеження: MVP працює з денною гранулярністю, потребує кошика незмаплених витрат для нетегованих ресурсів, а
мапінг Databricks-у-FOCUS -- це ваш код, який доведеться підтримувати, доки Databricks не випустить нативний експорт
FOCUS. Ніщо з цього не змінює висновку. Якщо ваша команда досі звіряє рахунки Azure і Databricks у таблиці, у вас уже є
все необхідне, щоб це припинити: підписка Azure, робочий простір Databricks і один запланований пайплайн. Побудуйте MVP,
налаштуйте на нього дашборд і витратьте час наступної місячної звірки на оптимізацію.

## {{< color-text text="FAQ" >}}

### Чи експортує Azure Databricks дані FOCUS нативно?

Лише зі сторони Azure. Azure Cost Management може за розкладом експортувати інфраструктурні витрати в нативному
форматі FOCUS в ADLS Gen2, але споживання DBU Databricks зберігається в системних таблицях (`system.billing.usage` та
`system.billing.list_prices`), які FOCUS не відповідають. Цю прогалину закриває запланована SQL-трансформація;
опублікований Databricks запит "FOCUS 1.3" -- гарна відправна точка.

### Як з'єднати білінгові дані Azure з використанням DBU Databricks?

Через теги. Azure Databricks додає теги `ClusterId` та `WorkspaceId` до кожної VM, яку він створює, тож пайплайн
парсить колонку `Tags` білінгового потоку Azure та зіставляє ці значення з `usage_metadata.cluster_id` у системних
таблицях Databricks. Залиште кошик незмаплених витрат для ресурсів, створених поза "control plane" Databricks, бо
вони таких тегів не мають.

### Чому витрати на serverless Databricks обліковуються двічі?

Бо ціна serverless DBU вже включає обчислення на VM. Якщо пайплайн зіставить інфраструктурні рядки Azure з
serverless-навантаженням і додасть їх до вартості DBU, те саме обладнання буде оплачено двічі. ETL мусить
розгалужуватися за джерелом білінгу: додавати інфраструктурні витрати лише для класичних обчислень, а для
serverless-навантажень вважати вартість DBU повною.

### Як обробляти ретроспективні перерахунки білінгу Azure?

Ідемпотентними "upsert"-операціями. Гіперскейлери перераховують білінгові дані протягом усього місяця, тож пайплайн
застосовує кожне оновлення через Delta Lake `MERGE INTO` з ключами `ChargePeriodStart`, `ResourceId` і `SkuId`.
Змаплені рядки оновлюються, нові вставляються, а дублікати ніколи не накопичуються. Delta "time travel" зберігає
аудиторський слід того, що й коли змінилося.
