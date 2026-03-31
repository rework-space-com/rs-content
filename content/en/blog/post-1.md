---
title: Terraform provider for FreeIPA DNS management
url: '/blog/2022-07-01-terraform-provider-for-freeipa-dns-management'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-1/post-1-banner.webp'
summary_image: '/blog-images/post-1/post-1-short.jpg'
sharing_image: '/blog-images/post-1/post-1-short.jpg'
twitter_sharing_image: '/static-blog-images/post-1/post-1-twitter-share.jpg'
alt: 'image from Terraform DNS провайдер для керування FreeIPA'
keywords: ['terraform', 'freeipa', 'dns', 'provider', 'authentication', 'authorization']
blog_tags: ['Terraform', 'FreeIPA', 'Rework-Space']
date: 2022-07-01
---

{{< color-link link_title="Terraform provider" path="https://registry.terraform.io/providers/rework-space-com/freeipa/latest/docs" target="_blank" >}}
for  FreeIPA DNS management is developed and proposed by Rework-Space Infrastructure team. 

Tested on FreeIPA version 4.9.1

#### Requirements:
- {{< color-link link_title="Terraform" path="https://www.terraform.io/" target="_blank" >}} 1.0.x
- Go 1.18 (to build the provider plugin)

#### Resources:
- freeipa_dns_record
- freeipa_dns_zone
- freeipa_group
- freeipa_user

{{< color-link link_title="FreeIPA" path="https://www.freeipa.org/page/Leaflet" target="_blank" >}}
is an integrated identity and authentication solution for Linux/UNIX 
networked environments. A FreeIPA server provides centralized authentication, authorization and account information by 
storing data about user, groups, hosts and other objects necessary to manage the security aspects of a network of 
computers.

We prefer use IaC to manage FreeIPA configuration and integration. Rework-Space team  propose Terraform provider  for  
FreeIPA DNS management is for community review according to
{{< color-link link_title="GNU GENERAL PUBLIC LICENSE" path="https://github.com/rework-space-com/terraform-provider-freeipa/blob/main/LICENSE" target="_blank" >}}.
