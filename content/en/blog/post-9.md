---
title: 'The Difference Between Knowing the Name of a User and Knowing the User'
url: '/blog/2025-12-02-rs-dataplatform-project-concept'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-8/seeds-background.png'
summary_image: '/blog-images/post-8/seeds-of-bravery.png'
sharing_image: '/static-blog-images/post-8/post-8-short-seeds-of-bravery-2025.png'
twitter_sharing_image: '/static-blog-images/post-8/seeds-of-bravery-twitter-share.png'
alt: 'Banner for article The Difference Between Knowing the Name of a User and Knowing the User'
keywords: [ 'DevOps', 'Seeds of Bravery', 'UASEEDs', 'Cloud Security', 'Hybrid Cloud', 'IAM', 'Open source' ]
date: 2025-12-02
blogs: [ 'DevOps', 'FreeIPA', 'Terraform', 'Rework-Space', 'Ukraine' ]
draft: false
---

## {{< color-text text="Stop the madness of Hybrid Cloud Identity" >}}

You know, our team learned very early the difference between knowing the name of something and knowing something. You
can look at a bird and say, "That’s a brown-throated thrush", or in German, "Die Schwarzkehldrossel", or in AWS, "arn:
aws:iam::123456:user/Bob". But knowing those names doesn't tell you anything about the bird. And it certainly doesn't
tell you anything about Bob.

How we build data platforms in the cloud today? It’s absolutely crazy. It’s a mess!

For example, we have a data engineer. Let’s call him Bob.
We want Bob to work on AWS. So we go into the console and create "Bob."
Then we want Bob to use Google Cloud. So we go into another console and create "Bob" again.
Then we have an on-premise OpenStack cluster. We create "Bob" a third time.

{{< img src="/blog-images/post-9/iam-fragmentation-challenge.png" alt="The Identity Fragmentation Nightmare" >}}

Now we have three Bobs! But in reality, there is only one Bob. We have confused the _badge_ with the _person_. We are
managing names, not identities. And when the real Bob leaves the company, we have to run around to three different rooms
to take back three different badges. It’s unscientific. It’s inefficient. And frankly, it’s dangerous.

Why we built
{{< color-link link_title="RS-DataPlatform" path="https://dp.rework-space.com/" target="_blank" >}}
project? - to stop the madness of Hybrid Cloud Identity.

### {{< color-text text="The First Principle: The Bouncer vs. The Boss" >}}

When we started designing RS-DataPlatform, we asked a simple question: **Why does the cloud provider get to decide
who Bob is?** The cloud provider is just a tool. It’s a hammer. You don’t ask your hammer for permission to enter your
house. You use the hammer to build the house. So we established a rule, a fundamental principle of nature for our
platform: **Cloud IAM is just the bouncer. It is NOT the boss.** (Or in technical terms: Cloud IAM is a Policy
Enforcement Point, not the Source of Truth.)

### {{< color-text text="The Solution: The Passport Office" >}}

We decided to build
{{< color-link link_title="RS-DataPlatform" path="https://dp.rework-space.com/" target="_blank" >}} -
a "Sovereign Identity Core". It sounds fancy, but it’s actually very simple.
It works like a passport system.

1. **The Source of Truth ( {{< color-link link_title="FreeIPA"
   path="https://registry.terraform.io/providers/rework-space-com/freeipa/latest" target="_blank" >}} ):** This is the
   government office. This is where Bob exists. If Bob is here, he is real.
2. **The Translator ( {{< color-link link_title="Keycloak" path="https://www.keycloak.org/" target="_blank" >}} ):**
   This is the passport control. It speaks all the languages. It speaks "AWS SAML". It speaks "Google OIDC". It speaks "
   OpenStack Keystone".

{{< img src="/blog-images/post-9/iam-solution-architecture.png" alt="The RS-DataPlatform Solution" >}}

Now, when Bob wants to use AWS, he doesn't log in to AWS. He logs in to our platform.
Our platform turns to AWS and says, _"Hey, I’ve checked, and this is Bob. He’s with us. Give him the keys to the
truck."_ And AWS says, _"Okay."_

### {{< color-text text="Why We Didn't Just Rent a Tuxedo (The Alternatives)" >}}

Now, some of you smart fellows might say, _"Hey, why don't we just use Okta?"_ or _"Doesn't AWS have a thing for this?"_
And you're right, they do. But here's the difference between a tool that works _for_ you and a tool that works _on_ you.

- **The "Rent-a-Passport" Problem (Okta, Auth0):** These are fantastic services. But they are like renting a very
  expensive tuxedo. It looks great, but you don't own it. You pay a premium for every user, every month. With
  RS-DataPlatform, you own the passport office. It’s open source. It’s yours.
- **The "Walled Garden" Problem (AWS IAM Identity Center):** This tool loves AWS. It tolerates everyone else. It’s like
  a mechanic who only has metric wrenches. Great for your European car, but useless when you need to fix your American
  truck (or your on-premise OpenStack). We treat all clouds as equal tools.

### {{< color-text text="The Laws of Physics (Policy as Code)" >}}

Solving identity is just the first step. Once you know _who_ Bob is, you need to make sure Bob doesn't accidentally burn
down the house. In nature, you can't travel faster than light. It's not a rule written in a book that you can choose to
ignore; it's just how the universe works. We wanted our platform to work the same way.

We use **Open Policy Agent (OPA)** to write these laws of physics. If a developer tries to write code that creates a
local IAM user, the system doesn't just suggest they stop. It simply says, _"No."_ It’s impossible. The universe of our
platform doesn't allow it. This catches the mistake before it ever becomes a security hole.

### {{< color-text text="The Magic Box (Crossplane)" >}}

Finally, we want to give Bob the tools to build his own house without calling us every five minutes. We use
{{< color-link link_title="Crossplane" path="https://www.crossplane.io/" target="_blank" >}}
to turn this complex hybrid cloud into a simple vending machine. Bob
shouldn't have to care about VPCs, Subnets, or Role bindings. Bob just wants a **Data Lake**.

Bob pushes a button: _"I need a Data Lake."_
And the platform automatically:

1. Spins up the storage.
2. Configures the networking.
3. **Connects the Identity Core** so Bob has access immediately.

It’s not just automation; it’s autonomy.

### {{< color-text text="Conclusion: Knowing the Bird" >}}

We are building **RS-DataPlatform** because we believe you should own your identity. You should be the master of your
own data, whether it lives in a server farm in Virginia or a box under your desk.

We are taking the complexity out of the "Hybrid Cloud" so you can stop worrying about the names of the users, and start
caring about the users themselves - and the amazing things they can discover.

### **👇 What do you think?** Are you managing three Bobs, or just one?
