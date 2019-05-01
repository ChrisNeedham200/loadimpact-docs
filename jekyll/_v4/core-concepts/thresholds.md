---
layout: classic-docs
title: Thresholds
description: An overview of using thresholds
categories: [core-concepts]
order: 4
redirect_from: /4.0/test-scripting/thresholds/
---

***

<h2>Background</h2>
This article explains Thresholds and how they are utilized within LoadImpact Version 4.0 and k6.

Thresholds are used to specify test pass/fail criteria. Thresholds can be set on all metrics including custom ones and you can limit the data point of a metric that are being included in the evaluation by scoping the threshold with tags.

**Note**: When using Thresholds, you want to stay within the defined values. Thresholds that evaluate to `false` will result in a failed test.


***


<h4>Try It!</h4>

Add the following sample to the options section of one of your scripts (or our _[in app sample](https://app.loadimpact.com/k6/tests/custom/editor)_). It will set a threshold at 500 ms for the 95th percentile of all requests. That is, the test will be marked as failed by threshold if the value is exceeded.


{% highlight js linenos %}
thresholds: {
        "http_req_duration": ["p(95)<500"]
},
{% endhighlight %}


***


- TOC
{:toc}



## Threshold expressions

Thresholds can be specified in a short or full format. The short format looks like this:

{% highlight js lineno %}
export let options = {
    thresholds: {
        metric_name: [threshold_expression_string],
    }
};
{% endhighlight %}

The full format is covered further down on this page.

A threshold expression is a snippet of JS code that is expected to evaluate to `true` or `false`. Every time a threshold is being evaluated k6 injects one or more variables into the JS context. There are four metric types in k6, and each metric type provides its own set of variables that are injected and which can be used in threshold expressions.

<table class="table table-striped">
  <thead>
    <tr>
      <th scope="col">Metric type</th>
      <th scope="col">Threshold expression variables</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>Counter</td>
      <td><code>count</code> and <code>rate</code></td>
    </tr>
    <tr>
      <td>Gauge</td>
      <td><code>value</code></td>
    </tr>
    <tr>
      <td>Rate</td>
      <td><code>rate</code></td>
    </tr>
    <tr>
      <td>Trend</td>
      <td><code>avg</code>, <code>min</code>, <code>max</code>, <code>med</code> and <code>p(N)</code> where <code>N</code> is a number between 0.0 and 100.0 meaning the percentile value to look at, eg. <code>p(99.99)</code> means the 99.99th percentile. The unit of these variables and functions are all in milliseconds.</td>
    </tr>
  </tbody>
</table>

## Standard metrics

{% highlight js lineno %}
import { sleep } from "k6";
import http from "k6/http";

export let options = {
    thresholds: {
        "http_req_duration": ["p(95)<500"],
    }
};

export default function() {
    http.get("https://test.loadimpact.com/");
    sleep(3);
};
{% endhighlight %}

## Custom metrics

Thresholds can also be set on [custom metrics]({{ site.baseurl }}/4.0/core-concepts/custom-metrics/). The variable referred to in the threshold expression varies between the different custom metrics as you'll see below.

### Counter metric

To set a threshold on a `Counter` metric you:

1. Create the custom metric and give it a name
2. Specify the threshold condition for the `Counter` metric by referring to the `count` variable
3. Add data points to the custom metric using the `add()` method

{% highlight js lineno %}
import http from "k6/http";
import { Counter } from "k6/metrics";

let CounterErrors = new Counter("Errors");

export let options = {
    thresholds: {
        "Errors": ["count<100"]
    }
};

export default function() {
    let res = http.get("https://loadimpact.com");
    let contentOK = res.html("h1").text().includes("LoadImpact");
    CounterErrors.add(!contentOK);
};
{% endhighlight %}

### Gauge metric

To set a threshold on a `Gauge` metric you:

1. Create the custom metric and give it a name
2. Specify the threshold condition for the `Gauge` metric by referring to the `value` variable
3. Add data points to the custom metric using the `add()` method

{% highlight js lineno %}
import http from "k6/http";
import { Gauge } from "k6/metrics";

let GaugeContentSize = new Gauge("ContentSize");

export let options = {
    thresholds: {
        "ContentSize": ["value<4000"],
    }
};

export default function() {
    let res = http.get("https://loadimpact.com");
    GaugeContentSize.add(res.body.length);
};
{% endhighlight %}

### Rate metric

To set a threshold on a `Rate` metric you:

1. Create the custom metric and give it a name
2. Specify the threshold condition for the `Rate` metric by referring to the `rate` variable
3. Add data points to the custom metric using the `add()` method

{% highlight js lineno %}
import http from "k6/http";
import { Rate } from "k6/metrics";

let RateContentOK = new Rate("Content OK");

export let options = {
    thresholds: {
        "Content OK": ["rate>0.95"],
    }
};

export default function() {
    let res = http.get("https://loadimpact.com");
    let contentOK = res.html("h1").text().includes("LoadImpact");
    RateContentOK.add(contentOK);
};
{% endhighlight %}

### Trend metric

To set a threshold on a `Trend` metric you:

1. Create the custom metric and give it a name
2. Specify the threshold condition for the `Trend` metric by referring to one or more of the variables `avg`, `min`, `max`, `med` and `p(N)` (0<=N<=100)
3. Add data points to the custom metric using the `add()` method

{% highlight js lineno %}
import http from "k6/http";
import { Trend } from "k6/metrics";

let TrendRTT = new Trend("RTT");

export let options = {
    thresholds: {
        "RTT": [
            "p(99)<300",
            "p(70)<250",
            "avg<200",
            "med<150",
            "min<100",
        ],
    }
};

export default function() {
    let res = http.get("https://loadimpact.com");
    TrendRTT.add(res.timings.duration);
};
{% endhighlight %}

## Aborting a test using thresholds

By default thresholds don't abort a test before the total duration or iterations have completed. But you can choose to abort the test, if a threshold is hit in the middle of the test:

{% highlight js lineno %}
export let options = {
    thresholds: {
        metric_name: [ { threshold: string, abortOnFail: boolean, delayAbortEval: string }, ... ],
    }
};
{% endhighlight %}

<table class="table table-striped">
  <thead>
    <tr>
      <th scope="col">Name</th>
      <th scope="col">Type</th>
      <th scope="col">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>threshold</td>
      <td><code>string</code></td>
      <td>This is the JS threshold expression string specifying the threshold condition to evaluate.</td>
    </tr>
    <tr>
      <td>abortOnFail</td>
      <td><code>boolean</code></td>
      <td>Whether to abort the test if the threshold is evaluated to false before the test has completed.</td>
    </tr>
    <tr>
      <td>delayAbortEval</td>
      <td><code>string</code></td>
      <td>If you want to delay the evaluation of the threshold for some time, to allow for more metric samples to be collected, you can specify the amount of time to delay using relative time strings like "10s", "1m" and so on.</td>
    </tr>
  </tbody>
</table>

See Also:
- k6 docs on [thresholds](https://docs.k6.io/docs/thresholds) for more information.


<<<<<<< HEAD:jekyll/_v4/core-concepts/thresholds.md
**Next**: [Tags]({{ site.baseurl }}/4.0/core-concepts/tags/)
=======
>>>>>>> f1a3e4eb10e61d3a842f8aed5ca3598aea065855:jekyll/_v4/core-concepts/thresholds.md
<!--stackedit_data:
eyJoaXN0b3J5IjpbNzQyMTE1MjEyXX0=
-->
