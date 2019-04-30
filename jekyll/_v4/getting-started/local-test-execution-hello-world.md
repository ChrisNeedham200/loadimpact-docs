---
layout: classic-docs
title:  Local test execution - "Hello world"
description: A guide on how to trigger your first tests from the command line. Intended for more advanced users.
categories: [getting-started]
order: 5
redirect_from:
    - /4.0/getting-started/hello-world/
    - /4.0/test-running/
---

***
<div class="callout callout-warning" role="alert">
  Attention! This article is intended for a more advanced LoadImpact User. If you are familiar with using command line tools and prefer to use your IDE of choice, please proceed forward!
</div>


<h1>Purpose</h1>

In this document, we will focus on running a very simple test from the command line with k6. This is so you can understand the various parts of k6 and how they relate to one another. We will be making a single request to `https://test.loadimpact.com`, but feel free to change that to a URL, or API endpoint, that you _are allowed to test_.

_Note:_ This test and script is one of the most basic ones you could have. In our example, `https://test.loadimpact.com` is only the main HTML document. No other dependencies, such as CSS and JavaScript, would be loaded. Further along in this documentation we will highlight ways to create more complex scripts (user scenarios) to create more realistic tests.

***

- TOC
{:toc}

To get started with LoadImpact 4.0 you'll need to complete a few steps:

1. Download and install k6 for your platform of choice: Linux, Mac or Windows
2. Run your first k6 test
3. Login to your LoadImpact account and stream your results to LoadImpact Insights

## Download and install k6

Follow these instructions for your platform of choice to download and install k6:

<div class="row platform-tabs">
    <div class="col-12">
        <ul class="nav nav-pills mb-3" role="tablist">
            <li class="nav-item">
                <a class="nav-link active" id="platform-tabs-link-linux" data-toggle="pill" href="#platform-tabs-content-linux" role="tab">Linux</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" id="platform-tabs-link-macos" data-toggle="pill" href="#platform-tabs-content-macos" role="tab">Mac</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" id="platform-tabs-link-windows" data-toggle="pill" href="#platform-tabs-content-windows" role="tab">Windows</a>
            </li>
        </ul>
        <div class="tab-content">
            <div class="tab-pane fade show active" id="platform-tabs-content-linux" role="tabpanel" aria-labelledby="platform-tabs-link-linux">
                <p>
                    <h4>Debian/Ubuntu</h4>
                    <pre><code>$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61
$ echo "deb https://dl.bintray.com/loadimpact/deb stable main" | sudo tee -a /etc/apt/sources.list
$ sudo apt-get update
$ sudo apt-get install k6</code></pre>
                </p>
                <p>
                    <h4>Redhat/CentOS</h4>
                    <pre><code>$ wget https://bintray.com/loadimpact/rpm/rpm -O bintray-loadimpact-rpm.repo
$ sudo mv bintray-loadimpact-rpm.repo /etc/yum.repos.d/
$ sudo yum install k6</code></pre>
                </p>
            </div>
            <div class="tab-pane fade" id="platform-tabs-content-macos" role="tabpanel" aria-labelledby="platform-tabs-link-macos">
                <pre><code>brew tap loadimpact/k6
brew install k6</code></pre>
            </div>
            <div class="tab-pane fade" id="platform-tabs-content-windows" role="tabpanel" aria-labelledby="platform-tabs-link-windows">
                <a href="https://dl.bintray.com/loadimpact/windows/k6-latest-amd64.msi">Download the latest k6 installer</a>
            </div>
        </div>
    </div>
</div>

Read more about [installation options](https://docs.k6.io/docs/installation) in the k6 docs.

## Run your first k6 test

Let's have a look at a simple k6 test script that you can use to run your first k6 test:

{% highlight js linenos %}
import { sleep } from "k6";
import http from "k6/http";

export default function() {
  http.get("https://test.loadimpact.com/");
  sleep(3);
}
{% endhighlight %}

We import some functionality we need, make a single request to `https://test.loadimpact.com/` and then have our Virtual User sleep/think for 3 seconds. The [main function]({{ site.baseurl}}{% link _v4/core-concepts/default-function.md %}) is essentially a while true loop. As long as the test as time left on it and an iteration limit has not been met, Virtual Users will continue to iterate over it.

Let's run this test script with k6. Save it in a file on your computer (we'll assume it's saved to a file called `script.js` below). In a terminal, in the same working directory where you saved the file, execute the following command:

`k6 run script.js`

You should see something like this in the terminal:

![First k6 execution]({{ site.baseurl }}/assets/img/v4/getting-started/hello-world-first-k6-exec.png)

This executed the test with k6's default settings in terms of number of concurrent Virtual Users (VU) and test duration. The default is 1 VU doing one iteration through the test script.

**Congratulations! You just ran your first k6 test.**

### Ramping VUs

To run a meaningful load test, we will need more than 1 Virtual User :)

We can specify the Virtual User ramping that we want by extending out test script with some [test configuration options]({{ site.baseurl}}{% link _v4/reference/test-configuration-options.md %}). These options are global in scope and are configured in the section `export let options` on lines 4-16 below:

{% highlight js linenos %}
import { sleep } from "k6";
import http from "k6/http";

export let options = {
    // Add VU ramping option, total test length is 3m
    stages: [
        // Ramp up from 1 VU to 25 VUs for 1 minute
        { target: 25, duration: "60s" },

        // Stay constant at 25 VUs for 1 minute
        { target: 25, duration: "60s" },

        // Ramp down from 25 VUs to 0 VUs for 1 minute
        { target: 0, duration: "60s" }
    ]
};

export default function() {
  http.get("https://test.loadimpact.com/");
  sleep(3);
}
{% endhighlight %}

_Note:_ Ramping configurations are linear and evenly distributed over the duration. In the above example, a new Virtual User would start every 2.4 seconds until reaching 25 at the 1 minute mark.

Let's run the test again:

`k6 run script.js`

Now you should see something like this in the terminal:

![k6 execution with VU ramping]({{ site.baseurl }}/assets/img/v4/getting-started/hello-world-ramping-k6-exec.png)

We were presented with a progress bar for the 3m duration of the test before being presented with the results.

Read more about [Types of Load and Performance Tests]({{ site.baseurl}}/4.0/core-concepts/types-of-load-performance-tests/) in the test scripting section.

## Stream results to LoadImpact Insights

So far we've only used one component (k6) of LoadImpact 4.0. Let's have a look at the centerpiece of the cloud offering, LoadImpact Insights. Insights is used for storing, analyzing, sharing, and trending k6 test results. It's easy to use, you just log in to your LoadImpact account and add `-o cloud`:

**Login to your LoadImpact account**:

`k6 login cloud`

**Run test and stream results to Insights**:

`k6 run -o cloud script.js`

![Streaming to Insights]({{ site.baseurl }}/assets/img/v4/getting-started/hello-world-streaming-k6-exec.png)

The interesting in the terminal this time around is the `output: cloud (https://app.loadimpact.com/k6/runs/12696)` line which tells us that we can go look at the test results in LoadImpact Insights by opening the provided URL. Let's do that:

![Test results in Insights]({{ site.baseurl }}/assets/img/v4/getting-started/hello-world-streaming-exec-insights.png)

Learn more about Insights in the [result analysis]({{ site.baseurl}}{% link _v4/result-analysis/insights-overview.md %}) section.

## Running a test with LoadImpact Cloud Execution

The last step of this intro guide is to try the last component of LoadImpact 4.0, LoadImpact Cloud Execution.

The only change we need to make is that we'll need to change the k6 command used from `run` to `cloud`:

`k6 cloud script.js`

Same as when we streamed our results to Insights, we'll now get a link to Insights to view the test results of this LoadImpact Cloud Executed test.

Learn more about Cloud Execution in the [test running]({{ site.baseurl}}{% link _v4/guides/cloud-execution.md %}) section.

**Congratulations!  You've run a smoke test, a locally executed test and a cloud executed test!**

## Next Steps

Now that you've successfully run your first batch of k6 tests, consider the reading our [Basic Load Testing Methodology]({{ site.baseurl}}/4.0/getting-started/basic-load-testing-methodology) to assist on your journey or performance testing mastery.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU1OTQyMjU4XX0=
-->
