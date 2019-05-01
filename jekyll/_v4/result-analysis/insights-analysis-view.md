---
layout: classic-docs
title: "Insights: Analysis Tab"
description: Guidance on using the Analysis tab within LoadImpact Insights
categories: [result-analysis]
order: 4
redirect_from:
  - /knowledgebase/articles/1172473-insights-analysis-view
  - /knowledgebase/articles/1172458-insights-overview
---
***

<h2>Purpose</h2>
The Analysis tab in insights allows you to dig into the trends of the various data recorded while your test was running.  It's a great place to look to get an understanding of performance of the System Under Test (SUT). It can also help you find important correlations in your data between the various metrics. At the top, The comparison chart is where you analyze selected metrics. Below that are the avilable metrics you add for comparison avaiable metrics to add to your test.

Use this tab to compare and analyze additional metrics and their correlations, including any custom metrics, group timings, HTTP request durations, etc.

**What's the best way to use this tab?**

By default, you start with the ramping profile of Virtual Users during your test.  You should use the "Add Metric to Visualize" button to start adding metrics for comparison, including custom metrics. Of the default metrics here are some examples of metrics and things you may want to look for. This is not an all inclusive list.

**Request rate** - The request rate of all Virtual Users at any given point in a test. Virtual Users can make multiple RPS each. Generally your request rate should follow the same trend of Virtual Users.  As more virtual users come on line, more total requests are being made.  If your Virtual Users are rising and your request rate is flat or declining, this would be a signal of a performance issue.

**Data Received** - Data received/loaded by the Load generators. Similar to request rate, we would expect more data to be received as virtual users rise. A flattening amount of data being received may be indivative of a bandwidth type bottleneck.

**Utilization CPU/Memory** - Built in Load Generator metrics for Cloud Execution. This does not measure these metrics from the SUT.  High utilization of either or both of these metrics could skew test results.  Use this as a sanity check.  Because of how Virtual Users come online, it's normal for tests to start with higher CPU utilization.

**HTTP Requests from the HTTP tab** - Response times for HTTP requests should remain flat during a test. This would suggest that the SUT is capable of handling the load. If you've noted any requests from the HTTP tab that have large variances between min/max response time along with percentile metrics that are closer to the max, you may want to look for how this relates to other data in your test. It also may give you hints as to where bottlenecks exist.  e.g. Requests that query a DB perform/degrade worse than those to static assets.

**Group Duration** - Similar to HTTP requests, we would expect group duration to stay flat during a test. A group duration that is increasing would suggest that requests in that group are degrading, this is helpful in narrowing down where  performance issues exist.


***


![Insights: Analysis section overview]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-analysis-tab/insights-tab-overview.png)

*Take Note*: All metrics added to this area are saved and will stay persist when you return or share the results.

## Default metrics
By default, the comparison area contains VUs, response time and check failures. These three metrics are aggregated and represents the whole test run. Toggle them on and off by clicking the name - just like in the breakdown.

- Check failures: contains a summary of all checks to see when in the test the most checks fail.
- VUs: show the number of virtual users executing your load test simultaneously.
- Response time: show the aggregated response time of all the requests.

![Insights: Analysis section default metrics]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-analysis-section-default-metrics.png)

## Added metrics
Below the comparison area you can find the metrics added from the breakdown. You can either compare them to each other by adding them to the comparison area or by hovering the chart.

![Insights: Analysis section added metrics]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-analysis-tab/insights-add-metrics.png)

To add a metric to the comparison area click the `+` icon in the top right for that metric.

![Insights: Analysis section how to add a metric]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-analysis-tab/insights-analysis-section-howtoadd-metric.png)

Once added the metric will appear in the comparison area and you can compare it to the other metrics.

![Insights: Analysis section metrics comparison]({{ site.baseurl }}/assets/img/v4/result-analysis/insights-analysis-tab/insights-analysis-section-metrics-compare.png)

***
