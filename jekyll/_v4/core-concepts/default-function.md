---
layout: classic-docs
title: Default Function
description: Overview of the default function, the code all Virtual Users will execute during a test
categories: [core-concepts]
order: 9
redirect_from:
  - /4.0/test-scripting/main-function/
  - /4.0/core-concepts/main-function
---

***

<h2>Background</h2>

There are 4 parts to a test script. This article describes the `default` function, also known as VU code.  It's important to note that Virtual Users will iterate over this function as long as the test is running. Each iteration should be considered a new session.

***

The main test code (aka "VU code") is called after the "init code" and "setup code" has been run:

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

The "VU code" is put inside the `export default function` (aka "main") function.

<div class="callout callout-warning" role="alert">
    Note that the <code>setup()</code> and <code>teardown()</code> functions are optional <a href="{{ site.baseurl }}{% link _v4/core-concepts/test-setup-teardown-life-cycle.md %}" class="alert-link">life-cycle hooks</a>. If they're not used no <code>data</code> will be passed to the <code>export default function</code>.
</div>

It's in the "default" function you'll be spending most of your time. The most important thing to grasp before writing your test code is how a VU executes the default function.

## The default function life-cycle

A VU will execute the default function from start to end in sequence. Nothing out of the ordinary so far, but here's the important part; once the VU reaches the end of the default function it will loop back to the start and execute the code all over.

As part of this "restart" process, the VU is reset. Cookies are cleared and TCP connections might be torn down, depending on your test configuration options.

<div class="callout callout-warning" role="alert">
    Make sure to use <code>sleep()</code> statements to pace your VUs properly. An appropriate amount of sleep/think time at the end of the default function is often needed to properly simulate a user reading content on a page. If you don't have a <code>sleep()</code> statement at the end of the default function your VU might be "aggressive" than you've planned.
</div>
