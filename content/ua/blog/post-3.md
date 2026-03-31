---
title: Оптимізація зображень у фреймворку Hugo
url: '/ua/blog/2023-01-30-image-processing-for-hugo-framework'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-3/banner-hand-holding-coffee-cup-with-may-photograph-table.jpg'
summary_image: '/blog-images/post-3/post-3-short.jpg'
sharing_image: '/static-blog-images/post-3/post-3-share.jpg'
twitter_sharing_image: '/static-blog-images/post-3/post-3-twitter-share.jpg'
alt: 'ілюстрація з Обробка зображень для сайту Hugo'
keywords: ['Hugo', 'shortcodes', 'processing', 'images', 'optimisation', 'srcset']
blog_tags: ['Hugo','HTTP Archive']
date: 2023-01-30
---

## {{< color-text text="Вступ" >}}

Блог, який ви зараз читаєте, створений за допомогою фреймворку 
{{< color-link link_title="Hugo" path="https://gohugo.io/" >}}. 
Команда Rework-Space обрала цей фреймворк завдяки можливості легко та швидко створити багатомовний статичний вебсайт. 
Але сьогодні ми поговоримо про інше.

Розробляючи сайт компанії ми зіткнулися з проблемою обробки зображень, адже великі зображення повільно завантажуються, 
особливо на мобільних пристроях, що значно знижує доступність сторінки. Вручну оптимізовувати кожне зображення 
недоцільно, це вимагає додаткових зусиль і в момент розробки, і під час подальшого розширення блогу.

## {{< color-text text="Параметри до оптимізації" >}}

Для прикладу візьмемо домашню сторінку вебсайту (Зображення 1). Перед оптимізацією об’єм сторінки складав 13.7 MB, а 
швидкість завантаження 509 ms.

{{< img src="/blog-images/post-3/screenshot-network-tab-before.png" alt="Зображення 1. Скріншот вкладки network до оптимізації" css_class="img-scale-down">}}

Найбільше зображення мало об’єм 9.0 MB, що вплинуло на показники продуктивності сторінки. На Зображенні 2 результати 
звіту {{< color-link link_title="Lighthouse" path="https://developer.chrome.com/docs/lighthouse/" >}}, зробленого 
інструментом від команди Chrome Developers.

{{< img src="/blog-images/post-3/post-3-screenshot-lighthouse-report-before.png" alt="Зображення 2. Скріншот звіту Lighthouse до оптимізації" css_class="img-scale-down">}}

Згідно з даними від
{{< color-link link_title="HTTParchive" path="https://httparchive.org/reports/page-weight?start=2017_01_01&end=latest&view=list#bytesImg" >}}
станом на грудень 2022 року середній розмір вебсторінки становив приблизно 2,2 MB для комп’ютерів і 2 MB для мобільних 
сайтів, середній розмір зображень 997 KB і 851 KB відповідно.

## {{< color-text text="Код оптимізації" >}}

Є два сценарії використання коду оптимізації зображень:
- shortcodes
- partials

Для автоматизованої оптимізації зображень команда Rework-Space використала можливість створювати власні shortcodes у 
фреймворку Hugo. Основа була взята з блогу 
{{< color-link link_title="Clavin June" path="https://clavinjune.dev/en/blogs/image-processing-using-hugo/" >}} 
та доопрацьована нашою командою:

```
{{- $alt := .Get "alt" -}}
{{- $res := resources.Get (.Get "src") -}}
{{- $css_class := .Get "css_class" -}}

{{- $widths := slice 480 768 1200 1920 -}}
{{- $srcset := slice -}}

{{/* if image is less than 1920px - we add origin size to srcset too */}}
{{/* and to avoid empty srcset when image is too small (less than 480px) */}}
{{- if (lt $res.Width 1920) -}}
    {{- $wd := $res.Width -}}
    {{- $url_origin := $res.RelPermalink | safeURL -}}
    {{- $fmt := printf "%s %dw" $url_origin $wd -}}
    {{- $srcset = $srcset | append $fmt -}}
{{- end -}}

{{- range $widths -}}
    {{/* to avoid creating an image that is larger than the source */}}
    {{- if (le . $res.Width) -}}
        {{- $w := printf "%dx" . -}}
        {{- $url := ($res.Resize $w).RelPermalink | safeURL -}}
        {{- $fmt := printf "%s %dw" $url . -}}
        {{- $srcset = $srcset | append $fmt -}}
    {{- end -}}
{{- end -}}

{{- $set := delimit $srcset "," -}}

<figure>
    <img
        srcset="{{ $set }}"
        sizes="(max-width: 480px) 100vw, 100vw"
        src="{{ $res.RelPermalink }}"
        alt="{{ $alt }}"
        {{- if $css_class -}}
        class="{{ $css_class }}"
        {{- end -}}
    >
    <figcaption>{{ $alt }}</figcaption>
</figure>
```

Фолдер shortcodes із даним кодом може бути розміщений як в темі, так і в проєкті контенту:
1. /themes/theme-name/layouts/shortcodes/shortcode-name.html
2. /layouts/shortcodes/shortcode-name.html

Зображення мають бути розміщені у фолдері assets, в структурі проєкту теми або контенту, за вашим вибором:
1. /themes/theme-name/assets/folder-name/image-name.extension
2. /assets/folder-name/image-name.extension

Також цей код можна використати повторно у репозиторії теми для оптимізації зображень, інформація про які вказана 
параметрами в markdown файлах. Для цього необхідно скопіювати html в фолдер partials 
/themes/theme-name/layouts/partials/file-name.html та замінити перші три рядки коду на:

```
{{- $alt := resources.Get .Params.alt -}}
{{- $res := resources.Get .Params.src -}}
{{- $css_class := resources.Get .Params.css_class -}}
```

## {{< color-text text="Приклади використання" >}}

Ми обрали підхід згідно з яким контент і тема розділені на два незалежні репозиторії.

### {{< color-text text="Shortcode" >}}

Файл html знаходиться у проєкті теми, а зображення знаходиться у проєкті контенту:
```
/rs-theme/layouts/shortcodes/img-demo.html
/rs-content/assets/content-images/ict-security-short.jpg
```
Використання у markdown файлі з контентом:
```
{{</* img-demo src="/content-images/ict-security-short.jpg" alt="Businessman logging his tablet" */>}}
```

### {{< color-text text="Partials" >}}

Файл html знаходиться у проєкті теми за шляхом: 
```
/rs-theme/layouts/shortcodes/image-srcset.html
```

Використання у html файлі теми:
```
{{ partial "image-srcset.html" . }}
```

Інформація про зображення вказується параметрами в markdown файлі з контентом:
```
---
src: "/content-images/ict-security-short.jpg"
alt: "Businessman logging his tablet"
css_class: contain
---
```

### {{< color-text text="HTML результат" >}}

{{< img src="/blog-images/post-3/screenshot-srcset-result.png" alt="Зображення 3. Скріншот html коду" css_class="img-scale-down" >}}

На Зображенні 3 показано як буде інтерпретовано оптимізовані зображення в html готової сторінки. Обидва приклади 
будуть мати однаковий результат після генерації.

## {{< color-text text="Параметри після оптимізації" >}}

{{< img src="/blog-images/post-3/screenshot-network-tab-after.png" alt="Зображення 4. Скріншот вкладки network після оптимізації" css_class="img-scale-down" >}}

Після оптимізацією об’єм сторінки зменшився з 13.7 MB до 1.4 MB, а швидкість завантаження знизилася з 509 ms до 157 ms.

Для зображення розміром 9.0 MB створено кілька оптимізованих варіантів розміром від 179 kB до 17.5 kB, що значно 
покращило показники продуктивності сторінки. На Зображенні 5. результати звіту
{{< color-link link_title="Lighthouse" path="https://developer.chrome.com/docs/lighthouse/" >}}.

{{< img src="/blog-images/post-3/post-3-screenshot-lighthouse-report-after.png" alt="Зображення 5. Скріншот звіту Lighthouse після оптимізації" css_class="img-scale-down" >}}

## {{< color-text text="Підсумок" >}}

Автоматизована обробка зображень дозволяє без особливих зусиль забезпечити чудовий досвід користувача. Для 
веброзробників як ніколи важливо впроваджувати подібні технології.

Сподіваємося ця стаття була для вас корисною.
