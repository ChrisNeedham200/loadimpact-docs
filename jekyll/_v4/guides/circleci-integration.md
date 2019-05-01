---
layout: classic-docs
title: How to Integrate CircleCI With LoadImpact and k6
description: Guide on how to integrate CircleCI with LoadImpact and k6. Shift left and automate your load testing in CI/CD.
categories: [guides]
order: 3
redirect_from: /4.0/integrations/circleci-integration/
---

***

<h2>Purpose</h2>

This guide and sample describes how to integrate LoadImpact 4.0 with CircleCI. Your exact installation and needs may vary and your business needs should be taken into consideration when designing your integration. This document follows a sample. Supporting files for the below sample are available in our [GitHub repo](https://github.com/loadimpact/k6-circleci-example). This guide will help you get up and running with k6, CircleCI and LoadImpact Insights (with build notifications sent to Slack). This guide assumes you are familiar with k6 and CircleCI.

- TOC
{:toc}

## Background

LoadImpact believes in goal oriented and automated load testing. That's why we built k6 to work well in these environments, integrating nicely with services like CircleCI, the continuous integration and delivery platform. This enables you to automatically run tests against your websites, APIs, microservices, web apps, or anything else you may want to monitor for performance regressions over time.

It's highly advised to utilize thresholds in automated tests, so you can pass or fail your builds based on the outcome of your performance test. Refer to [this article]({{ site.baseurl }}/4.0/core-concepts/thresholds/) for more information on thresholds.

## Prerequisites

- A LoadImpact account
  - Trial or paid plan
- LoadImpact API key
- Test scripts for what you want to test
  - You may utilize our script in lieu of one of yours
- Familiarity with CircleCI

## Your test script

It all starts with code, here is the example script we will be using in this guide. It tests our site, `test.loadimpact.com`.

{% highlight js linenos %}
import { check, group, sleep } from "k6";
import http from "k6/http";

// Options
export let options = {
    stages: [
        { duration: "60s", target: 10 },
        { duration: "60s" },
        { duration: "60s", target: 0 }
    ],
    thresholds: {
        http_req_duration: ["p(95)<500"]
    },
    ext: {
        loadimpact: {
            name: "test.loadimpact.com"
        }
    }
};

// Scenario
export default function() {
    group("Front page", function() {
        let res = http.get("http://test.loadimpact.com/");
        check(res, {
            "is status 200": (r) => r.status === 200
        });
        sleep(10);
    });
}
{% endhighlight %}

To describe the load test script in words:

1. The test will ramp up from 0 to 10 users over 60s, stay flat at 10 users for another 60s, before ramping down to 0 users again over 60s
2. We've set a goal that the 95th percentile response time should be below 500ms. **This step, k6 [thresholds]({{ site.baseurl }}/4.0/core-concepts/thresholds/), is essential, it's how we define our goals which is the basis for the pass/fail mechanism to work that is built into k6**. If a threshold fails, k6 will end with a non-zero exit code, which in turn indicates a failed build step to CircleCI (and other CI tools).
3. In the load test "main" function we define a group `Front page` inside which, we make a request, [check]({{ site.baseurl }}/4.0/core-concepts/checks/) the response status code and sleep for 10s, before letting the user loop from the top of the function

## Configuration

Below is the barebones `circle.yml` file needed to run k6 and the above load test script from CircleCI!

{% highlight yaml linenos %}
dependencies:
  cache_directories:
    - "~/k6-bin"
  post:
    - mkdir -p ~/k6-bin
    - |
      if [[ ! -f ~/k6-bin/k6 ]]; then
        curl -O -L https://github.com/loadimpact/k6/releases/download/v0.15.0/k6-v0.15.0-linux64.tar.gz;
        tar -xvzf k6-v0.15.0-linux64.tar.gz;
        mv k6-v0.15.0-linux64/k6 ~/k6-bin/k6;
      fi
test:
  override:
    - ~/k6-bin/k6 run -q -o cloud loadtests/main.js
{% endhighlight %}

To describe the config in words:

1. Download a k6 release archive and extract the binary to a directory that is cached between builds
2. Run the k6 load test main.js located in the `loadtests` directory in the root of the repository
**Note:** The k6 command includes the `-o cloud` option which means the results will be streamed to Insights. Alternatively, you can use `k6 cloud` to execute the test from the LoadImpact cloud service. For that to work we'll need to make sure k6 has access to our LoadImpact API token in the environment variable K6_CLOUD_TOKEN.


## Environment variables

To have the results of our load tests streamed to Insights or executed in the LoadImpact cloud, we need to add the K6_CLOUD_TOKEN environment variable.
1. Navigate to your project's settings in CircleCI
2. Find the "Environment variables" section under "Build settings" in the menu to the left.
3. Add a new environment variable with name K6_CLOUD_TOKEN
4. Copy/paste the API token from your account as the value

![CircleCI Environment variables]({{ site.baseurl }}/assets/img/v4/integrations/circleci-integration/k6-circleci-environment-variables.png)

## Slack Notifications

**Note:** If you do not use Slack, or do not want notifications, skip to the next section.

Next, let's connect Slack to our CircleCI project so that we are notified when our load tests finish. For this, you will need to set up an [Incoming Webhook](https://api.slack.com/incoming-webhooks) integration in Slack. Once that is complete, copy/paste the Webhook URL into the input field in "Chat Notifications" Settings:

![CircleCI Slack Notifications]({{ site.baseurl }}/assets/img/v4/integrations/circleci-integration/k6-circleci-slack-notification.png)

## Triggering builds

In this example, we will trigger a build by making a small change to the load test script. We suggest changing the threshold to 100ms. Push the change to Github, CircleCI will pick up the repo change and execute the `circle.yml` file.

![CircleCI triggered test run]({{ site.baseurl }}/assets/img/v4/integrations/circleci-integration/k6-circleci-load-test-triggered.png)

In this example, lowering the threshold to 100ms results in a failed build step and thus a failed build. The notifcation informed us:

![CircleCI Slack Notifications]({{ site.baseurl }}/assets/img/v4/integrations/circleci-integration/k6-circleci-notifications-slack.png)

## Result analysis

Passing builds aren't too interesting. When builds fail, you will want to determine why. When it comes to load tests, there is no exception to be made. Insights, our result analysis tool in the LoadImpact cloud is built to be the perfect companion to k6. Insights is designed to be error driven, this helps you to quickly find the cause of the failed load test. Refer to [this article]({{ site.baseurl }}/4.0/result-analysis/insights-overview/) for more information on result analysis.

## See also
- [GitHub repo for this example](https://github.com/loadimpact/k6-circleci-example)
- [Installing k6]({{ site.baseurl }}/4.0/getting-started/hello-world/#download-and-install-k6)
- [Your API token](https://app.loadimpact.com/integrations)
- [Thresholds]({{ site.baseurl }}/4.0/core-concepts/thresholds/)
- [Insights Overview]({{ site.baseurl }}/4.0/result-analysis/insights-overview/)
- [Checks]({{ site.baseurl }}/4.0/core-concepts/checks/)
- [Slack's Incoming Webhooks](https://api.slack.com/incoming-webhooks)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTM3MzI3NDY4MF19
-->
