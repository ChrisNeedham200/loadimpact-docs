---
layout: classic-docs
title: Scheduling Load Tests with LoadImpact
description: A guide on how to schedule your load tests to run in the future or on a schedule within the LoadImpact Web UI.
categories: [guides]
order: 3
redirect_from: /4.0/test-running/test-scheduling/
---

***

<h2>Background</h2>

It's not always feasible to be able to trigger a test to run when you need it.  The scheduling option in LoadImpact allows you to configure a test to execute at a particular time, and on a regular interval, if needed.

Some reasons include, but are not limited to:
- You need to test a production system and want to do it during hours with minimal usage
- You want to build a performance trend to monitor for regressions (but aren't ready to integrate it as a step in a CI Pipeline


## Scheduling

You have the ability to schedule any tests that currently exists in your LoadImpact account that has been executed on our cloud service (you can not schedule a locally run test through the web UI). You may also schedule tests that you create within the web UI after saving your configuration.

For scheduling a new test configuration, the option will appear after you click save.

![]({{ site.baseurl }}/assets/img/v4/test-running/test-scheduling/schedule-test-config.png)

Similarly, if your test has already run, you can schedule the test from the page with the performance trending graph.  This is helpful if you have triggered a cloud test from the command line and want to automatically run it regularly (without using a CI tool)

![]({{ site.baseurl }}/assets/img/v4/test-running/test-scheduling/schedule-existing-test.png)

## Scheduling options

In both cases, after clicking "Schedule" you are presented with the following options. You are able to run a test now or at a later date.  You can also set the execution to repeat on an Hourly, Daily, Weekly, or Monthly interval. You can also control how long the test will run for, either after a set number of occurences, or after a certain date.  There is some very granular control here, so do explore the option.

![]({{ site.baseurl }}/assets/img/v4/test-running/test-scheduling/schedule-options.png)

Finally - we do recommend setting up [notifications]({{ site.baseurl }}/4.0/guides/notifications/) to complete an automated loop.  Schedule your test to run, and get notified of the results/completion of your test.

***

See Also:
- [Notifications]({{ site.baseurl }}/4.0/guides/notifications/)

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMzI5OTE2OTldfQ==
-->
