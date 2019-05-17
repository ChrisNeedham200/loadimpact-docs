---
layout: classic-docs
title: Structure of a Test Script
description: "A breakdown of the different parts of a test script: itialization, setup, virtual user, and teardown code."
categories: [core-concepts]
order: 8
redirect_from: /4.0/test-scripting/test-structure-breakdown/
---

***

<h2>Background</h2>

Explanation of the breakdown of the different parts of a test script, and what they are used for.

***


A k6 test script can be broken down into four distinct life-cycle stages.

This can be illustrated by looking at a skeleton of k6 test:
{% highlight js linenos %}
// 1. init code

export function setup() {
    // 2. setup code
}

export default function(data) {
    // 3. vu code
}

export function teardown(data) {
    // 4. teardown code
}
{% endhighlight %}

It can also be visualized like this:

<p class="text-center"><img src="{{ site.baseurl }}/assets/img/v4/test-scripting/v4-k6-execution-breakdown.svg" alt="k6 execution breakdown" width="400"></p>

## Init code

Init code is run once per VU, when the VU is initialized. This is the only place where interacting with files is allowed, via the [`open()`](https://docs.k6.io/docs/open-filepath-mode) API.

See the k6 docs on [init code](https://docs.k6.io/docs/test-life-cycle#section-init-and-vu-stages) for more information.

### VU code

The VU code is the code that contains the actual testing logic. This code is executed from top to bottom. If you have test duration that is longer than the time it takes to run through one iteration of the VU code it will loop, and start executing again from the top of this [default function]({{ site.baseurl }}/4.0/core-concepts/default-function) over and over until the test duration finalizes.

{% highlight js linenos %}
export default function(data) {

}
{% endhighlight %}

### Setup/teardown life-cycle hooks

The `setup()` and `teardown()` functions are life-cycle hooks that are run once pre and post test respectively. See article on [setup/teardown life-cycle hooks]({{ site.baseurl }}/4.0/core-concepts/test-setup-teardown-life-cycle) for more details.


{% highlight js linenos %}
export function setup() {

}

export function teardown(data) {

}
{% endhighlight %}