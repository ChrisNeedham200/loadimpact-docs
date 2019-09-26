---
layout: classic-docs
title: Interpreting results: An overview of LoadImpact Insights
description: An in depth overview on how to interpret various result data in LoadImpact and k6
categories: [result-analysis]
order: 0
redirect_from:
  - /knowledgebase/articles/1172458-k6-insights-overview
  - /knowledgebase/articles/174121-how-do-i-interpret-test-results
  - /knowledgebase/articles/173368-test-result-introduction
  - /knowledgebase/topics/118848-test-results
---

***

<h2>Background</h2>

LoadImpact web based result analysis, Insights, is designed to be the perfect companion to [k6](https://k6.io/), a convenient and powerful solution for storing and analyzing your k6 test results.

Continuous use of Insights will also enable the performance trending functionality to keep track of how the performance of your system changes over time. This is good for spotting performance regressions before they become a larger problems, ensuring you are within SLAs for requests and more, depending on your use case/need.

The Insights page is divided into these sections:
- TOC
{:toc}

## Performance overview
The top of the page provides a breadcrumb menu and an overview of details about your test.

The breadcrumb menu allows to quickly navigate between the latest runs of the test, the test page or all the tests in the current project.

The test overview shows the test length, number of maximum VUs, and the status of the test. If the test is running; live metrics such as the current number of active VUs and time will be displayed and the chart updated in real time. When the test is complete, the status will be updated based on the results of any [thresholds]({{ site.baseurl }}/4.0/core-concepts/thresholds/) that you defined.

![Test run navigation]({{ site.baseurl }}/assets/img/v4/result-analysis/test-run-navigation.png)

Performance overview panel contains:
- A chart with VU ramping, response time and requests per second metrics
- Performance Alerts, based on what our algorithms detect.
- A high level summary of your test (If no issues were detected)

The first signal of a good or bad result will generally be in the Performance overview panel.  Here are the most common patterns to consider.  Our Performance Alerts will notify you if some of these are detected.

Typical signs of a good result:
- Response time has a flat trend for the duration of the test
- Request rates follow the same ramping pattern as Virtual Users(if VUs increase, so does request rate)

Typical signs of a performance issue/bottleneck
- Response times rise during the test
- Response times rise, then quickly bottom out and stay flat
- Request rates do not rise with VUs (and response times start to increase)

This is not an all inclusive list. You should use these patterns as a first indicator of good or bad performance of your test.

![Insights: Performance overview]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-overview/insights-performance-status.png)

## Filters

The filter section allows you to:

- Filter results system or user defined tags
- Change data aggregation that is used in test breakdown structure and analysis panels(min, mean, max, and different percentiles)

![Insights: filters]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters.png)


## Result Tabs

The result tabs allow you to dig into the specific result data sets from your test. We present the following tabs to organize your result data:

Tab Name   | Defintion                                                             | Add to analysis? | Sorting
-----------|-----------------------------------------------------------------------|------------------|----------------
Thresholds | List of your Thresholds in the order they are defined in your script. | Yes              | In order defined
Checks     | List of Checks, organized into Groups (if used).                      | Yes              | By group, or list (all)
HTTP       | List of HTTP requests made, organized into Groups (if used).          | Yes              | By group, or list (all)
Websocket  | List of Websocket requests made, organized into groups (if used)      | Yes              | By group, or list (all)
Analysis   | Tab used to overlay data for analysis                                 | N/A              | N/A
Script     | Script used to run your test (cloud tests only)                       | N/A              | N/A
{: class="table table-bordered"}

These tabs let you dig into your test data in a visual way.  You are able to click on any metric to expand a graph.  You can also add theses graphs to the Analysis tab, for comparison. This allows you to look for interesting correlations in the data. These tabs are designed to be error driven.  Note the &#10003; or &#10005; next to the individual metrics for Thresholds, Checks, HTTP, or Websockets if failures were encountered.  In the image below, note our failing check, as an example.

![Checks tab with a failing check]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-overview/insights-checks.png)

**Note**: The Thresholds, Checks, HTTP, and Websocket tabs will only be present if data exists for them.  For example, if you ran a test that only made Websocket requests with a threshold defined, you would not see the Checks or HTTP tab (as no data would exist for either).

Refer to these articles for more specific information on:
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})
- [Checks]({{ site.baseurl }}{% link _v4/core-concepts/checks.md %})
- [HTTP Table]({{ site.baseurl }}{% link _v4/result-analysis/insights-http-table.md %})

#### Analysis

The analysis tab enables you to analyze and compare selected metrics. This is helpful for finding correlations in the results to investigate.

To effectively use this tab, we recommend looking at the following things:

- Ensure that VUs and Request rate follow the same trend.
- Add and compare interesting requests from the HTTP and Websocket tabs to compare with other metrics
- Check the load generator CPU and Memory consumption by clicking on "add metrics" to ensure they are not saturated (metrics only available for tests run in the cloud)
- Add thresholds that have been exceeded
- Add checks that have failures

How you work through the analysis will depend on your individual test results.  You may or may not need to add certain things. The above list is not meant to be all inclusive, rather a starting point in helping you dig into performance related issues so you can identify them. For more information, please refer to our article on the [Analysis Tab]({{ site.baseurl }}{% link _v4/result-analysis/insights-analysis-view.md %})

![Insights: analysis section]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-overview/analysis-tab.png)

***

See Also:
- [Insights: Performance Overview]({{ site.baseurl }}{% link _v4/result-analysis/insights-performance-overview.md %})
- [Insights: filters]({{ site.baseurl }}{% link _v4/result-analysis/insights-filters.md %})
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})
- [Checks]({{ site.baseurl }}{% link _v4/core-concepts/checks.md %})
- [Insights: HTTP Table]({{ site.baseurl }}{% link _v4/result-analysis/insights-http-table.md %})
- [Insights: Analysis Tab]({{ site.baseurl }}{% link _v4/result-analysis/insights-analysis-view.md %})


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY0MDQyODczXX0=
-->
