---
layout: classic-docs
title: What metrics are reported back for a URL?
description: Explanation on the metrics LoadImpact reports back on URLs in a load test
categories: [test-results]
order: 3
redirect_from: /knowledgebase/articles/174136-what-metrics-are-reported-for-a-url
---

***

For each URL in a test, LoadImpact reports what status codes the tested server has responded with. For each of those status codes, the max, min, and average response time, and the count of each response with that particular status code per URL.

On the test results page, there is an tab “URLs” where you see a list of every URL included in the test, and some statistics about the URL. This is how it looks:

![URL Tab]({{ site.baseurl }}/assets/img/v3/test-result/what-metrics-are-reported-for-a-url/url-tab.png)

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

**Note**: Among the status codes you will normally not see any 3xx redirects, as the default behavior is to follow a redirect and then report the transaction time as the time from when the first (redirected) request was made and until the final (non-redirected) transaction was completed. This behaviour can be overridden – refer to the `http.request` method and the `http.request_batch` method in our [Load Script API](https://loadimpact.com/load-script-api).


### More about results metrics

Response times, and other metrics, are measured continually throughout the test, in 3-second intervals. The load generator will record HTTP transaction times for each individual URL and every 3 seconds it reports the maximum, minimum and average transaction time seen for that URL during the most recent 3-second interval.

These values are stored in the results database, and downloaded by a client-side JavaScript when you open your test result (or, in the case of a running test, results are downloaded incrementally as they are being generated). The JavaScript code in your browser has access to all the individual data samples, but the graph component will aggregate data when necessary, in order to generate decent-looking graphs. The aggregation level depends on what zoom level you set your chart at. The chart can display up to about 170 points in a graph, which means that a 5-minute test (300 seconds) that has about 100 individual data samples (one sample every 3 seconds) can be shown in its entirety with no aggregation, while a 10-minute test will not quite fit into the 170 points but has to be slightly aggregated.

Currently these metrics are stored once every 3 seconds during a test, and can be graphed:

Metric                            | Description                                                                                                      | Tagged with [2]
----------------------------------|:------------------------------------------------------------------------------------------------------------------:|:--------------------------------------------------:
Maximum transaction time          | Highest transaction time seen during 3-second sample period                                                      | URL, HTTP response code, load zone, user scenario
Minimum transaction time          | Lowest transaction time seen during 3-second sample period                                                       | URL, HTTP response code, load zone, user scenario
Average transaction time          | Average transaction time seen during 3-second sample period                                                      | URL, HTTP response code, load zone, user scenario
Number of transactions            | Number of transactions seen during 3-second sample period                                                        | URL, HTTP response code, load zone, user scenario
User load time                    | Approximate load time as experienced by a user \[3]                                                               | load zone
Accumulated                       | load time	Sum of all individual transaction times during period                                                   | load zone
Bandwidth usage                   | Average bandwidth usage during 3-second sample period                                                            | load zone
HTTP requests/second              | Average HTTP requests per second issued during 3-second sample period                                            | load zone
Failure rate                      | Number of failed HTTP transactions per second during 3-second sample period                                      | load zone
Clients active                    | Number of concurrent, simulated clients active at the end of the 3-second sample period                          | load zone
Connections active                | Number of concurrent TCP connections used at the end of the 3-second sample period                               | load zone
Load generator memory utilization | How large a percentage of system memory the load generator application uses on the load generator hosts          | load zone
Load generator CPU utilization    | How large a percentage of system CPU is being used by the load generator application on the load generator hosts | load zone
{: class="table table-bordered"}



[2] A result metric being tagged with something means that you can easily separate data based on the tag. I.e. if “Bandwidth usage” is tagged with “load zone” and you have executed a load test using the Tokyo and the Dublin load zones, it means you can plot one bandwidth graph displaying the bandwidth usage between your web server and Tokyo, and another graph displaying the bandwidth usage between your web server and Dublin. Transaction times are tagged with several things, which enables very advanced comparisons – i.e. you can plot a graph showing the maximum response times for the URL “http://www.dom.ain/index.html” when accessed by the Tokyo load zone alongside with the average reponse times for URL “http://www.dom.ain/index2.html” when accessed by the Dublin load zone.

[3] User load time is the average time it has taken user scenarios (any user scenarios) to run to completion. Note that only time spent actively loading things is counted here – sleep statements in a load script are excluded. A user scenario will commonly take a lot more than 3 seconds to complete, however, so the data for each 3-second period will consist of the user scenario load times that were reported during this 3-second period (i.e. the actual user scenario execution would have happened earlier).

See Also:
- [What Status codes are reported back for a URL?]({{ site.baseurl }}/3.0/test-results/what-status-codes-are-there/)
