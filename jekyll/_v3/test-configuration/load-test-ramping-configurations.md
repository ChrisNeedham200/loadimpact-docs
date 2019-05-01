---
layout: classic-docs
title: Ramping configurations
description: Common load test ramping configuration examples and what you can learn from using them
categories: [test-configuration]
order: 9
redirect_from: /knowledgebase/articles/945337-different-types-of-ramping-configurations
---

***

There are countless ways to configure tests in LoadImpact. Depending on how you configure your ramping, it will provide different test profiles that can help you learn different things. Running different types of tests will provide different types of results so you can take different types of actions.

Here are some examples of tests you may run. This isn't meant to be an all inclusive list, but some of the most common test ramping configurations used. You should adjust values to suit your specific need.

- TOC
{:toc}

#### Baseline test

The first test you should run, almost always. It is ssed to determine what performance is like under ideal conditions and sets a baseline to compare future tests against. After your initial testing you may want to run **Baseline Tests** on a very regular basis to monitor performance without impact your systems too much. You may increase VUs slightly in this case, based on your needs.

![Baseline Test]({{ site.baseurl }}/assets/img/v3/test-configuration/load-testing-ramping-configurations/baseline-test.png)


#### Stress test

This test is designed to help narrow down where performance starts to breakdown. The stability of Virtual Users after quick growth will help highlight if performance issues occur at that level. It configures ramp ups that aren't too steep, which might cause servers to experience a **spike test**. It can be one step or several, just make sure you overreach and try for more than you expect your system can handle. In most cases, you should expect to iterate this test multiple times.

![Stress Test]({{ site.baseurl }}/assets/img/v3/test-configuration/load-testing-ramping-configurations/stress-test.png)


#### Load test

Used to evaluate if performance goals are met. Set the thresholds and limits for the goals you target with your test. After running multiple **Stress Tests** you would use a **Load Test** to verify all your changes are working. You may continue to run **Load Tests** on a regular basis to monitor performance over time.


![Load Test]({{ site.baseurl }}/assets/img/v3/test-configuration/load-testing-ramping-configurations/load-test.png)


#### Spike test

Sudden spike in traffic from normal levels that may last for any amount of time. If you were running an ad campaign during the next big game you would really like to make sure you can handle the amount of users coming in a very short amount of time. The defining characteristic is a very short ramp up time.

![Spike Test]({{ site.baseurl }}/assets/img/v3/test-configuration/load-testing-ramping-configurations/spike-test.png)



#### Soak test

Used to find problems that arise when a system is under pressure for extended periods of time. Run for longer duration and is used to find long term problems such as memory leaks, resource leaks or corruption and degradation that occurs over time. Run these at roughly 80% of your load testing goals. Not at max capacity.

![Soak Test]({{ site.baseurl }}/assets/img/v3/test-configuration/load-testing-ramping-configurations/soak-test.png)
