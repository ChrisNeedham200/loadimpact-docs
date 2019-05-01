---
layout: classic-docs
title: What are thresholds?
description: Explanation of LoadImpact's "Threshold" feature, which allows you to set binary pass/fail metrics for your load and performnace tests
categories: [test-configuration]
order: 4
redirect_from: /knowledgebase/articles/918699-thresholds
---

***

The threshold feature allows to set binary pass/fail criteria for a test.


Tests that meet or exceed the set criteria would be "Failed by Threshold". Individual Thresholds can also be set to automatically abort a running test. Both options would return non zero exit codes - useful when automating tests in a CI Pipeline.

You can define Thresholds based on:

- Virtual User Load Time
- Failure Rate.
- Page load time
- Custom metrics

When running your load tests, thresholds will be continuously evaluated, and they determine the final status of your load test.


Here are some examples of Thresholds that can be set:

- When total/user load time is higher than 1000ms.
- When the response time of a specific resource or a page is higher than expected.
- When a server response is different than expected.

Thresholds are configured on a per test basis in your [test configuration]({{ site.baseurl }}/3.0/test-configuration/what-is-a-test-configuration/):

![Threshold examples]({{ site.baseurl }}/assets/img/v3/test-configuration/thresholds/thresholds-1.png)

As in other testing disciplines, your own criteria defines a test as a success or failure. Thresholds provides this pass/fail behaviour for your load tests.
