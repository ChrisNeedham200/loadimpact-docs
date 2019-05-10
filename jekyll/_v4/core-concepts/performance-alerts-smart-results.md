---
layout: classic-docs
title: Performance Alerts
description: Explanation of the different Performance Alerts available in LoadImpact Insights. This includes alerts related to test health and ones based on our Smart Result algorithms.
categories: [core-concepts]
order: 13
redirect_from: /4.0/result-analysis/insights-smart-results/
---

***

<h2>Background</h2>

LoadImpact's Performance Alerts are algorithms built into LoadImpact Insights. Our algorithms are automatically executed as part of test result processing when you stream your results into our cloud service or execute your test on our cloud infrastructure. These alerts can be broken down into two categories Test Health Performance Alerts and Smart Result Performance Alerts.

- TOC
{:toc}


## Smart Result Performance Alerts

Smart Result Performance Alerts are alerts that intelligently analyze your results and will alert you to patterns that we know are related to performance issues on the system under test. Their purpose is to help highlight problem areas so you can spend more time fixing performance problems than analyzing results.

### Throughput Limit

This alert is raised when a throughput limit has been detected. The number of active in-flight requests continue to grow as the number of Virtual Users are increasing, while the request rate (finished requests) has flatlined. This is indicative that the system under test is overloaded, thus resulting in higher response times. We recommend that you correlate the performance bottleneck with data from an APM and/or server monitoring tool. After making changes, you should run your tests again at the same Virtual User level, to verify if your changes have improved performance on the system under test.

![Throughput limit example ]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/throughput-limit.png)

***

### Increased HTTP failure rate

This alert is raised when a period of elevated HTTP errors has been detected (10% higher than in the beginning of the test). There could be a number of reasons for this, e.g. web server configuration (timeouts, rate limiting etc.) or internal errors caused by saturation of a resource (CPU, memory, disk I/O or database connections). It typically means the target system is close to its performance limit.

**Note:** Failed responses are often returned much faster than successful responses. Consequently, an increased HTTP error rate may produce misleading request rate and response time metrics.

![Increased HTTP failure rate]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/too-many-http-failures.png)

***

### High HTTP failure rate

The total number of HTTP(s) errors is higher than 15% during the first 100 completed script iterations.

Errors that occur early on are typically not considered to be performance related. Our algorithms also have not detected an increase in the error rate as load has increased.
With that in mind, there are a number of non-performance related reasons for errors, which includes, but is not limited to:

- You're making invalid requests:
  - Invalid URLs, eg. with a typo in it or a hostname that is not in the public DNS system.
  - Missing required headers, eg. authentication/authorization headers or user-agent.
  - Sending the wrong body data.
- You're trying to test a system that's behind a firewall.
- You're hitting a rate limit.

**Note:** Failed responses are often returned much faster than successful responses.

### Not Enough Training Data

This alert is raised because our Smart Results algorithms need at least 100 complete VU iterations of training data plus an additional 15 seconds to produce meaningful output. Your test did not complete the 100 VU iterations necessary for the training data. We recommend increasing the test duration to get the full benefits of performance alerts.

Sample result: <a href="https://app.loadimpact.com/k6/anonymous/1026ecd031c0481eaaed1bb4312f2509" target="_blank">Not enough training data</a>

***

## Test Health and Informational Performance Alerts

Test Health Performance Alerts are alerts that intend to highlight test or script related issues. These issues, if not addressed, can either skew your results or make result analysis harder to parse through. These alerts are often quickly solved through changes in the test script or test configuration.

**Important**: The `Third Party Content` and `Too Many URLs` alerts are more informational alerts. Depending on what you are testing, it may be appropriate to disregard these alerts. High CPU or Memory usage **should never** be ignored.

***

### Third Party Content

**This is an informational alert, we strongly recommend you remove third party requests from your test**

This alert is raised when we detect many different domains in a test. This is typically caused by your test script containing requests to 3rd party resources such as CDNs, social media scripts, analytic tools, etc. It's recommended to remove third party requests as it may violate the terms of service of that third party, that third party may throttle your requests skewing the percentiles of your results, or you may have no ability to impact performance of that third party.

*Special Notes:*
- You may have a valid reason to test your CDN. Most CDNs charge based on usage so your tests could result in additional costs from your CDN.
- Your system under test may utilize multiple domains, in which case you can ignore this alert.
Refer to: [Why should I filter domains?]({{ site.baseurl }}/4.0/frequently-asked-questions/why-should-i-filter-domains/) for more information.

***

### Too Many URLs

**This is an informational alert, we strongly recommend you group dynamic URLs as it will make analysis easier**

This alert is raised when we detect more than 500 unique URLs in your test results. This is commonly caused by a URL that contains a query parameter that is unique per iteration. e.g. tokens, session IDs, etc. You should utilize the <a href="https://docs.k6.io/docs/http-requests#section-aggregating-results-for-http-requests" target="_blank">URL grouping feature of k6</a> to aggregate data from dynamic URLs into a single URL metric.

In the following example, our query parameter would produce large number of URL metrics:

{% highlight js linenos %}
for (var id = 1; id <= 600; id++) {
  http.get(`http://test.loadimpact.com/?ts=${id}`);
}
{% endhighlight %}
![Too Many URLs - Dynamic URLs]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/insights-no-url-grouping.png)

But you can group all these URL metrics together in our result analysis using the <b>name tag</b>, making it easier for you to interpret the data.

{% highlight js linenos %}
for (var id = 1; id <= 600; id++) {
  http.get(`http://test.loadimpact.com/?ts=${id}`, {tags: {name: 'test.loadimpact.com?ts'}});
}
{% endhighlight %}
![Too Many URLs - Group Dynamic URLs]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/insights-url-grouping.png)

*Note:* In some cases, the unique URL may be generated by a third party resource. As mentioned in the <a href="#third-party-content">Third Party Content alert</a>, it's a best practice to not include third party resources in your test scripts.

***

### Too Many Groups

**This is an informational alert, we recommend reviewing how you use the <a href="https://docs.k6.io/docs/group-name-fn-cond" target="_blank">group API</a> in your test script.**

This alert is raised when we <b>detect a high number of group</b> in your test script.  The most common reason for this alert is an incorrect usage of the <a href="https://docs.k6.io/docs/group-name-fn-cond" target="_blank">group API</a> using it for aggregating different HTTP requests, or within a loop statement.

Groups are meant to organize and provide an overview of your result tests allowing you a BDD-style of testing. By allowing this organization, you can quickly find specific parts of your test. For example, the point where users are on a certain page or taking specific actions.

{% highlight js linenos %}
import { group } from "k6";

export default function() {
  group("user flow: returning user", function() {
    group("visit homepage", function() {
      // load homepage resources
    });
    group("login", function() {
      // perform login
    });
  });
};
{% endhighlight %}

If you want to group multiple HTTP requests, we suggest you use the <a href="https://docs.k6.io/docs/http-requests#section-aggregating-results-for-http-requests" target="_blank">URL grouping feature of k6</a> to aggregate data into a single URL metric.

***

### High Load Generator CPU Usage

This alert is raised when we detect high utilization of the load generator CPU during a test. Over utilization of the load generator can skew your test results producing data that varies from test to test and unpredictably. Virtual Users will naturally try to execute as quickly as possible. The exact cause of over utilization can vary, but is likely due to one of the following reasons:

- Lack of sleep times in your scripts
  - Sleep times help with pacing and emulating real user behavior
- High RPS per VU
  - When testing API endpoints you may configure your test to aggressively request an endpoint.
- Large number of requests in a single request batch
  - Requests made in a request batch will be made in parallel up to the default or defined limits
- Large amounts of data are returned in responses resulting in high memory utilization
  - When the memory of the load generator reaches near total consumption, the garbage collection efforts of the Load Generator can cause increase CPU utilization.
- A JavaScript exception is being thrown early in VU execution. This results in an endless restart loop until all CPU cycles are consumed.

Possible fixes:
- Increase sleep times where appropriate
- Increase the number of VUs to produce less RPS per VU (thus the same total load)
- Utilize multiple load zones to spread VUs out across multiple regions

***

### High Load Generator Memory Usage

This alert is raised when we detect high utilization of load generator memory during a test. When memory is highly utilized in a test, it can result in some unexpected behavior or failures. It may also cause high CPU utilization as garage collection efforts consume more and more of the CPU cycles.

Possible fixes:
- Utilize the test option `discardResponseBodies` to throw away the response body by Default
  - Use `responseType:` to capture the responseBodies you may require

***

See also:
- [k6 Docs](https://docs.k6.io/docs)
- [Results Analysis]({{ site.baseurl }}/4.0/result-analysis/)
- [Why should I filter domains?]({{ site.baseurl }}/4.0/frequently-asked-questions/why-should-i-filter-domains/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTk0Njg0ODc4XX0=
-->
