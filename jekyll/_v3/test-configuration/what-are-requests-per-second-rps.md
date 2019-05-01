---
layout: classic-docs
title: What are RPS (Requests per second)?
description: Explanation of requests per second, as it relates to LoadImpact.
categories: [test-configuration]
order: 3
redirect_from: /knowledgebase/articles/1148857-what-are-requests-per-second-rps
---

***

Requests Per Second, RPS, or r/s is a scalability measure characterizing the throughput handled by a system.

Using RPS when defining sizing and scale of load testing is common when testing APIs, in the same way that the number of concurrent visitors or VUs is the most common concept and metric when talking about websites or apps.

In the current version of LoadImpact, sizing of load tests are defined by VUs. Converting the scale or size of a load test from VUs to RPS depends on a number of factors such as response times, script processing complexity and VU concurrency factors. When testing single API endpoints it's typically more logical to think in terms of request per second rather than concurrent users. For this reason, we have developed a sample script that can be used to maximize efficiency of the Virtual Users when testing an API endpoint. We take advantage of the ability of a Virtual User being able to open multiple connections in parallel.

***

See also:
- [Virtual Users]({{ site.baseurl }}/3.0/test-configuration/what-are-virtual-users-vus/)
- [How to load test an API]({{ site.baseurl }}/3.0/how-to-tutorials/how-to-load-test-an-api/)
- [Load testing with Postman]({{ site.baseurl }}/3.0/integrations/load-testing-with-postman/)
- [User scenario scripting examples]({{ site.baseurl }}/3.0/user-scenario-scripting-examples/)
