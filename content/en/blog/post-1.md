---
title: Terraform provider for FreeIPA DNS management
url: '/blog/2022-07-01-terraform-provider-for-freeipa-dns-management'
omit_header_text: false
featured_image: '/blog-images/post-1-banner.jpg'
summary_image: '/blog-images/post-1-short.jpg'
date_prefix: 'Published #'
date: 2022-07-01
read_more_copy: 'Read more ->'

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