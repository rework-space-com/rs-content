---
title: Image processing for Hugo framework
url: '/blog/2023-01-30-image-processing-for-hugo-framework'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-3/banner-hand-holding-coffee-cup-with-may-photograph-table.jpg'
summary_image: '/blog-images/post-3/post-3-short.jpg'
sharing_image: '/static-blog-images/post-3/post-3-share.jpg'
twitter_sharing_image: '/static-blog-images/post-3/post-3-twitter-share.jpg'
alt: 'Picture for Image processing for Hugo framework'
keywords: ['Hugo', 'shortcodes', 'processing', 'images', 'optimisation', 'srcset']
blog_tags: ['Hugo','HTTP Archive']
date: 2023-01-30
---

## {{< color-text text="Introduction" >}}

The blog you are reading is built using the 
{{< color-link link_title="Hugo" path="https://gohugo.io/" >}} framework.
The Rework-Space team chose this framework because of the ability to easily and quickly create a multilingual static 
website. But today we are going to talk about something else.

While developing the company's website, we encountered a problem with image processing, because large images load 
slowly, especially on mobile devices, which significantly reduces the accessibility of the page. It is impractical 
to optimize each image manually, and it requires additional efforts both at the time of development and during the further 
expansion of the blog.

## {{< color-text text="Parameters before optimization" >}}

For example, let's take the home page of a website (Figure 1). Before optimization, the page size was 13.7 MB and 
the loading speed was 509 ms.

{{< img src="/blog-images/post-3/screenshot-network-tab-before.png" alt="Figure 1. Network tab screenshot before optimization" css_class="img-scale-down">}}

The largest image was 9.0 MB, which affected the performance of the page. Figure 2 shows the results of the 
{{< color-link link_title="Lighthouse" path="https://developer.chrome.com/docs/lighthouse/" >}} report, produced by 
a tool from the Chrome Developers team.

{{< img src="/blog-images/post-3/post-3-screenshot-lighthouse-report-before.png" alt="Figure 2. Lighthouse report screenshot before optimization" css_class="img-scale-down">}}

According to
{{< color-link link_title="HTTParchive" path="https://httparchive.org/reports/page-weight?start=2017_01_01&end=latest&view=list#bytesImg" >}}
in September 2022 the average web-page size was around 2.2 MB for desktop sites and 2 MB for mobile sites.

## {{< color-text text="Optimization code" >}}

There are two ways for using image processing code:
- shortcodes
- partials

For automation image optimization, the Rework-Space team used the ability to create custom shortcodes in the Hugo 
framework. The basis was taken from 
{{< color-link link_title="Clavin June's" path="https://clavinjune.dev/en/blogs/image-processing-using-hugo/" >}}
blog and extended by our team:

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

The shortcodes folder with this code can be placed both in the theme and in the content project:
1. /themes/theme-name/layouts/shortcodes/shortcode-name.html
2. /layouts/shortcodes/shortcode-name.html

Images should be placed in the assets folder, in the theme or content project structure, of your choice:
1. /themes/theme-name/assets/folder-name/image-name.extension
2. /assets/folder-name/image-name.extension

Also, this code can be reused in the theme repository to optimize images, information about which is specified by 
parameters in markdown files. To do this, you need to copy the html to the folder partials 
/themes/theme-name/layouts/partials/file-name.html and replace the first three lines of code with:

```
{{- $alt := resources.Get .Params.alt -}}
{{- $res := resources.Get .Params.src -}}
{{- $css_class := resources.Get .Params.css_class -}}
```

## {{< color-text text="Usage examples" >}}

We chose the approach according to which the content and theme are separated into two independent repositories.

### {{< color-text text="Shortcode" >}}

The html file is in the theme project, and the image is in the content project:
```
/rs-theme/layouts/shortcodes/img-demo.html
/rs-content/assets/content-images/ict-security-short.jpg
```

Usage in markdown file with content:
```
{{</* img-demo src="/content-images/ict-security-short.jpg" alt="Businessman logging his tablet" */>}}
```

### {{< color-text text="Partials" >}}

The html file is located in the theme project at the following path:
```
/rs-theme/layouts/shortcodes/image-srcset.html
```

Usage in the html file of the theme:
```
{{ partial "image-srcset.html" . }}
```

Information about the image is specified by parameters in the markdown file with the content:
```
---
src: "/content-images/ict-security-short.jpg"
alt: "Businessman logging his tablet"
css_class: contain
---
```

### {{< color-text text="HTML result" >}}

{{< img src="/blog-images/post-3/screenshot-srcset-result.png" alt="Figure 3. HTML code screenshot" css_class="img-scale-down" >}}

Figure 3 shows how optimized images will be interpreted in the html of the final page. Both examples will have the same 
output after build.

## {{< color-text text="Parameters after optimization" >}}

{{< img src="/blog-images/post-3/screenshot-network-tab-after.png" alt="Figure 4. Network tab screenshot after optimization" css_class="img-scale-down" >}}

After optimization, the page size decreased from 13.7 MB to 1.4 MB, and the loading speed decreased from 509 ms to 157 ms.

For a 9.0 MB image, several optimized options were created from 179 kB to 17.5 kB, which significantly improved 
the performance of the page. Figure 5 shows the results of the
{{< color-link link_title="Lighthouse" path="https://developer.chrome.com/docs/lighthouse/" >}} report.

{{< img src="/blog-images/post-3/post-3-screenshot-lighthouse-report-after.png" alt="Figure 5. Lighthouse report screenshot after optimization" css_class="img-scale-down" >}}

## {{< color-text text="Summary" >}}

Automation image processing allows to provide a great user experience without big efforts. It is more important than ever 
for web developers to implement such optimization.

We hope the article content has been useful for you.
