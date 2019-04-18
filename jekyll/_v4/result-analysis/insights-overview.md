---
layout: classic-docs
title: Insights overview
description: An overview of LoadImpact Insights and how to utilize it to get actionable data from your load tests
categories: [result-analysis]
order: 0
redirect_from: /knowledgebase/articles/1172458-k6-insights-overview
---

***

<h1>Background</h1>

Insights is designed to be the perfect companion to [k6](https://k6.io/), a convenient and powerful solution for storing and analyzing your k6 test results.

Continuous use of Insights will also enable the performance trending functionality to keep track of how the performance of your system changes over time. This is good for spotting performance regressions before they become a larger problems, ensuring you are within SLAs for requests and more, depending on your use case/need.

The Insights page is divided into these 5 sections:
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

### Filters

The filter section allows you to:

- Filter results by user tags and system tags.
- Change data aggregation that is used in test breakdown structure and analysis panels(min, mean, max, and different percentiles)

![Insights: filters]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-filters.png)


### Breakdown tree

The breakdown tree section contains your test script in a visual way; most of the components (thresholds, groups, requests...) that you've added in your script will be represented in the same order in the breakdown structure. The breakdown tree was designed to be error driven, you will notice a red bar on the left of items with errors. Also take note of the three dots on the far right. This allows you to add an individual metric to the Metrics tab for analysis.

This section allows you to quickly:

- Find failing errors: checks, thresholds and requests.
- Find the slowest/fastest groups and requests.
- Visualize custom metrics.
- Drill down check, custom and request metrics.



![Insights: test breakdown structure]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-breakdown-structure.png)

### Metrics

The analysis section is where you analyze selected metrics. The section allows you to:

- Get a quick overview of some metrics: VUs, response time and check failure aggregation.
- Visualize custom metrics.
- Add, compare, and analyze metrics from the breakdown section.

![Insights: analysis section]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-metrics-analysis.png)

### URL Table

The URL-table section is where you can get a raw overview of the URLs in your test. It will help you to:

- Get an overview of all the URLs tested in your k6 test.
- Identify which URLs are returning expected or unexpected status codes.
- Sort the URLs by different column values.
- Filter the URLs by [k6 tags](https://docs.k6.io/docs/tags-and-groups).

![Insights: URL table]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-url-table.png)



<!--stackedit_data:
eyJoaXN0b3J5IjpbLTY0MDQyODczXX0=
-->
