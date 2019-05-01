---
layout: classic-docs
title: I was invited to an organization, why can't I run tests?
description: How to correctly specify/choose a correct project to run your tests in LoadImpact
categories: [frequently-asked-questions]
order: 5
---

***

<h2>Purpose</h2>

The Organizations, Projects, and Team Member functionality enables account administrators to organize and control access to their LoadImpact account and subscription for collaboration purposes. However, newly invited members, not being familiar with this structure, sometimes ask us questions or get stuck when trying to run tests. For example:

**I was invited to an organization with a subscription.  However, When I try to run tests, I get an error that my subscription doesn't have enough Virtual Users/exceeds the duration/uses too many load zones. Our subscription allows for the test I want to run. What is wrong and how do I fix this?**

It's important to note that every user in LoadImpact is an owner of organization. Organizations can contain multiple Projects. Each Project can contain multiple tests. LoadImpact subscriptions are associated with an Organization. In almost all cases, this error is a result of trying to run a test in an Organization without a subscription (likely your own default organization).

### How do I change my Organization to fix this?

#### In the web interface

If you are running tests from the web interface, you will need to use the drop down menu in the left side bar, to select a project within the organization with a subscription. In Figure 1 below, our user is a member of two organizations, "LoadImpact" and "Second Organization".  In this example "LoadImpact" is our primary organization associated with the users account and  "Second Organization" is one which the user has been invited to. In order to run tests using the subscription that was purchased and associated with "Second Organization" we would need to select a Project associated with it.

#### From the command line

If you are using k6 to trigger tests from the command line, you will need to specify, in your test configuration, the project that should be used to run a test.  By default, k6 will use the default Organization and default Project associated with a users account.  In order to do this, the `projectId` must be set as a [test configuration option]({{ site.baseurl }}/4.0/reference/test-configuration-options/#project)  within the `ext` object of your script.

Example:

{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            projectID: 123456
        }
    }
}
{% endhighlight %}


![Select a project]({{ site.baseurl }}/assets/img/v4/frequently-asked-questions/specify-project/projects-in-app.png)
 _Figure 1, Project Selection_
