---
layout: classic-docs
title: Insights Performance Alerts
description: Explanation of the different Performance Alerts available in LoadImpact Insights. This includes alerts related to test health and ones based on our Smart Result algorithms.
categories: [result-analysis]
order: 6
---

***

<h1>Background</h1>

LoadImpact's Performance Alerts are algorithms built into Load Impact Insights. Our algorithms are automatically executed as part of test result processing when you stream your results into our cloud service or execute your test on our cloud infrastructure. These alerts can be broken down into two categories Test Health Performance Alerts and Smart Result Performance Alerts.

- TOC
{:toc}


## Smart Result Performance Alerts

Smart Result Performance Alerts are alerts that intelligently analyze your results and will alert you to patterns that we know are related to performance issues on the system under test. Their purpose is to help highlight problem areas so you can spend more time fixing performance problems than analyzing results.

### Throughput Limit

This alert is raised when a throughput limit has been detected. The number of active in-flight requests continue to grow as the number of Virtual Users are increasing, while the request rate (finished requests) has flatlined. This is indicative that the system under test is overloaded, thus resulting in higher response times. We recommend that you correlate the performance bottleneck with data from an APM and/or server monitoring tool. After making changes, you should run your tests again at the same Virtual User level, to verify if your changes have improved performance on the system under test.

Sample Result: [Throughput Limit](https://app.loadimpact.com/k6/anonymous/1333df36a62848a7add1636c6cb99b46)
![Throughput limit example ]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/throughput-limit.png)

***

### Too Many HTTP Failures

This alert is raised when a period of elevated HTTP errors has been detected (10% higher than in the beginning of the test). There could be a number of reasons for this, e.g. web server configuration (timeouts, rate limiting etc.) or internal errors caused by saturation of a resource (CPU, memory, disk I/O or database connections). It typically means the target system is close to its performance limit.

**Note:** Failed responses are often returned much faster than successful reponses. Consequently, an increased HTTP error rate may produce misleading request rate and response time metrics.

Sample Result: [Too many HTTP failures](https://app.loadimpact.com/k6/anonymous/82e943f60e9e471cbe0343497748f6aa)
![Too many HTTP failures]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/too-many-http-failures.png)

***

### Not Enough Training Data

This alert is raised because our Smart Results performance alert algorithms need at least 100 complete VU iterations of training data plus an additional 15 seconds to produce meaningful output. Your test did not complete the 100 VU iterations necessary for the training data. We recommend increasing the test duration to get the full benefits of performance alerts

Sample result: [Not enough training data](https://app.loadimpact.com/k6/anonymous/1026ecd031c0481eaaed1bb4312f2509)
![Not enough training data example]({{ site.baseurl }}/assets/img/v4/result-analysis/smart-results/not-enough-training-data.png)

***

## Test Health Performance Alerts

Test Health Performance Alerts are alerts that intend to highlight test or script related issues. These issues, if not addressed, can either skew your results or make result analysis harder to parse through. These alerts are often quickly solved through changes in the test script or test configuration.

**Important**: The `Third Party Content` and `Too Many URLs` alerts are more informational alerts. Depending on what you are testing, it may be appropriate to disregard these alerts. High CPU or Memory usage **should never** be ignored.

***

### Third Party Content

This alert is raised when we detect more than 3 different domains in a test. This is typically caused by your test script containing requests to 3rd party resources such as CDNs, social media scripts, analytic tools, etc. It's generally recommended to remove third party requests as it may violate the terms of service of that third party, that third party may throttle your requests skewing the percentiles of your results, or you may have no ability to impact performance of that third party.

*Special Notes:*
- You may have a valid reason to test your CDN. Most CDNs charge based on usage so your tests could result in additional costs from your CDN.
- Your system under test may utilize multiple domains, in which case you can ignore this alert.
Refer to: [Why should I filter domains?]({{ site.baseurl }}/4.0/frequently-asked-questions/why-should-i-filter-domains/) for more information.

***

### Too Many URLs

This alert is raised when we detect more than 500 unique URLs in your test results. This is commonly caused by a URL that contains a query parameter that is unique per iteration. e.g. tokens, session IDs, etc. You should utilize the URL grouping feature of k6 to combine these unique URLs into a single metrics. You can

*Note:* In some cases, the unique URL may be generated by a third party resource. As mentioned in the Third Party Content alert, it's a best practice to not include third party resources in your test scripts.

In the following example, our query parameter would produce large number of unique URLs, the name tag would group these all together in our result analysis, making it easier for you to interpret the data.

{% highlight js linenos %}
http.get("http://test.loadimpact.com/?ts=" + Math.floor(Math.random() * Math.floor(1000)),
          {tags: {name: 'test.loadimpact.com'}});
{% endhighlight %}

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
eyJoaXN0b3J5IjpbLTc0MTM5ODMyNl19
-->