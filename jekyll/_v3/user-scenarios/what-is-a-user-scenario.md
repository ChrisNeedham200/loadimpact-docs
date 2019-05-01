---
layout: classic-docs
title: What is a User Scenario?
description: "What is a User Scenario?"
categories: [user-scenarios]
order: 1
redirect_from:
  - /knowledgebase/articles/174287-what-is-a-user-scenario
  - /knowledgebase/topics/118845-user-scenario
---

***

A **user scenario** defines what URLs or web pages will be requested by the simulated users during a load test. The best user scenarios are ones that mimic real user behavior.

You can create multiple user scenarios and use them all in a single load test. E.g. if you have a shopping site, you can then have one user scenario that simulates a user just browsing on the site, and another user scenario that simulates a user who logs in on the site, puts some products into the shopping cart, and then performs a check-out.

**User scenarios** can be created in several ways. Starting on the [User scenarios](https://app.loadimpact.com/user-scenarios/new) page, you click the _**New user scenario**_ button.

![Create user scenarios in a variety of ways]( {{ site.baseurl}}/assets/img/v3/user-scenarios/what-is-a-user-scenario/user-scenario-options.jpg)

 You then have several options:
  - You can enter a URL in the _**Submit a URL for a quick scenario**_ field and then click the Proceed button.
  - If you know exactly what individual URLs you want the user scenario to load, you can write the load script for the scenario manually, choosing the **Scripting** option.
  - You can also choose the [Chrome Extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension) option, or the [Proxy Recorder]({{ site.baseurl }}/3.0/user-scenarios/load-impact-proxy-recorder) option.

The _**Submit a URL for a quick scenario**_ field will make the system load the URL and parse it like a web browser does. i.e. if you specified the address of a certain web page, the system would load that address, parse the HTML code it gets and find out what other resources (images, CSS files, etc) would be necessary to load for a web browser to be able to render the web page. Then a load script would be generated that fetched all these resources, in the same order a web browser would have fetched them. When the user scenario is run in a load test it would be just like a real user loaded that one, single web page with all its dependent resources.

The **Chrome Extension _(Most users utilize this option)_** button will allow you to record yourself browsing like a user would to generate a user scenario. You will be able to browse around on your site, loading multiple pages. This will create a load script that contains all the HTTP transactions that were executed by your own browser while you were browsing, mimicking your behavior exactly (including pauses in between page loads).

The **Proxy session button** will make LoadImpact launch a HTTP proxy server, allowing you to configure your web browser so that it uses this proxy server to access the web. It works similiar to the Chrome extension.

**Tip**: A basic load test is better than no load test. If you are unsure of where to start use the URL generator so you can set some baseline performance metrics.

**IMPORTANT**: _User scenarios are **NOT** automatically refreshed when your app/API/website is updated, or if you change the Target URL in the test configuration. Script maintenance is a normal part of performance testing and should be kept in mind as your site/app/APIs are updated._
