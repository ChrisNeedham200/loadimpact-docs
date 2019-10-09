---
layout: classic-docs
title: LoadImpact product overview
description: Am overview of the LoadImpact's product offering and related components
categories: [overview]
order: 2
redirect_from: /4.0/getting-started/product-overview/
---

***

As illustrated below LoadImpact 4.0 is composed of 3 major components:

- **[k6]({{ site.baseurl }}/4.0/overview/product-overview/#k6)** - the [open source load testing tool](https://github.com/loadimpact/k6) created and maintained by LoadImpact.

- **[LoadImpact Insights]({{ site.baseurl }}/4.0/overview/product-overview/#loadimpact-insights)** - A LoadImpact cloud service offering. Insights enables you to store, analyze, plot trending graphs, and share your k6 test results with your team.

- **[LoadImpact Cloud Execution]({{ site.baseurl }}/4.0/overview/product-overview/#loadimpact-cloud-execution)** - A LoadImpact cloud service offering. Cloud execution enables you to run tests on the global cloud infrastructure managed by LoadImpact. We provide on-demand support for larger tests and geographically distributed tests.

![LoadImpact 4.0 product overview]({{ site.baseurl }}/assets/img/v4/getting-started/v4-product-overview.svg)

## k6

An open source load testing tool where you script your tests in ES6 JavaScript. To learn more about k6 head over to the [k6 repo on Github](https://github.com/loadimpact/k6). To learn more about k6 test scripting head on over to the [test scripting examples]({{ site.baseurl}}/4.0/examples/) section.

## LoadImpact Insights

As k6 tests can run from anywhere, local/on-premise or in the cloud (on your own infrastructure or ours), having a central place where results can be sent and stored, analyzed and trended is very convenient. That's what Insights was built for. Learn more about Insights in the [result analysis]({{ site.baseurl}}{% link _v4/result-analysis/insights-overview.md %}) section.

## LoadImpact Cloud Execution

There are many different use cases for running load tests. In some, running tests locally (on-premise/behind the firewall) is the only solution. In others, you want the convenience of a cloud service or need the geographic distribution and scale that it offers. That's when Cloud Execution is a good fit. Learn more about Cloud Execution in the [test running]({{ site.baseurl}}{% link _v4/guides/cloud-execution.md %}) section.

Now, let's get you set up so you can [run your very first test]({{ site.baseurl}}{% link _v4/getting-started/local-test-execution-hello-world.md %})
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI1OTYyNTI3MSwtMTQ5Nzc1MjU2NV19
-->
