---
layout: classic-docs
title: How to interpret the performance trending graph
description: How to Interpret the performance trending graph for your load tests
categories: [test-results]
order: 5
---

***

The performance trending graph plots individual runs of the same test over time. This enables you to locate patterns of performance degradation or improvement and easily validate the performance impact of code and infrastructure changes. Once you have completed more than one run of a particular test, LoadImpact's performance trending plots single, high-level performance metrics on the same graph for each individual run.

The single high-level performance metric given in the performance trending graph is the _95% percentile_ of all requests included in the test. This means that 95% of the HTTP(s) requests in your test will have a load time better than this value. Similar to looking at an individual tests `VU Load time`, a rising graph indicates degrading performance over time.

To make the best use of the Performance trending graph, we recommend utilizing our API to [automate tests]({{ site.baseurl }}/3.0/integrations/automating-load-testing/) or [schedule tests]({{ site.baseurl }}/3.0/test-configuration/scheduling-tests/) to run on a regular basis.

Since performance and load testing typically calls for multiple iterations and changes of a test run, we demarcate the graph to show when test configurations were changed. This is displayed as an orange line on the graph, as shown below.


![Performance trending graph]({{ site.baseurl }}/assets/img/v3/test-result/interpreting-the-performance-trending-graph/performance-trending-graph.png)


**Note**: Although we will include all URLs tested by default, you can purposely include/exclude certain URLs from trending by setting “report_results=true/false” in the load script for the test.

Note: If you choose to exclude URLs from trending by setting “report_results=false” in the test script, the results for the excluded URLs will not be automatically reported in the general test results - however, you can still use custom metrics to have URLs in your results and analysis although they will not be included in the trending graph.
