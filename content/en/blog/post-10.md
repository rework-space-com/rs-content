---
title: 'Keycloak Fine-Grained Admin Permissions (FGAP) V2: Fine-Grained Access & Safe Impersonation'
url: '/blog/2026-03-27-keycloak-fine-grained-admin-permissions-v2'
type: article
omit_header_text: false
featured_image: '/static-blog-images/post-10/cyber-security-concept-04.jpg'
summary_image: '/blog-images/post-10/keyboard-enlarged-by-loupe-short.jpg'
sharing_image: '/static-blog-images/post-10/keyboard-enlarged-by-loupe-share.jpg'
twitter_sharing_image: '/static-blog-images/post-10/keyboard-enlarged-by-loupe-twitter-share.jpg'
alt: 'Banner for article Keycloak Fine-Grained Admin Permissions (FGAP) V2: Fine-Grained Access & Safe Impersonation'
keywords: [ 'Keycloak', 'permissions', 'user impersonation', 'access policies', 'DevOps', '', '' ]
date: 2026-03-27
blogs: [ 'Keycloak', 'Terraform', 'DevOps', '', '' ]
draft: false
---

## {{< color-text text="The problem" >}}

Basic Keycloak permissions can be granted via the roles present in system clients. The **realm-management** client is
used in most cases. However, there is a problem: existing permissions cannot be configured to meet your needs and
provide too much access. You can make a user to manage all users in the realm or manage no one.

There are two possible scenarios in this case. Only a limited number of developers have access to the Realm, which makes
them responsible for managing test users. Or, grant access to every developer, allowing them to see your customers'
data. Neither of them can be considered enterprise approaches. We wish there was a way to group users and gain access
only to the required group. That is exactly what the Admin Fine-Grained permissions do.

## {{< color-text text="Fine-Grained Permissions" >}}

Mentioned Admin Permissions V2 are based on the
{{< color-link link_title="Fine-Grained permissions" path="https://www.keycloak.org/2025/05/fgap-kc-26-2" target="_blank" >}}
 , which appeared in Keycloak 26.2.0. This feature allows moving the user authentication out of the actual program and
delegating it to Keycloak itself. Before configuring the admin permissions, let's consider key components used in both
features.

The Resource server is an application that holds the available resources. The Resource is anything you want to protect.
It may be the file in object storage, a record from a database, or a basic API endpoint. Each resource may have a set of
optional scopes that defines which actions are allowed to perform on it. The access can be granted depending on the
conditions, forced by Policies.

Policies are not attached to the resources directly, instead the Permissions are a glue that combines them. This
approach makes the system flexible, allowing you to combine policy with different resources. They can be applied to the
scope of the resource itself, granting full control over it. This approach makes the system flexible, allowing you to
combine policy with different resources.

{{< img src="/blog-images/post-10/solving-the-overexposure-problem-diagram.png" alt="Picture 1. Global Admin Access vs. Fine-Grained Permissions: Solving the overexposure problem." >}}

### Terraform Configuration

The 
{{< color-link link_title="Fine-Grained permissions" path="https://www.keycloak.org/2025/05/fgap-kc-26-2" target="_blank" >}}
 feature is fully supported in the official community-driven
{{< color-link link_title="Terraform Provider v5.7.0" path="https://github.com/keycloak/terraform-provider-keycloak/tree/v5.7.0" target="_blank" >}}
 . It allows you to version your configuration and apply it to a new deployment immediately. This combination provides
you with functionality similar to the OPA policy agent, making Keycloak more than just an identity manager.

## {{< color-text text="Admin Permissions" >}}

Now, when we are familiar with Fine-Grained permissions, let's take a look at the Admin permissions. Obviously, the
resource server in this scenario is the Keycloak itself and it has a set of predefined resources with their scopes:
Users, Groups, Clients, and Roles. We can create policies and permissions to manage access on our behalf.

### Real example

Let's consider the real example of an Impersonation feature. Keycloak impersonation grants access to the account without
the user's credentials. It is helpful for testing and reproducing bugs. With basic permissions, it is not possible to
limit the pool of users available for impersonation. This can be harmful in production applications.

The best option is to use the v2 Admin permissions to grant granular access. It requires only two steps. Create a policy
to determine: "Who can impersonate?" And create a permission, which allows impersonation to only a limited set of users,
consider the test-users group.

{{< img src="/blog-images/post-10/enabling-fine-grained-admin-permissions-v2.png" alt="Picture 2. Enabling Fine-Grained Admin Permissions V2 in Keycloak Realm settings." >}}

Group policy is used in the example below to allow access only to the users who are members of the dev-group.

{{< img src="/blog-images/post-10/group-policy-example.png" alt="Picture 3. Creating a Group Policy to grant access specifically to the 'dev-group' members." >}}

With the policy created, it’s time to create group permissions with the scope impersonate-members.

{{< img src="/blog-images/post-10/group-permission-example.png" alt="Picture 4. Setting up the 'impersonate-members' permission." >}}

Now the developers can impersonate only a limited pool of test accounts.

## {{< color-text text="Challenges" >}}

Fine-Grained Admin Permissions (FGAP) is a fresh feature that greatly expands the list of Keycloak use cases. Because it
is new, the feature has several pitfalls that present new challenges. In this section, we will examine two of them.

### Limited Terraform Support

Even though the FGAP feature is based on Fine-Grained permissions, it implements its own separate API. The separation
results badly in the Terraform coverage. Currently, it is only possible to enable the feature with the provider.
Permissions and Policies may be created only with UI or API calls. There is an
{{< color-link link_title="issue" path="https://github.com/keycloak/terraform-provider-keycloak/issues/1187" target="_blank" >}}
 related to the problem. However, there is no final date for when the feature will be fully supported.

### Bugs

Like any new feature, it may contain bugs that weren't investigated during the testing stage. For example, Keycloak
26.2.0 has a bug that ignores Fine-Grained Group Permissions during new user creation. It allows adding a new user to a
group with limited access.

Let's consider a bug mentioned in the
{{< color-link link_title="discussion" path="https://github.com/keycloak/keycloak/discussions/37133#discussioncomment-15298242" target="_blank" >}}
 from the Keycloak repository (FGAP configuration can be found here). The test realm has a group hierarchy displayed in
Picture 5. Where users from GroupA have the lowest privileges and aren’t able to add members to the child groups, and
perform any changes in user accounts from privileged groups.

{{< img src="/blog-images/post-10/group-hierarchy.png" alt="Picture 5. Test realm group hierarchy." >}}

After logging in with the GroupA member’s account, who has the least privileges, it is possible to add a new user to
GroupC (Picture 6). The action can only be performed during creation requests.

{{< img src="/blog-images/post-10/adding-group-with-user-creation.png" alt="Picture 6. Adding a group during the user creation." >}}

Fortunately, it is not possible to set the password for a new user immediately. After creating the user, a temporary
password must be set, or a ‘Reset password’ message must be sent. However, due to the FGAP configuration, the mentioned
actions are not allowed for the GroupA member. All requests finish with the 403 error (Picture 7).

{{< img src="/blog-images/post-10/403-error.png" alt="Picture 7. Attempting to set the password for a new user." >}}

## {{< color-text text="Conclusion" >}}

Keycloak Fine-Grained Admin Permissions feature solves a long-standing problem: how to delegate administrative access
without overexposing sensitive data. Instead of an all-or-nothing approach with realm roles, permissions can now be
scoped to specific resources and actions. This allows safe responsibility delegation while protecting production
customer data. With Terraform support, FGAP will soon turn Keycloak into a powerful access-control platform, allowing
competition with OPA.
