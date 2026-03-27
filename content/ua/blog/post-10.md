---
title: 'Keycloak Fine-Grained Admin Permissions (FGAP) V2: Fine-Grained Access & Safe Impersonation'
url: '/ua/blog/2026-03-27-keycloak-fine-grained-admin-permissions-v2'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-10/cyber-security-concept.jpg'
summary_image: '/blog-images/post-10/keyboard-enlarged-by-loupe-short.jpg'
sharing_image: '/static-blog-images/post-10/keyboard-enlarged-by-loupe-share.jpg'
twitter_sharing_image: '/static-blog-images/post-10/keyboard-enlarged-by-loupe-twitter-share.jpg'
alt: 'Банер до статті Keycloak Fine-Grained Admin Permissions (FGAP) V2: Fine-Grained Access & Safe Impersonation'
keywords: [ 'Keycloak', 'permissions', 'user impersonation', 'access policies', 'DevOps', 'authentication', 'security' ]
date: 2026-03-27
blogs: [ 'Keycloak', 'Terraform', 'DevOps' ]
draft: false
---

## {{< color-text text="Проблема" >}}

Базові дозволи Keycloak надаються за допомогою ролей, присутніх в системних клієнтах. У більшості випадків
використовується клієнт **realm-management**. Однак, наявні дозволи є негнучкими та надають забагато доступу. Ви можете
надати користувачу право керувати усіма користувачами в realm або не керувати ніким.

У цьому випадку є два можливі сценарії роботи. У першому, лише обмежена кількість розробників має доступ до realm, що
робить їх відповідальними за його управління. Або надати доступ кожному розробнику, дозволяючи їм бачити дані ваших
клієнтів. Жоден із них не можна вважати оптимальним підходом. Було б добре мати спосіб надавати доступ лише до обмеженої
кількості користувачів, приховуючи дані інших. Саме це, і не тільки, дозволяють робити 
**Fine-Grained Admin Permissions**.

## {{< color-text text="Fine-Grained Permissions" >}}

Згадані **Fine-Grained Admin Permissions (FGAP)** базуються на 
{{< color-link link_title="Fine-Grained permissions" path="https://www.keycloak.org/2025/05/fgap-kc-26-2" target="_blank" >}},
що з'явилися в Keycloak 26.2.0. Ця функція дозволяє винести автентифікацію користувача з додатку та
делегувати її для Keycloak. Перед налаштуванням Fine-Grained Admin Permissions, розглянемо ключові компоненти, що
використовуються в обох функціях.

**Сервер ресурсу (Resource server)** -- це застосунок, який містить доступні ресурси. **Ресурс (Resource)** -- це все,
що ви хочете захистити. Ресурсом може бути файл в об'єктному сховищі, запис із бази даних, або API. Кожен ресурс може
мати набір необов'язкових **сфер застосування (Scopes)**, які визначають набір дозволених дій. Доступ може бути наданий
залежно від умов, визначених у **політиках (Policies)**.

Політики не прикріплюються до ресурсів безпосередньо, натомість **дозволи (Permissions)** є з'єднувальною ланкою, що їх
поєднує. Цей підхід робить систему гнучкою, дозволяючи комбінувати політику з різними ресурсами. Вони можуть
застосовуватися до конкретної сфери застосування (scope) або до самого ресурсу, надаючи повний контроль над ним.

{{< img src="/blog-images/post-10/solving-the-overexposure-problem-diagram.png" alt="Рисунок 1. Global Admin Access vs. Fine-Grained Permissions: Вирішення проблеми надмірного доступу." >}}

### Конфігурація Terraform

Функція
{{< color-link link_title="Fine-Grained permissions" path="https://www.keycloak.org/2025/05/fgap-kc-26-2" target="_blank" >}}
 повністю підтримується в офіційному 
{{< color-link link_title="Terraform Provider v5.7.0" path="https://github.com/keycloak/terraform-provider-keycloak/tree/v5.7.0" target="_blank" >}}.
Він дозволяє версіонувати конфігурацію та миттєво застосовувати її. Інтеграція із Terraform провайдером значно 
розширює функціонал Keycloak та робить його повноцінною заміною для OPA policy agent.

## {{< color-text text="Admin Permissions" >}}

Тепер, коли ми ознайомилися з Fine-Grained Permissions, розгляньмо Fine-Grained Admin Permissions. Очевидно, що сервер
ресурсу (resource server) у цьому сценарії -- це сам Keycloak. Він має набір попередньо визначених ресурсів з їхніми
scopes: Users, Groups, Clients та Roles. Ми можемо створювати політики та дозволи для керування доступом до даних
ресурсів.

### Реальний приклад

Розглянемо реальний приклад механізму _імперсонації_. Імперсонація у Keycloak надає доступ до облікового запису без
облікових даних користувача. Вона корисна для тестування та відтворення помилок. З базовими дозволами неможливо обмежити
вибірку користувачів доступних для імперсонації, що може призвести до зловживання правами у застосунках.

Найкращий варіант -- використовувати Fine-Grained Admin Permissions для надання гранулярного доступу. Для цього потрібно
лише два кроки. Створити політику, щоб визначити: «Хто може імперсонувати?» І створити дозвіл, який дозволяє
імперсонацію лише для обмеженого набору користувачів, група _test-users_ у цьому прикладі. Перш за все, необхідно
увімкнути Fine-Grained Admin Permissions:

{{< img src="/blog-images/post-10/enabling-fine-grained-admin-permissions-v2.png" alt="Рисунок 2. Увімкнення Fine-Grained Admin Permissions V2 у налаштуваннях Keycloak Realm." >}}

У наведеному нижче прикладі використовується **Group Policy**, щоб дозволити доступ лише користувачам, які є членами
_dev-group_.

{{< img src="/blog-images/post-10/group-policy-example.png" alt="Рисунок 3. Створення Group Policy для надання доступу конкретно членам 'dev-group'." >}}

Після створення політики, настав час створити Group Permission зі Scope _impersonate-members_.

{{< img src="/blog-images/post-10/group-permission-example.png" alt="Рисунок 4. Налаштування дозволу 'impersonate-members'." >}}

Тепер розробники можуть імперсонувати лише обмежену вибірку тестових облікових записів.

## {{< color-text text="Виклики" >}}

Fine-Grained Admin Permissions -- це нова функція, яка значно розширює сфери застосування Keycloak. Через свою новизну,
функція має кілька підводних каменів, що створюють нові виклики. У цьому розділі ми розглянемо два з них.

### Обмежена підтримка Terraform

Попри те, що функція FGAP базується на Fine-Grained permissions, вона реалізує власний окремий API. Це розділення 
погано впливає на підтримку Terraform провайдером. Наразі він дозволяє лише увімкнути функцію на рівні realm. Дозволи 
та політики можна створювати лише через UI або API-виклики. У офіційному репозиторії, є створене
{{< color-link link_title="issue" path="https://github.com/keycloak/terraform-provider-keycloak/issues/1187" target="_blank" >}}
щодо цієї проблеми. Однак, на поточний момент, остаточна дата реалізації залишається невідомою. 

### Помилки

Як і будь-яка нова функція, вона може містити помилки, що не були виявлені під час етапу тестування. Наприклад,
Keycloak 26.2.0 має помилку, яка ігнорує Fine-Grained Group Permissions під час створення нового користувача. Це
дозволяє додати нового користувача до груп з обмеженим доступом.

Розглянута помилка згадана у
{{< color-link link_title="обговоренні" path="https://github.com/keycloak/keycloak/discussions/37133#discussioncomment-15298242" target="_blank" >}}
з репозиторію Keycloak (детальна конфігурація FGAP описана в обговоренні). На Рисунку 5 зображена ієрархія груп
тестового realm. У наведеній конфігурації, користувачі з GroupA мають найнижчі права. Вони не можуть додавати нові
облікові записи до дочірніх груп та виконувати будь-які зміни над ними.

{{< img src="/blog-images/post-10/group-hierarchy.png" alt="Рисунок 5. Ієрархія груп тестового realm." >}}

Після авторизації з обліковим записом користувача GroupA, існує можливість обійти FGAP та додати нового користувача до
GroupC. Дія може бути виконана лише під час виконання запиту на створення нового облікового запису у системі 
(Рисунок 6).

{{< img src="/blog-images/post-10/adding-group-with-user-creation.png" alt="Рисунок 6. Додавання групи під час створення користувача." >}}

На щастя, Keycloak не дозволяє встановити пароль для нового користувача одразу у меню створення. Активація користувача
вимагає створення тимчасового пароля або надсилання повідомлення «Reset password». Дані дії відбуваються уже після
створення і є забороненими конфігурацією FGAP для користувача GroupA. Спроби виконання зазначених запитів закінчуються
помилкою 403.

{{< img src="/blog-images/post-10/403-error.png" alt="Рисунок 7. Спроба встановити пароль для нового користувача." >}}

## {{< color-text text="Висновок" >}}

Функція Keycloak Fine-Grained Admin Permissions вирішує давню проблему: як делегувати адміністративний доступ без
надмірного розкриття конфіденційних даних. Замість підходу «все або нічого» з базовими дозволами, доступ тепер можна
обмежувати за допомогою гнучкого налаштування політик FGAP. Це дозволяє безпечно делегувати відповідальність, захищаючи
при цьому дані клієнтів. З підтримкою Terraform, FGAP незабаром перетворить Keycloak на потужну платформу для контролю
доступу, відкриваючи нові сфери застосування додатка.
