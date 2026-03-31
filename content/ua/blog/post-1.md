---
title: Terraform DNS провайдер для керування FreeIPA
url: '/ua/blog/2022-07-01-terraform-provider-for-freeipa-dns-management'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-1/post-1-banner.webp'
summary_image: '/blog-images/post-1/post-1-short.jpg'
sharing_image: '/blog-images/post-1/post-1-short.jpg'
twitter_sharing_image: '/static-blog-images/post-1/post-1-twitter-share.jpg'
alt: 'ілюстрація з Terraform DNS провайдер для керування FreeIPA'
keywords: ['terraform', 'freeipa', 'dns', 'provider', 'authentication', 'authorization']
blog_tags: ['Terraform', 'FreeIPA', 'Rework-Space']
date: 2022-07-01
---

{{< color-link link_title="Terraform DNS провайдер" path="https://registry.terraform.io/providers/rework-space-com/freeipa/latest/docs" target="_blank" >}}
для керування FreeIPA розроблено та запропоновано командою Rework-Space Infrastructure.

Перевірено на FreeIPA версії 4.9.1

#### Вимоги:
- {{< color-link link_title="Terraform" path="https://www.terraform.io/" target="_blank" >}} 1.0.x
- Go 1.18 (для створення плагіна провайдера)

#### Ресурси:
- freeipa_dns_record
- freeipa_dns_zone
- freeipa_group
- freeipa_user

{{< color-link link_title="FreeIPA" path="https://www.freeipa.org/page/Leaflet" target="_blank" >}}
— це інтегроване рішення ідентифікації та автентифікації для мережевих 
середовищ Linux/UNIX. Сервер FreeIPA централізовано забезпечує автентифікацію, авторизацію та систему облікових записів, 
зберігаючи дані про користувачів, групи, хости та інші об’єкти, які є необхідними для керування аспектами безпеки 
мережі комп’ютерів.

Ми віддаємо перевагу використанню IaC для керування конфігурацією та інтеграцією FreeIPA. Команда Rework-Space пропонує 
Terraform DNS провайдер для керування FreeIPA на розгляд спільноти відповідно до ліцензії
{{< color-link link_title="GNU GENERAL PUBLIC LICENSE" path="https://github.com/rework-space-com/terraform-provider-freeipa/blob/main/LICENSE" target="_blank" >}}.
