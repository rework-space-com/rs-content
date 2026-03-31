---
title: Стримування DDoS атак для краудфандингової ініціативи Canadian Bayraktar
url: '/ua/blog/DDoS-attack-mitigation-for-Canadian-Bayraktar-crowdfunding-initiative'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-2/post-2-banner.webp'
summary_image: '/blog-images/post-2/post-2-short.jpg'
sharing_image: '/blog-images/post-2/post-2-short.jpg'
twitter_sharing_image: '/static-blog-images/post-2/post-2-twitter-share.jpg'
alt: 'ілюстрація з Стримування DDoS атак для краудфандингової ініціативи Canadian Bayraktar'
keywords: ['DDoS', 'mitigation', 'google', 'jigsaw', 'hosting', 'crowdfunding']
blog_tags: ['Ukraine','UHelpUkraine', 'Cyber-security']
date: 2022-08-01
---

## {{< color-text text="Опис" >}}

Щоб вистояти в героїчній битві, захищаючи цивілізований світ від російської агресії, Україні терміново і вкрай 
необхідно більше обладнання для захисту неба. Одним із найпопулярніших і найефективніших безпілотників є турецький 
середньовисотний довготривалий БПЛА Baykar Bayraktar TBx.

Усвідомлюючи глобальний масштаб російської загрози, наші друзі в Литві, Україні та Польщі зібрали мільйони доларів по 
всьому світу через масові збори коштів, щоб забезпечити та провести закупівлі знаменитого безпілотника Bayraktar.

{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}} 
є зареєстрованою неприбутковою організацією (REG. #1000182616, Канада) завзятих українсько-канадських волонтерів. Ініціатива 
{{< color-link link_title="#BuyaBayraktar" path="https://www.facebook.com/hashtag/BuyaBayraktar/" target="_blank" >}} 
започаткована UHelpUkraine 20 липня 2022 року, привернула
{{< color-link link_title="чимало уваги" path="https://www.einnews.com/pr_news/582045288/canadian-non-profit-uhelpukraine-raising-funds-for-crucial-drone-needed-in-ongoing-fight-against-russia" target="_blank" >}}
як очікуваної, так і небажаної.


## {{< color-text text="Виявлення сигнатури атак та вжиті заходи" >}}

Команда Rework-Space була залучена до проєкту Canadian Bayraktar з 23 липня 2022 року для вирішення проблем із 
підозрілою активністю трафіку, що спричинило низьку доступність сайту
{{< color-link link_title="uhelpukraine.org" path="https://uhelpukraine.org/" target="_blank" >}}
для користувачів.

Для початку, ми провели деякі перевірки та дослідження хостингу, що дозволило нам налаштувати правила 
AWS Web Application Firewall (
{{< color-link link_title="WAF" path="https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html" target="_blank" >}}
). У результаті ми захистили веб-сайт
{{< color-link link_title="uhelpukraine.org" path="https://uhelpukraine.org/" target="_blank" >}}
від поширених експлойтів, а також 
детально дослідили трафік. Менш ніж за 2 дні було заблоковано 140 000 000 нелегітимних запитів.

{{< img src="/blog-images/post-2/post-2-image-1.png" alt="Зображення 1. Візуалізація запитів WAF" >}}

Використання CPU значно покращилося.

{{< img src="/blog-images/post-2/post-2-image-2.png" alt="Зображення 2. Візуалізація завантаження CPU інстанції EC2">}}

Однак ми виявили, що були короткі проміжки часу, коли правила WAF не були ефективними, а завантаження CPU залишалося 
високим (Зображення 2). Загалом AWS
{{< color-link link_title="пропонує" path="https://docs.aws.amazon.com/waf/latest/developerguide/ddos-responding.html#ddos-responding-manual" target="_blank" >}}
два види відповіді на DDoS-атаки (7-рівнева модель OSI), а саме:
1. Використання власних засобів стримування;
2. Звернення до служби підтримки – якщо ви є клієнтом Shield Advanced.

Перший варіант неприйнятний, адже у даному випадку ми мали більше ніж 60 мільйонів заблокованих запитів із географічно 
розподіленим походженням. Оскільки плата стягується за кожен мільйон заблокованих запитів, щоденний бюджет нашого 
клієнта становив близько $50. Другий варіант хороший, але потребує місячної підписики та по вартості стартує від $3000. 
Очевидно, що це не буде найкращим рішенням для нашого клієнта, некомерційної організації з дуже обмеженим бюджетом.

Отже, ми провели дослідження проєктів і компаній, які підтримують ініціативи відкритого суспільства та допомагають 
людям почуватися безпечніше використовуючи інформаційні технології. В результаті ми обрали
{{< color-link link_title="Jigsaw" path="https://jigsaw.google.com/" target="_blank" >}}, підрозділ Google, який досліджує загрози відкритим інформаційним системам і 
створює технології, що дозволяють застосовувати масштабовані рішення.

Ви можете побачити результат на Зображенні 2, де представлено нове рішення, реалізоване 25 липня 2022 року. Варто 
зазначити, що для некомерційних організацій та інших відповідних клієнтів
{{< color-link link_title="Jigsaw" path="https://jigsaw.google.com/" target="_blank" >}}
пропонує кеш CDN (інфраструктура Google),
{{< color-link link_title="reCAPTCHA" path="https://www.google.com/recaptcha/about" target="_blank" >}}
(потрібно ввімкнути явно) та візуалізацію всіх показників на одному дашборді цілком безкоштовно,
{{< color-link link_title="як базову опцію" path="https://www.pcmag.com/opinions/inside-project-shield-jigsaws-anti-ddos-machine" target="_blank" >}}.

{{< img src="/blog-images/post-2/post-2-image-3.png" alt="Зображення З. Динаміка витрат uhelpukraine.org">}}

У підсумку, ми скоротили витрати на стороні AWS. Тепер команда
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}}
оплачує лише послуги хостингу AWS і зосереджується на волонтерській діяльності. Команда Rework-Space продовжує надавати 
послуги з кібербезпеки нашому новому партнеру
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}}
і буде робити це до тих пір, поки наші люди не переможуть.

Усі матеріали публікуються за згодою команди 
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}}.
