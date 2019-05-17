---
layout: classic-docs
title: Types of Load and Performance Tests
description: Definitions and examples of different types of load and performance tests and what information you can learn from them
categories: [core-concepts]
order: 1
redirect_from:
  - /4.0/test-scripting/load-test-ramping-configurations/
  - /4.0/core-concepts/load-test-ramping-configurations/
---

***

<h2>Background</h2>

Different types of performance tests serve to provide different information about the System Under Test(SUT). You can run different tests by altering the ramping patterns used in the test script. Ramping patterns can be directly defined in the global options of your k6 script. You may want to configure different ramping patterns based on what you want to learn from your test. The following examples are some of the most popular and commonly used configurations. You should adjust the Virtual Users and duration to fit your individual needs.


- TOC
{:toc}

#### Baseline test

Almost always the first test you should run. It is used to determine what performance is like under ideal conditions and sets a baseline to compare future tests against. After your initial testing you may want to run **Baseline Tests** on a regular basis, to monitor performance without impacting your systems too much. You may increase VUs slightly in this case, based on your needs.

{% highlight js linenos %}
export let options = {
  stages: [
    { duration: "1m", target: 10 },
    { duration: "10m", target: 10 },
    { duration: "1m", target: 0 }
  ],
};
{% endhighlight %}
#### Stress test

This test is designed to help narrow down where performance starts to break down. The stability of Virtual Users after quick growth will help highlight if performance issues occur at that level. It configures ramp ups that aren't too steep, which might cause servers to experience a **spike test**. It can be one step or several, just make sure you overreach and try for more than you expect your system can handle. In most cases, you should expect to iterate this test multiple times.

{% highlight js linenos %}
export let options = {
  stages: [
    { duration: "5m", target: 1000 },
    { duration: "10m", target: 1000 },
    { duration: "5m", target: 2000 },
    { duration: "10m", target: 2000 },
    { duration: "5m", target: 4000 },
    { duration: "10m", target: 4000 },
    { duration: "1m", target: 0 }
  ],
};
{% endhighlight %}

#### Load test

Used to evaluate if performance goals are met. Set the thresholds and limits for the goals you target with your test. After running multiple **Stress Tests** you would use a **Load Test** to verify all your changes are working. You may continue to run **Load Tests** on a regular basis to monitor performance over time.

{% highlight js linenos %}
export let options = {
  stages: [
    { duration: "5m", target: 2000 },
    { duration: "15m", target: 2000 },
    { duration: "5m", target: 0 }
  ],
};
{% endhighlight %}

#### Spike test

Sudden spike in traffic from normal levels that may last for any amount of time. If you were running an ad campaign during the next big game you would really like to make sure you can handle the amount of users coming in a very short amount of time. The defining characteristic is a very short ramp up time.

{% highlight js linenos %}
export let options = {
  stages: [
    { duration: "1m", target: 2000 },
    { duration: "9m", target: 2000 },
    { duration: "3m", target: 10000 },
    { duration: "7m", target: 10000 },
    { duration: "10m", target: 0 }
  ],
};
{% endhighlight %}


#### Soak test

Used to find problems that arise when a system is under pressure for extended periods of time. Run for longer duration and is used to find long term problems such as memory leaks, resource leaks or corruption and degradation that occurs over time. Run these at roughly 80% of your load testing goals. Not at max capacity.

{% highlight js linenos %}
export let options = {
  stages: [
    { duration: "10m", target: 1000 },
    { duration: "340m", target: 1000 },
    { duration: "10m", target: 0 }
  ],
};
{% endhighlight %}
