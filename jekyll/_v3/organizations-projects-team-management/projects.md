---
layout: classic-docs
title: Projects
description: Projects are a foldering system within LoadImpact. Projects allow you to organize your tests/scenarios and assign team members to have access to them.
categories: [organizations-projects]
order: 2
redirect_from: /knowledgebase/articles/780522-projects
---

### What are projects?

Projects are a way to organize work and can be thought of as folders. Every organization can have several projects.

All tests, user scenarios, data stores and organization members are grouped under projects. Here is a diagram of the organizational hierarchy:


![Hierarchy]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/projects/organization-hierarchy-diagram.png)

As an admin, to add a new project, navigate to the "Organizations and Projects" menu using the drop down next to your name:

![Menu navigation]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/projects/menu-navigation.png)


Next, Select "New Project":

![Create New Project]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/projects/create-new-project.png)

Then, give your project a name and add [team members]({{ site.baseurl }}/3.0/organizations-projects-team-management/adding-team-members/), as appropriate. Note: you can only add team member who have been invited to your [organization]({{ site.baseurl }}/3.0/organizations-projects-team-management/organizations/) previously.

![Name your project, add team member and create it]({{ site.baseurl }}/assets/img/v3/organizations-projects-team-management/projects/name-invite-create.png)

***

Once someone has been invited to an organization and assigned a permission level, they are either automatically(admins) or manually(read/write members) added to projects. Permissions are set at the organization level, and include the following:

**Owner:** Owns the organization and has all privileges. Automatically assigned to all projects. Each organization has one owner.

**Admin:** Can do everything an Owner can do, including adding/removing members and permissions, assigning members to projects, managing billing, creating projects, managing testing, managing API/server agent/New Relic tokens and keys, and creating new organizations. The only thing they can not do is remove an organization owner. Finally, Admins are automatically assigned to all projects in the organization.

**Read/Write:** Once assigned to a project, Read/Write members can create user scenarios, data stores and tests, and can run tests. They can also access LoadImpact server agent tokens and New Relic API keys. They can not manage billing, create projects, assign themselves or other members to projects, add/remove members from the organization, or access LoadImpact API tokens. Finally, Read/Write members must be specifically added to projects by organization Owners or Admins.

If you have any questions about projects or would like help with setup, please reach out to us here at support.
