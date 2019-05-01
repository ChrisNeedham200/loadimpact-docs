---
layout: classic-docs
title: What are VUs (Virtual Users)?
description: Definition of what Virtual Users are in context of LoadImpact.
categories: [core-concepts]
order: 0
redirect_from:
  - /knowledgebase/articles/174260-what-are-virtual-users-vus
  - /3.0/test-configuration/what-are-virtual-users-vus/
  - /4.0/getting-started/what-are-virtual-users/
---

***

<h2>Background</h2>

LoadImpact's definition of Virtual Users(VUs) along with supplemnental information to cover common questions about VUs.

***


Virtual Users(VUs) are the entities in LoadImpact that execute your test script and make HTTP(s) or websocket requests. VUs are concurrent and will continously iterate through the default function until they ramp down or the test ends.  Any number of VUs will create a number of sessions a factor larger than their total count, depending on test and script length.

For example, if you ran a test with 10 VUs for 10 minutes and the default function took each VU 30 seconds to complete, you would see roughly 200 completions/total sessions generated from this test. This is approximate and will vary based on your ramping configuration.

## When testing Web apps or Websites

Virtual Users are designed to act and behave like real users/browsers would. That is, they are capable of making multiple network connnections in parallel, just like a real user in a browser would. When using a `http.batch()` request, HTTP requests are sent in parallel.  Further, you can even control the specifics of this behavior through the [batch]({{ site.baseurl }}/4.0/reference/test-configuration-options/#batch) and [batchPerHost]({{ site.baseurl }}/4.0/reference/test-configuration-options/#batchPerHost) options. The default is 10 connections in parallel and per host.

When using our [Chrome Extension]({{ site.baseurl }}/4.0/how-to-tutorials/load-impact-version-4-chrome-extension/) or [converting a HAR file]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/), all requests made within 500 ms of one another will be placed into a `http.batch()`.

## When testing APIs
When testing individual API endpoints, you can take advantage of each VU making multiple requests each to produce requests per second(rps) a factor higher than your VU count.  e.g. Your test may be stable with each VU making 10 rps each. If you wanted to reach 1000 RPS, you may only need 100 VUs in that case. This will vary based on what you are testing and the amount of data returned. For more information on testing APIs, please refer to our article [How to Load Test an API]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-load-test-an-api-with-k6/)

***

Because Virtual Users are using multiple parallel network connections, they will be opening multiple concurrent network connections and transferring resources in parallel. This results in faster page loads, more stress on the target server, and more realistic result data set. **Not all load testing tools operate in this more complex and realistic fashion**

## Calculating the number of Virtual Users needed

Calculating the number of virtual users can be done by using this formula:

` VUs = (hourly sessions * average session duration in seconds)/3600`

You may want to use this formula multiple times, with different data such as sessions in your busiest/peak hour, a normal hour, etc. When setting up your test, if your user journey (what's in the default function) mimics an average user session, the number of VUs determined by the formula would produce the amount of sessions you entered if you ran your test for an hour.

Further, We wrote this [blog post](http://blog.loadimpact.com/blog/monthly-visits-concurrent-users/) that uses Google Analytics as an example source of this information.

***

## See also

- [Batch requests](https://docs.k6.io/docs/batch-requests)
- [Chrome Extension]({{ site.baseurl }}/4.0/how-to-tutorials/load-impact-version-4-chrome-extension/)
- [Converting a HAR file]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/)
- [Test configuration options]({{ site.baseurl }}/4.0/reference/test-configuration-options/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbNTg0NDg5MDM1XX0=
-->
