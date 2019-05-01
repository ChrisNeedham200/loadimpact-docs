---
layout: classic-docs
title: Checks
description: An overview of using checks
categories: [core-concepts]
order: 3
redirect_from: /4.0/test-scripting/checks/
---

***

<h2>Background</h2>

Checks are like asserts but differ in that they don't halt the execution of the test if a `false` result is encountered, instead the pass/fail result of the check is stored and the test continues executing.

***

## Example

Use checks to evaluate if functional pass/fail conditions are met during test execution. In our below example, we evaluate if a status code of 200 is returned:

{% highlight js linenos %}
import { check } from "k6";
import http from "k6/http";

export default function() {
  let res = http.get("http://test.loadimpact.com/");
  check(res, {
    "is status 200": (r) => r.status === 200
  });
}
{% endhighlight %}

**Note:** In order to utilize custom metrics, you MUST `import { check } from "k6";` within the init context of your script.

***

## Best Practice Example: Failing a test using thresholds
Checks on their own can' t fail a test, you need to combine them with [custom metrics]({{ site.baseurl }}/4.0/core-concepts/custom-metrics/) and [thresholds]({{ site.baseurl }}/4.0/core-concepts/thresholds/) to specify failure criteria.

<div class="callout callout-warning" role="alert">
    A failed test for Locally Executed tests means the exit code of the k6 process will be non-zero, and in the case of a <a href="/4.0/guides/cloud-execution/" class="alert-link">Cloud Executed</a> test it means that the test will get a `Failed by thresholds` status.
</div>

In the below example we set a threshold that specifies we accept a maximum 10% check failure rate before considering the test a failure:

{% highlight js linenos %}
import http from "k6/http";
import { check } from "k6";
import { Rate } from "k6/metrics";

export let errorRate = new Rate("errors");

export let options = {
    thresholds: {
        "errors": ["rate<0.1"],
   }
};

export default function() {
    let success = check(http.get("http://test.loadimpact.com"), {
        "status is 200": (r) => r.status == 200
    });
    if (!success) {
        errorRate.add(1);
    }
};
{% endhighlight %}

See also:
- [k6 docs on checks](https://docs.k6.io/docs/checks)
- [Custom Metrics]({{ site.baseurl }}/4.0/core-concepts/custom-metrics/)
- [Thresholds]({{ site.baseurl }}/4.0/core-concepts/thresholds/)
