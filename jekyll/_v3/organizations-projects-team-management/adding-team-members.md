---
layout: classic-docs
title: Adding Team Members
description: Brief guide on adding/inviting team members to participate in your LoadImpact account and subscription.
categories: [organizations-projects]
order: 3
redirect_from: /knowledgebase/articles/922467-adding-team-members
---

Team members are other users within your [organization]({{ site.baseurl }}/3.0/organizations-projects-team-management/organizations/) that you would like to participate in your organization in LoadImpact. To add Team Members you must first invite them to the parent organization. This is done through Organizations and Projects menu or the "+" menu in the upper right corner of the platform.

Either use the Organizations and project menu

![Menu navigation]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/adding-team-members/menu-navigation.png)


or the  "+" sign

![Alternative method to add team members using + icon]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/adding-team-members/alternative-add-team-member.png)


Click "Add Member" and type the email of the team member you wish to invite and assign them the correct permission level, and projects (if they are a read/write member).



![Alternative method to add team members using + icon]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/adding-team-members/add-member-assign-role.png)



Once finished, click "update organization".

***

Once someone has been invited to an organization and assigned a permission level, they are either automatically(admins) or manually(read/write members) added to projects. Permissions are set at the organization level, and include the following:

**Owner:** Owns the organization and has all privileges. Automatically assigned to all projects. Each organization has one owner.

**Admin:** Can do everything an Owner can do, including adding/removing members and permissions, assigning members to projects, managing billing, creating projects, managing testing, managing API/server agent/New Relic tokens and keys, and creating new organizations. The only thing they can not do is remove an organization owner. Finally, Admins are automatically assigned to all projects in the organization.

**Read/Write:** Once assigned to a project, Read/Write members can create user scenarios, data stores and tests, and can run tests. They can also access LoadImpact server agent tokens and New Relic API keys. They can not manage billing, create projects, assign themselves or other members to projects, add/remove members from the organization, or access LoadImpact API tokens. Finally, Read/Write members must be specifically added to projects by organization Owners or Admins.

If you require more team members, please reach out to support.
