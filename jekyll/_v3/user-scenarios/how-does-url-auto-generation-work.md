---
layout: classic-docs
title: Auto Generated URL scenarios
description: "How does the URL auto-generation feature work?"
categories: [user-scenarios]
order: 3
redirect_from: /knowledgebase/articles/174237-how-does-the-auto-generate-feature-work
---

***

LoadImpact provides three tools for you to get a basic script quickly; the “Auto-generate” feature, the [Chrome Extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension) and the [Proxy Recorder]({{ site.baseurl }}/3.0/user-scenarios/load-impact-proxy-recorder).

![Create user scenarios in a variety of ways]( {{ site.baseurl}}/assets/img/v3/user-scenarios/how-does-auto-generation-work/user-scenario-options.jpg)

The **"Auto-generate"** feature utilizes the same Page analyzer at [loadimpact.com](https://loadimpact.com/). It loads a single page, parses the HTML and CSS to find objects needed to render the page, then generates a load script that loads the page objects in the same order that a real browser client would load them. This results in a simple load script with only two request batches, one with just the Target URL (loading the HTML for the page) and one with all the dependent objects that are needed to fully render that page. The page analyzer omits resources from outside the target domain. This is ideal for the simpliest of tests, either creating baseline metrics or testing a landing page.


**Consider**:  JavaScript included in the page is not executed, so any resources loaded by JavaScript through AJAX calls will not be included in the script.

**Having trouble with the URL Generator?** Certain pages are unable to be correctly analyzed for scenario creation. When this occurs, we recommend using our [Chrome Extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension) or [Proxy Recorder]({{ site.baseurl }}/3.0/user-scenarios/load-impact-proxy-recorder) to create your scenarios.
