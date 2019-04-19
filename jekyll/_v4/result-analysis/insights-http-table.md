---
layout: classic-docs
title: HTTP table
description: Documentation for the Insights URL table
categories: [result-analysis]
order: 5
redirect_from:
  - /knowledgebase/articles/1825249-insights-url-table
  - /4.0/result-analysis/insights-url-table/
---

***

<h1>Background</h1>

The HTTP Table in insights allows you to inspect individual HTTP requests made during your test. This table gives you a very granular view of all the requests happening during your test so you can dig into them individually, as required.

The HTTP-table will help you to:
- Get an overview of all the URLs tested in your test.
- Identify which URLs are returning good and/or bad status codes.
- Identify URLs which are returning large differences in min/max response times
- Sort the URLs by different column values.
- Filter the URLs by [tags](https://docs.k6.io/docs/tags-and-groups) to focus on specific things important to you.

![HTTP Table]({{ site.baseurl }}/assets/img/v4/result-analysis/http-table/http-table-overview.png)


## How is the information organized?

URLs are grouped by status code. This means that if the same URL returned two different statuses during the test, like 204 and 500, it will get two separate rows in the URL table. For example, if you make a GET request to a resource that starts failing during your test, you will see it listed twice. Once with a total count of 200 status codes and another with the total count of the failed status (e.g. 500 internal server error).

If you are using Groups in your test script, you can also view all requests organized by the individual groups.  You can switch between Group and list view by using the "View As" toggle on the upper right corner of the HTTP table.

![Toggle Views]({{ site.baseurl }}/assets/img/v4/result-analysis/http-table/http-table-toggle.png)

## What does each column represent?

Column | Defintion
-------|----------------------------------------------------------------------------------------
URL    | The individual resource requested. Tip: use tags on dynamic urls
METHOD | The HTTP method used to make the request
STATUS | Status Code returned for the request. Different status codes are counted separately
COUNT  | Count of how many times the requested URL returned with the status code for each method
MIN    | The minimum time to fully load the response
AVG    | The average time to fully load the response
STDDEV | Measure of how response time groups around the AVG. 68% of requests are +/- this value from the AVG response time.
P95    | 95th percentile response, 95% of requests performed better than this value
P99    | 99th percentile response, 99% of requests performed better than this value
MAX    | The maximum time to fully load the response
{: class="table table-bordered"}


## What should I look for in this table?

There are multiple approaches that can be taken when reviewing this table.  Depending on factors unique to you, you may find some pieces of data more interesting than others.  In general, we suggest reviewing the data in an order similar to this:

1. Sort by STATUS, do you have any requests that are reporting a large number of unexpected STATUS codes?
2. Sort by MAX, do you have any requests that are reporting back a large difference between MIN and MAX?
3. Compare your percentiles to the MAX. The closer they are to the MAX, the less likely the MAX is an outlier.
4. STDDEV can be used to also gain some hints.  A low STDDEV would mean that most requests return a similar response time. A higher STDDEV can suggest potential instability.
5. AVG on it's own is not useful, however when combined with STDDEV you can get an idea of what most users are experiencing.

### Standard Deviation Example
Let's take a request that is returning the following data: an average of 1s and a standard deviation of 100 ms. This tells us that most response times are between 900ms and 1.1s. To be more precise, roughly 68% of response times will fall in this range (and 95% would fall between 800ms and 1.2s, which is 2 standard deviations). Standard Deviation isn't the only value to consider when testing, but it can be helpful to identify bottlnecks.  Always consider other available data when analyzing your results.


## Additional Metrics Available Per URL
If you find a request is interesting, you may want to dig deeper into it.  By clicking on the row of the request, a graph will expand that plots response time for that URL during the test.  This can then be added as a graph on the Analysis tab by clicking `ADD THIS GRAPH TO THE ANALYSIS`

![HTTP Table URL Graph]({{ site.baseurl }}/assets/img/v4/result-analysis/http-table/http-table-graph.png)

Further, you may wish to view some different details about this request during the course of the test. Metrics, such as request rate, response timings, and response time % is also available for graphing/analysis:

![HTTP Table URL Graph]({{ site.baseurl }}/assets/img/v4/result-analysis/http-table/http-table-url-metrics.png)


## Other Tips for the HTTP Table

- [Remove third party requests]({{ site.baseurl }}/4.0/core-concepts/performance-alerts-smart-results/#third-party-content) from your test script. These can fill your URL table with unactioable data or even contaminate your larger result dataset
- [Utilize tags to group similar URLs]({{ site.baseurl }}/4.0/core-concepts/performance-alerts-smart-results/#too-many-urls) that contain dyanmic data, such as a session ID in a query parameter.
- If you have a large number of URLs that can't be grouped (or removed), sorting and/or filtering by tags comes in handy:

![HTTP Table Tags]({{ site.baseurl }}/assets/img/v4/result-analysis/http-table/http-table-tags.png)

***

**Next**: [Insights: Analysis Tab]({{ site.baseurl }}{% link _v4/result-analysis/insights-analysis-view.md %})
