---
layout: classic-docs
title: How to Interpret Results
description: Introduction on understanding and interpreting your LoadImpact load and performance test results
categories: [test-results]
order: 1
---

***

When you run a load test using LoadImpact, you will always get a default results graph on the test result page. This graph will give you a high level estimate of how much time, as experienced by a user, it would take to load your API, webapp, or website.

In most situations, it is correct to say that if `VU Load Time` is flat the System Under Test(SUT) can handle the load comfortably. If you want to know where its limits are, you will need to increase the load/concurrent users. You have to look at the absolute load times of resources reported and decide what are acceptable values. Typically, most users have formal or informal SLAs for how their systems should respond.

#### Metrics reported in a test
`VU Load Time` is the “default” metric that gets shown in the default chart for every test started, along with the number of active clients. Below is an example of the default chart, showing `VU Load Time` as a blue graph and active clients as a green graph _(Colors may vary based on the metrics you plot)_

![Flat `VU Load Time` Graph]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/flat-vu-load-time.png)

`VU Load Time` tells you how much time it took the simulated clients to perform all the HTTP transactions in your user scenario. It measures the total time it takes Virtual Users to make all the HTTP(s) requests in a user scenario, ignoring any client.sleep(). When looking at `VU Load Time` you want to look for trends:

- Flat `VU Load Time` is generally a sign of stability
- Increasing `VU Load Time` is generally a sign of degrading performance

###  Common graph types/patterns
Below are some common patterns you may encounter and what to potentially take away from them.

##### 1. The flat graph

The chart below shows a green graph that displays number of active clients in a test, and a blue graph that shows user load time (response time) during the test. Despite increasing the load (the green graph) from 1 to 25 concurrent clients you can see that the blue graph doesn't change – it fluctuates slightly up and down but stays at the same average level regardless of the load level. This tells us that the system being tested is nowhere near stressed by the load it is subjected to. Substantially higher load levels would be required to see any kind of performance degradation.

![Flat `VU Load Time` Graph]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/flat-vu-load-time.png)

##### 2. The rising graph

Consider a test where the load level is ramped up from about 75 to 500 clients over the course of about 12 minutes (the test started at 0 clients but we have zoomed in the graph to show the most interesting parts). We can see that the blue graph showing response time is flat for a while, then it starts to increase at approx. 200 concurrent clients, and continues increasing linearly throughout the test.

From this graph we can deduce that demand for some resource is exceeding availability of that resource around 200 clients, causing a graceful performance degradation from that point onwards as every client has to share the scarce resource equally. Shortage of some system resources (e.g. network bandwidth) will cause this kind of graceful/linear performance degradation, while others will result in very fast, exponential performance degradation (system memory shortage, for instance). Note that graceful degradation usually becomes exponential once the shortage gets severe enough.

![Rising `VU Load Time` Graph]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/rising-vu-load-time.png)

##### 3. The exponential graph

This graph looks flat to begin with, but then it starts to rise, and keeps rising faster and faster as load increases. This is called exponential behavior, where every little bit of load that is added will cause an ever larger performance degradation. When you see this type of graph you know that you are not far away from the extreme limit of your system’s performance.

![Expoonential `VU Load Time` Graph]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/exponential-vu-load-time.png)

### Additional Metrics

Below the main graph, you have the ability to add additional result metrics. If you have any Server Monitoring Agents or created Custom Metrics they are available here.

**What other metrics should I consider?**

This varies greatly depeding on your testing goals. Here are some general things to keep in mind:

  - Load Generator CPU and memory
    - Good for a sanity check. Are these > 50%? If yes - you may be overutilizing the Load Gen and should consider configuration changes
  - Bandwidth
    - Typically you should expect bandwidth to grow with the number of Virtual users
    - If Bandwidth plauteaus as VUs grow, you may have a bottleneck
  - Failure rate
    - If you have a high failure rate it would suggest some type of problem
  - Server Monitoring Agents
    - If you have added them to your test config, what is happening to the SUT during the test?
  - Custom metrics
    - If you took the time to create them, you likely want to view them

### URL and Page Tabs

Once you have reviewed the main result metrics, you should consider the URL and Pages tabs.

#### URL Tab

The URL tab will show you how each resource requested during the test has performed. The tab is searchable and sortable for most test cases. You should look for unexpected statuses and large variances between min and max load time for individual resources. These two things suggest potential performance problems. You can use the three dots next to Avg to plot the resource to the main or small chart.

![URL Tab]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/url-tab.png)

Title| Definition
-|-
URL  |  Unique resource requested during the tests
Load Zone/User Scenario  | The name of the Load zone and User Scenario the request originated from
Method  | HTTP Verb used to make the request
Status  |  Status code returned
Count  | Count of how many times the resource was requested during the test
Size/Compressed  | Size of the resource
Min/Max  | Minimum and maximum response time for the resource
Avg  |  Average response time for this resource
{: class="table table-bordered"}

Also refer to:
- [What Metrics are reported for a URL?]({{ site.baseurl }}/3.0/test-results/what-metrics-are-reported-for-a-url/)
- [What Status codes are reported back for a URL?]({{ site.baseurl }}/3.0/test-results/what-status-codes-are-there/)

#### Pages Tab
If you have utilized `http.page_start()` and `http.page_end()` in your script, we will group and calculate all resources between those functions in your script on this tab. It's highly recommended to rename the default names to something contextual. i.e. `http.page_start("Page 1")` ---> `http.page_start("Homepage")`  This small adjustment will make your results a lot easier to read for yourself and others you share the results with.

The structure of the tab is similar to the URL tab. You should be looking for large variances between Min and Max load times for these pages. It's sometimes useful to start on the Pages tab to find the worst performing pages and then digging into the URL tab to find the exact request.

![Pages Tab]({{ site.baseurl }}/assets/img/v3/test-result/how-do-i-interpret-results/pages-tab.png)
