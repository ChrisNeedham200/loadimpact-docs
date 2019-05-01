---
layout: classic-docs
title: What's the Difference Between LoadImpact's Version 3.0 (Lua) and 4.0(JavaScript)
description: A brief overview of the differences between LoadImpact 3.0 (Lua) and LoadImpact 4.0 (JS/k6) products
categories: [frequently-asked-questions]
order: 3
redirect_from:
  - /4.0/migrating-lua-to-js/migration-overview/
---

***

<h2>Purpose</h2>

An overview of some key differences between Load Impact 3.0 and 4.0.

***

From a general performance testing perspective the 3.0 and 4.0 products are more or less the same:

1. You create one or more user scenarios that step through a particular process of your target system that you want to test.
2. You then combine your user scenario(s) with a traffic simulation profile that specifies how many Virtual Users (VUs) should be concurrently executing the user scenario(s) at different points in time of the test.
3. You execute your test, metrics data is collected and you are presented with results.

## Differences between 3.0 and 4.0 products
When looking more closely though there are some differences in how you accomplish step 1, 2 and 3 above.

A big difference is in the workflow that you can accomplish with each respective product.

The 3.0 product is completely cloud based, user scenarios and test configuration are created/edited and stored in the LoadImpact cloud service, and running tests is also done exclusively from the cloud. This also means the target system that is being tested needs to be accessible from the public Internet.

With the 4.0 product we've opened up the platform in two important ways. First, the core load testing software, [k6](https://github.com/loadimpact/k6), is now an open source tool, and secondly it can be used both [on-premise]({{ site.baseurl }}{% link _v4/guides/local-on-premise-execution.md %}) as well as from the LoadImpact service via our [cloud execution]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %}) functionality. The user scenarios and test configuration has been merged into one, it's now all just JavaScript code, so very version control friendly.

This brings us to an important difference, in the 4.0 product you're responsible for storing and version controlling your tests (the JavaScript combining user scenario and test configuration), and the LoadImpact cloud service can provide you with result storage, visualization and trending, as well as geographically distributed cloud execution of tests.

### User scenario
In the 3.0 product user scenarios described using Lua code. You can end up with the Lua code in various ways, by using one of the recorder options, the Postman converter or hand coding it, but at the end of the day the output of all these various ways is a piece of Lua code.

In the 4.0 product user scenarios are described using JavaScript, the ES6 version of JS to be precise. This means it's not only a more familiar language to most developers/testers but it also introduces a nice addition compared to the 3.0 product: support for modules, allowing code to be modularized and reused across tests and teams.

See the [Lua to JS migration guide]({{ site.baseurl }}{% link _v4/guides/lua-to-js.md %}) for more information on how to migrate your Lua user scenarios to JS.

### Test configuration
In the 3.0 product you compose one or more user scenarios into a separate entity known as a "Test" (aka "Test configuration"), and then add additional configuration like traffic simulation profile and thresholds. This is done through the LoadImpact WebApp UI.

In the 4.0 product the equivalent configuration options are specified in the script itself:

{% highlight js linenos %}
export let options = {
    // Stages represents the traffic ramping profile that will be used in the test,
    // controlling the VU concurrency throughout the duration of the test
    'stages': [
        // Linear ramp-up from 0 to 50 VUs for 60s
        { 'target': 50, 'duration': '60s' },

        // Stay constant at 50 VUs for 60s
        { 'target': 50, 'duration': '60s' },

        // Linear ramp-down from 50 to 0 VUs for 60s
        { 'target': 0, 'duration': '60s' }
    ],

    // Use thresholds to set your metric targets, thresholds are used to pass/fail tests
    // and for controlling automatic test termination
    thresholds: {
        // Add a threshold mark test as failed is 95th percentile of overall response time goes above 500ms
        'http_req_duration': 'p(95)<500',

        // Add another threshold to fail and abort the test if the threshold hits 1s
        'http_req_duration': {'trigger': 'p(95)<1000', 'abortOnFail': true}
    }
};
{% endhighlight %}

***

## See Also
- [Lua to JS migration guide]({{ site.baseurl }}{% link _v4/guides/lua-to-js.md %})
- [Configuration options]({{ site.baseurl }}{% link _v4/reference/test-configuration-options.md %})
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NDU3OTE4MV19
-->
