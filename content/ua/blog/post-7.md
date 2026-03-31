---
title: 'Розвідка на основі штучного інтелекту для надзвичайних ситуацій: Rework-Space на 8-му CASSINI Hackathon - EU Space for Security and Defence'
url: '/ua/blog/2024-12-01-rework-space-at-the-8th-cassini-hackathon'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-7/cassini-defence-2024.png'
summary_image: '/blog-images/post-7/post-7-short-cassini-2024.png'
sharing_image: '/blog-images/post-7/post-7-short-cassini-2024.png'
twitter_sharing_image: '/blog-images/post-7/post-7-short-cassini-2024.png'
alt: 'Picture for article Rework-Space at the 8th CASSINI Hackathon'
keywords: ['8th CASSINI Hackathon', 'International Development', 'Innovators', 'Space for Defence and Security',
           'Copernicus', 'TNTU']
date: 2024-12-01
blog_tags: ['CASSINI', 'QueryOptima', 'Rework-Space', 'Ukraine']
draft: false
---

З 22 по 24 листопада 2024 року новатори з усієї Європи зібралися на
{{< color-link link_title="EU Space for Defence and Security CASSINI Hackathon" path="https://taikai.network/en/cassinihackathons/hackathons/euspace-defence-security/overview" target="_blank" >}} –
ініціативу, започатковану Європейським Союзом для використання космічних технологій у рішеннях нового покоління для 
оборони та безпеки. Ця важлива подія стала викликом для команд інноваторів, підприємців, інженерів даних та знань, щоб 
створити масштабовані застосунки на основі супутникових даних Copernicus та космічної інфраструктури ЄС.

## {{< color-text text="DroneSight: прискорення ситуаційної обізнаності за допомогою ШІ та інтегрованих дата-пайплайнів" >}}

У 
{{< color-link link_title="Rework-Space" path="https://rework-space.com" target="_blank" >}},
ми віримо в трансформаційний потенціал ШІ, дата-інженерії та аналітики реального часу для захисту критичної 
інфраструктури й реагування на надзвичайні ситуації. Реалізуючи цю візію, наша команда розробила
{{< color-link link_title="DroneSight" path="https://taikai.network/cassinihackathons/hackathons/euspace-defence-security/projects/cm2yk8hgq03y9bsslwti7fzjd/idea" target="_blank" >}} –
платформу, що використовує інноваційні рішення в галузі інженерії даних та засобів штучного інтелекту для виявлення 
загроз та оцінки ризиків за допомогою морських та річкових дронів.

DroneSight агрегує супутникові знімки (Copernicus) із даними, зібраними дронами, щоб забезпечити оперативний моніторинг 
для підвищення безпеки операцій. Ця інтеграція здійснюється через наш власний
{{< color-link link_title="QueryOptima" path="https://rework-space.com/blog/2024-10-04-queryoptima-revolutionizing-data-optimization-and-bi-integration" target="_blank" >}}
 рушій злиття даних, який приймає дані з
{{< color-link link_title="OpenEO APIs" path="https://dataspace.copernicus.eu/analyse/openeo" target="_blank" >}}
 та різноманітних відкритих геопросторових репозиторіїв, нормалізує та збагачує їх для отримання практичної інформації.

{{< img src="/blog-images/post-7/system-design.png" alt="Проєктування системи DroneSight" >}}

Ми використовуємо супутникові знімки Sentinel-1 SAR, Sentinel-2 та дані з дронів із
{{< color-link link_title="YOLOv11" path="https://github.com/ultralytics/ultralytics" target="_blank" >}}
 моделлю і фреймворком MarinexT для виявлення суден й ідентифікації загроз, таких як морське сміття, нафтові плями тощо. 
Також ми використовуємо
{{< color-link link_title="MyShipTracking" path="https://www.myshiptracking.com" target="_blank" >}}
 AIS для отримання даних про судна в реальному часі. Далі ми застосовуємо наш інноваційний прозрамний засіб QueryOptima 
для нормалізації та структурування даних, а також Blue Brain Nexus для їхнього впровадження у граф знань. Оброблені 
дані використовуються для оцінки ризиків, визначення безпечних маршрутів і подальшої візуалізації з метою забезпечення 
своєчасного та обґрунтованого прийняття рішень.

Наш граф обробки даних підтримує:
* Інтеграцію та гармонізацію з багатьох джерел.
* Подієво-орієнтовану аналітику на базі ШІ для сповіщень у реальному часі.
* Оптимізовану хмарно-нативну архітектуру для масштабованості.

Це рішення позиціонує DroneSight як цінний інструмент для підтримки європейських систем реагування на надзвичайні 
ситуації та екологічного моніторингу.

{{< img src="/blog-images/post-7/google-meet-screenshot-from-2024-11-23.png" alt="Google meet команди DroneSight" >}}

## {{< color-text text="Національне визнання та стратегічний вплив" >}}

{{< color-link link_title="DroneSight" path="https://taikai.network/cassinihackathons/hackathons/euspace-defence-security/projects/cm2yk8hgq03y9bsslwti7fzjd/idea" target="_blank" >}}
був відзначений на національному етапі хакатону, здобувши 3 місце в українському відборі, організованому POLE Product 
Design Center та місцевим інноваційним партнером ГО
{{< color-link link_title="Metalab" path="https://metalab.space/news/ukrainska-komanda-signal-hawk-sered-peremozhtsiv-ievropeiskogo-kosmichnogo-khakatonu-cassini" target="_blank" >}}.
Це досягнення підкреслює здатність нашої команди швидко прототипувати та впроваджувати геопросторові рішення на основі 
ШІ, які відповідають стратегічним пріоритетам ЄС.

{{< img src="/blog-images/post-7/DroneSight-team.jpg" alt="Команда DroneSight в ТНТУ" >}}

## {{< color-text text="Потужність інноваційної екосистеми України" >}}

Наша команда об'єднує досвід, знання й ініціативу працівників, студентів та випускників 
{{< color-link link_title="Тернопільського національного університету імені Івана Пулюя" path="https://tntu.edu.ua" target="_blank" >}}.
Щоб успішно вирішити складну та амбітну проблему, разом працювали представники кафедр програмної інженерії, 
комп'ютерних наук, комп'ютерних систем та мереж, фізики та кібербезпеки ТНТУ. Активна участь у вирішенні актуальних 
проблем глобального та регіонального рівня є частиною стратегії розвитку 
{{< color-link link_title="факультету комп'ютерно-інформаційних технологій і програмної інженерії ТНТУ" path="https://fis.tntu.edu.ua/" target="_blank" >}}
й університету загалом.  

Наш шлях у CASSINI Hackathon став можливим завдяки
{{< color-link link_title="Metalab" path="https://metalab.space/news/ukrainska-komanda-signal-hawk-sered-peremozhtsiv-ievropeiskogo-kosmichnogo-khakatonu-cassini" target="_blank" >}},
прогресивній урбаністичній лабораторії інновацій, розташованій в Івано-Франківську. Відома своєю підтримкою освітніх, 
соціальних та технологічних проєктів, Metalab створила динамічне середовище для генерації ідей та швидкої розробки. 
Їхня місія – сприяти сталим інноваціям – повністю відповідає нашому баченню: застосовувати найсучасніші ШІ та 
дата-інфраструктуру для вирішення складних реальних проблем.
