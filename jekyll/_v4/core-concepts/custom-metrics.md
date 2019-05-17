---
layout: classic-docs
title: Custom metrics
description: Custom metrics allow you to calculate and track non standard metrics in LoadImpact.
categories: [core-concepts]
order: 2
redirect_from: /4.0/test-scripting/custom-metrics/
---

***

<h2>Background</h2>

Custom Metrics allow you to track something that is not part of the standard metrics in LoadImpact.

The are four metric types in k6; `Counter`, `Gauge`, `Rate` and `Trend`. All four can be used when creating custom metrics. Below we provide examples of each and how they could be utilized within a test.

**Note:** In order to utilize custom metrics, you MUST `import { Counter, Gauge, Rate, Trend } from "k6/metrics";` within the init context of your script. You may remove any types you are not using in the specific test.

***

<h4>Try It!</h4>

Add any of the following samples to one of your scripts (or our _[in app sample](https://app.loadimpact.com/k6/tests/custom/editor)_).

- TOC
{:toc}

***


## Counter metrics

A metric that cumulatively sums added values.

{% highlight js linenos %}
import http from "k6/http";
import { Counter } from "k6/metrics";

let CounterErrors = new Counter("Errors");

export default function() {
    let res = http.get("https://test.loadimpact.com");
    let contentOK = res.html("h2").text().includes("Welcome to the LoadImpact.com demo site!");
    CounterErrors.add(!contentOK);
};
{% endhighlight %}

## Gauge metrics

A metric that stores the last value added to it.

{% highlight js linenos %}
import http from "k6/http";
import { Gauge } from "k6/metrics";

let GaugeContentSize = new Gauge("ContentSize");

export default function() {
    let res = http.get("https://test.loadimpact.com");
    GaugeContentSize.add(res.body.length);
};
{% endhighlight %}

## Rate metrics

A metric that tracks the percentage of added values that are non-zero.

{% highlight js linenos %}
import http from "k6/http";
import { Rate } from "k6/metrics";

let RateContentOK = new Rate("Content OK");

export default function() {
    let res = http.get("https://test.loadimpact.com");
    let contentOK = res.html("h2").text().includes("Welcome to the LoadImpact.com demo site!");
    RateContentOK.add(contentOK);
};
{% endhighlight %}

## Trend metrics

A metric that allows for calculating statistics on the added values (min, max, average and percentiles).

{% highlight js linenos %}
import http from "k6/http";
import { Trend } from "k6/metrics";

let TrendRTT = new Trend("RTT");

export default function() {
    let res = http.get("https://test.loadimpact.com");
    TrendRTT.add(res.timings.duration);
};
{% endhighlight %}

See Also:
- k6 docs on [custom metrics](https://docs.k6.io/docs/result-metrics#section-custom-metrics) for additional information.

**Next**: [Ramping configurations]({{ site.baseurl }}/4.0/core-concepts/load-test-ramping-configurations/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTkwMTQ4Njc5OV19
-->
