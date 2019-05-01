---
layout: classic-docs
title: Analyze and Interpreting your results
description: Brief guide on interpreting your load or performance test results in LoadImpact.
categories: [quick-start-guide]
order: 4
---

***

Interpreting [test results]({{ site.baseurl }}/3.0/test-results/how-do-i-interpret-results/) can be tricky, but there are some general guidelines that you should consider when viewing your results.

The *Vitual Users active* graph and the *Virtual User Load Time (VU Load Time)* graph are default graphs displayed on the result page.
### Simple Reminders:

- Look out for overall chart trends — is it trending upward/downwards, linear or exponential?.
- The VU Load Time represents how much time took all the HTTP transactions in a user scenario, and its graph gives a quick indication of how the target site handled the load during the test.
- VUs active graph show you the number of active simulated clients (simulated virtual users) at any point during the test - what we also sometimes call the “load level”.
- The optimum page load time is less than one minute. Ideally, it should be between 3-8 seconds
- Regardless of the type of graph you get, look out for the failed requests count. Failed requests indicate that you’re not getting any useful content back from the server: visible in the URL list with response code such as, 404
- Load testing is an experimental process, and should be planned and executed methodically. Testers should change only a single parameter for each load test, whether it's the type or mix of scripts, server configuration, test dataset, or other factor

### Example results 1:

![example 1]({{ site.baseurl }}/assets/img/v3/quick-start-guide/analyze-interpret-results/analyze-interpret-results-1.png)


- Green graph shows the Number of VUs active increasing.
- Blue graph shows VU Load Time increasing exponentially.
- If there was significant slow down in how fast the site responded to HTTP requests, it will be visible in the VU Load Time graph. It will show steadily increasing values as the load level increased.

### Example results 2:
![example 2]({{ site.baseurl }}/assets/img/v3/quick-start-guide/analyze-interpret-results/analyze-interpret-results-2.png)


- Green graph shows the Number of VUs active
- Blue graph shows VU Load Time unaffected by the increasing load


A fairly common result is the completely flat VU Load Time graph. Depending on your objective for running the test in the first place, this can be either a good thing or a bad thing — it depends on your objectives:


**Example test objective 1: The load test**

If your goal is to test your site’s capability of handling 1,000 users and you get a flat graph without any errors and with reasonable response times, it suggests that the site can really handle 1,000 users.

**Example test objective 2: The stress test**

If, on the other hand, you are testing to determine the extreme performance limits of a site (how much load it can take before breaking) and you get a flat graph, you will probably need to run another test with more simulated users, as this implies there’s not enough stress being applied to the servers.


### Example results 3:
![example 3]({{ site.baseurl }}/assets/img/v3/quick-start-guide/analyze-interpret-results/analyze-interpret-results-3.png)


- Green graph shows the Number of VUs active
- Blue graph shows VU Load Time dipping after the first point, then increasing again
- It's not uncommon that VU Load time falls initially. This may be caused by DNS caching on the client side and/or content caching on the server side.

The ability to extrapolate information from load test results allows you to understand and appreciate what is happening within your system. Here are key factors to consider when analyzing load test results:
- Check Bandwidth
- Check load time for a single page rather than user load time
- Check load times for static objects vs. dynamic objects
- Check the failure rate
- For Server Monitoring: Check CPU and Memory usage status

At this point - you should have a basic understanding of how to create your use cases, include them in tests, run those tests and analyze the results.

[Next steps]({{ site.baseurl }}/3.0/quick-start-guide/next-steps)
