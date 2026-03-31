---
title: DDoS attack mitigation for Canadian Bayraktar crowdfunding initiative
url: '/blog/DDoS-attack-mitigation-for-Canadian-Bayraktar-crowdfunding-initiative'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-2/post-2-banner.webp'
summary_image: '/blog-images/post-2/post-2-short.jpg'
sharing_image: '/blog-images/post-2/post-2-short.jpg'
twitter_sharing_image: '/static-blog-images/post-2/post-2-twitter-share.jpg'
alt: 'image from DDoS attack mitigation for Canadian Bayraktar crowdfunding initiative'
keywords: ['DDoS', 'mitigation', 'google', 'jigsaw', 'hosting', 'crowdfunding']
blog_tags: ['Ukraine','UHelpUkraine', 'Cyber-security']
date: 2022-08-01
---

## {{< color-text text="Introduction" >}}

To endure in a heroic battle defending the civilized world from russian aggression, Ukraine urgently and absolutely 
needs more equipment to protect the sky. One of the most popular and efficient unmanned aerial vehicles is Turkish 
medium-altitude long-endurance Baykar Bayraktar TBx UAV.

Aware of the global scale of russian threat, our friends in Lithuania, Ukraine, and Poland have raised millions of 
dollars across the globe through grassroots fundraisers to ensure and conduct purchases of the famous Bayraktar drone.

{{< color-link path="https://uhelpukraine.org/" link_title="UHelpUkraine" target="_blank" >}} 
is a registered non-profit organization (REG. #1000182616, Canada) of proud Ukrainian-Canadian volunteers. An initiative
{{< color-link link_title="#BuyaBayraktar" path="https://www.facebook.com/hashtag/BuyaBayraktar/" target="_blank" >}}
was started by UHelpUkraine on Jul 20, 2022 and attracted
{{< color-link link_title="a lot of attention" path="https://www.einnews.com/pr_news/582045288/canadian-non-profit-uhelpukraine-raising-funds-for-crucial-drone-needed-in-ongoing-fight-against-russia" target="_blank" >}}, 
both anticipated and unwanted one.

## {{< color-text text="Attack signature detection and response" >}}

Rework-Space team was involved in the project since Jul 23, 2022 to resolve issues with suspicious traffic activity 
that caused poor performance of
{{< color-link link_title="uhelpukraine.org" path="https://uhelpukraine.org/" target="_blank" >}}
site.

First, we have done some hosting audits and research which enabled us to configure AWS Web Application Firewall
({{< color-link link_title="WAF" path="https://docs.aws.amazon.com/waf/latest/developerguide/waf-chapter.html" target="_blank" >}}).
As a result, we have protected the web application
{{< color-link link_title="uhelpukraine.org" path="https://uhelpukraine.org/" target="_blank" >}}
from common web exploits and also researched the traffic in detail. In less than 2 days, 140 000 000 illegitimate 
requests were blocked.

{{< img src="/blog-images/post-2/post-2-image-1.png" alt="Figure 1. WAF requests visualization">}}

CPU utilization improved considerably.

{{< img src="/blog-images/post-2/post-2-image-2.png" alt="Figure 2. EC2 instance CPU utilization">}}

However, we discovered that there were short time intervals when WAF rules were not efficient and CPU utilization was 
still high (Figure 2). Generally, AWS
{{< color-link link_title="proposes" path="https://docs.aws.amazon.com/waf/latest/developerguide/ddos-responding.html#ddos-responding-manual" target="_blank" >}}
two kinds of  response to DDoS attacks (the 7-layer OSI Model), namely:
1. Provide your own mitigations;
2. Contact support – If you're a Shield Advanced customer.

Option 1 is not acceptable in this case of more than 60M blocked requests with geographically distributed origins. 
The daily budget of our customer was about $50, and you pay for each 1M of blocked requests. Option 2 is good but 
starts from $3000 for a monthly subscription. Obviously, it would not be the best solution for our customer, a 
non-profit organization with a very limited budget.

So, we have conducted research on projects and companies that support open society initiatives and help people feel 
safe when using information technologies. As a result, we have chosen
{{< color-link link_title="Jigsaw" path="https://jigsaw.google.com/" target="_blank" >}}
, which is a unit within Google that explores threats to open information systems and creates technology that enables 
scalable solutions.

You can see the result in Figure 2, presenting the new solution implemented on Jul 25, 2022. Noteworthy, for non-profit 
organizations and other eligible clients
{{< color-link link_title="Jigsaw" path="https://jigsaw.google.com/" target="_blank" >}}
proposes CDN cache (Google infrastructure),
{{< color-link link_title="reCAPTCHA" path="https://www.google.com/recaptcha/about" target="_blank" >}}
(should be enabled explicitly), metrics in a single dashboard at no cost at all,
{{< color-link link_title="as a basic option" path="https://www.pcmag.com/opinions/inside-project-shield-jigsaws-anti-ddos-machine" target="_blank" >}}.

{{< img src="/blog-images/post-2/post-2-image-3.png" alt="Figure 3. Cost dynamics for uhelpukraine.org">}}

In conclusion, we got rid of expenses on the AWS side. Now
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}}
team pays just for AWS hosting services and focuses on volunteer activities. Rework-Space team continues to provide 
cyber-security services to our new partner
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}}
and will do so till our people prevails.

All materials are published with the consent of
{{< color-link link_title="UHelpUkraine" path="https://uhelpukraine.org/" target="_blank" >}} 
team.
