---
layout: classic-docs
title: Interpreting the Performance Trending Graph
description: Guide on how to interpret the LoadImpact performance trending graphs for your load tests
categories: [guides]
order: 8
redirect_from:
  - /knowledgebase/articles/617088-how-do-i-interpret-the-performance-trend-analysis
  - /4.0/how-to-tutorials/how-to-interpret-performance-trending/
---

***

The performance trending graph plots single, high-level performance metrics for executions of the same test over time. This enables you to locate patterns of performance degradation or improvement and easily validate the performance impact of code and infrastructure changes. After each completion of an individual test run, we automatically update the performance trending graph.

The single high-level performance metric given in the performance trending graph is the _95% percentile_ of all requests included in the test. This means that 95% of the HTTP(s) requests in your test will have a load time that performs better than this value. Similar to looking at an individual tests `Response time`, a rising graph indicates degrading performance over time.

Performance trending provides the most value when tests are executed on a regular basis. Refer to our [Guides and Integrations]({{ site.baseurl }}/4.0/guides/) for more information on integrating/automating your tests.

**Note:** Failed tests will be shown in red, passing tests in green, and aborted tests will show a "negative" value.


![Performance trending graph]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/perf-trending.png)
