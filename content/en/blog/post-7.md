---
title: "Advancing AI-Driven Emergency Intelligence: Rework-Space at the 8th CASSINI Hackathon - EU Space for Security and Defence"
url: '/blog/2024-12-01-rework-space-at-the-8th-cassini-hackathon'
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

From November 22-24, 2024, innovators across Europe came together for the
{{< color-link link_title="EU Space for Defence and Security CASSINI Hackathon" path="https://taikai.network/en/cassinihackathons/hackathons/euspace-defence-security/overview" target="_blank" >}} –
an initiative powered by the European Union to leverage space technologies for next-generation defence and security 
solutions. This high-impact event challenged teams of entrepreneurs, engineers, and data scientists to create scalable 
applications using Copernicus satellite data and EU space infrastructure.

## {{< color-text text="DroneSight: accelerating situational awareness with AI and integrated data pipelines" >}}

At 
{{< color-link link_title="Rework-Space" path="https://rework-space.com" target="_blank" >}},
we believe in the transformative potential of AI, data engineering, and real-time analytics in critical 
infrastructure and emergency response. Implementing this vision, our team developed
{{< color-link link_title="DroneSight" path="https://taikai.network/cassinihackathons/hackathons/euspace-defence-security/projects/cm2yk8hgq03y9bsslwti7fzjd/idea" target="_blank" >}} –
a precision intelligence platform for drone-based hazard detection and risk assessment in maritime and riverine 
environments.

DroneSight fuses satellite imaging (Copernicus) with drone-collected data to deliver near-real-time monitoring for 
enhanced safety operations. This integration is enabled through our proprietary
{{< color-link link_title="QueryOptima" path="https://rework-space.com/blog/2024-10-04-queryoptima-revolutionizing-data-optimization-and-bi-integration" target="_blank" >}}
 data fusion engine, which ingests data from
{{< color-link link_title="OpenEO APIs" path="https://dataspace.copernicus.eu/analyse/openeo" target="_blank" >}}
 and various open-source geospatial repositories, normalizing and enriching it into actionable intelligence.

{{< img src="/blog-images/post-7/system-design.png" alt="DroneSight system design" >}}

We use Sentinel-1 SAR, Sentinel-2, and drone data with the
{{< color-link link_title="YOLOv11" path="https://github.com/ultralytics/ultralytics" target="_blank" >}}
 model and MarinexT framework to detect ships and identify hazards like marine debris and oil spills, etc. Also, we use
{{< color-link link_title="MyShipTracking" path="https://www.myshiptracking.com" target="_blank" >}}
 AIS to obtain real-time ship data. After that we use our solution QueryOptima to normalize and structure the data and 
Blue Brain Nexus to embed it into a knowledge graph. Processed data is used for risk assessment, identification of safe 
routes and further visualization to assure timely and informed decision making.

Our data engineering pipeline supports:
* Multi-source ingestion and harmonization.
* Event-driven AI analytics for real-time alerting.
* Optimized cloud-native architecture for scalability.

This solution positions DroneSight as a valuable tool in supporting EU-wide emergency response efforts and 
environmental monitoring initiatives.

{{< img src="/blog-images/post-7/google-meet-screenshot-from-2024-11-23.png" alt="DroneSight team google meet" >}}

## {{< color-text text="National Recognition and Strategic Impact" >}}

{{< color-link link_title="DroneSight" path="https://taikai.network/cassinihackathons/hackathons/euspace-defence-security/projects/cm2yk8hgq03y9bsslwti7fzjd/idea" target="_blank" >}}
was recognized at the national stage of the hackathon, earning 3rd place in Ukraine’s selection round hosted by POLE 
Product Design Center and local innovation partner NGO
{{< color-link link_title="Metalab" path="https://metalab.space/news/ukrainska-komanda-signal-hawk-sered-peremozhtsiv-ievropeiskogo-kosmichnogo-khakatonu-cassini" target="_blank" >}}.
This achievement highlights our team’s capability to rapidly prototype and deploy AI-driven geospatial solutions 
aligned with EU strategic priorities.

{{< img src="/blog-images/post-7/DroneSight-team.jpg" alt="DroneSight team at TNTU" >}}

## {{< color-text text="Powered by Ukraine’s Innovation Ecosystem" >}}

Our journey in the CASSINI Hackathon was made possible by
{{< color-link link_title="Metalab" path="https://metalab.space/news/ukrainska-komanda-signal-hawk-sered-peremozhtsiv-ievropeiskogo-kosmichnogo-khakatonu-cassini" target="_blank" >}},
a forward-thinking urban innovation lab based in Ivano-Frankivsk. Known for catalyzing educational, social, and 
technological projects, Metalab provided a dynamic environment for ideation and rapid development. Their mission to 
foster sustainable innovation aligns perfectly with our own vision: to apply cutting-edge AI and data infrastructure 
to complex, real-world problems.
