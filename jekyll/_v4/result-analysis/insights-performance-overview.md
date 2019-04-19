---
layout: classic-docs
title: Insights performance overview
description: Documentation for the Insights performance overview
categories: [result-analysis]
order: 1
redirect_from: /knowledgebase/articles/1172467-insights-performance-overview
---

***

<h1>Background</h1>

The performance overview section contains a timeline of the VUs ramping, aggregated response time and requests per second. The [LoadImpact Performance Alert algorithms]({{ site.baseurl }}{% link _v4/core-concepts/performance-alerts-smart-results.md %}) will also poulate here, if issues are detected. While our Performance Alerts are designed to help save you time from having to dig too deeply into results. If no issues are detected, we will display a green check with some high level metrics from your test.  A green check only means our algorithms have not detected any patterns related to performance issues. You should still dig into your data, as required.

## Performance Overview Considerations

As the test runs, the graph will populate and resize and change it's scaling based on the data returned. The data you see in this section can be your first hint of a potential performance issues to investigate. When you look at this section, you should immediately look for trends in the data.  Here are some things to look for:

### Signals of a Good Result
- Response times trend flat during the entire test. Individual peaks/jagged graph lines can be okay, as long as the total trend is flat. Take note of the scaling to ensure you intepret the data correctly
- Request rate follows the same trend of Vitual Users.  As more VUs are added, we expect total requests to rise.  As they ramp down, we expect them to fall. **Note**: there are fringe cases where this won't be true, such as tests with long sleep times or if you stopped suddenly stopped using batch requests in your script

### Signals of a Performance Issue
- Response times trend upwards as the test is running.  This is a signal that the System Under Test is unable to handle the load at the point the trend starts to increase.
- Request rates does not increase at Virtual Users are added.  This is a signal that the System Under Test may be refusing connections, timing out, etc.


![Insights: Performance overview]({{ site.baseurl }}/assets/img/v4/result-analysis/performance-overview/performance-overview.png)

***

**Next**: [Insights: filters]({{ site.baseurl }}{% link _v4/result-analysis/insights-filters.md %})
