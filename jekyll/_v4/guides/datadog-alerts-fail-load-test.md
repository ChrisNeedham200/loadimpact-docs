---
layout: classic-docs
title: How to use Datadog alerts and Thresholds to fail your load test
description: A guide on how to use Datadog alerts and Thresholds to fail your load test.
categories: [guides]
order: 12
redirect_from: /4.0/guides/how-to-use-datadog-alerts-to-fail-k6-test/
---

***

<h2>Background</h2>

[Datadog](https://www.datadoghq.com/) is a monitoring and analytics platform that can help you to get full visibility of the performance of your applications. Here at [LoadImpact](https://loadimpact.com/) we use Datadog to monitor various different services of our platform. [Datadog alerts](https://docs.datadoghq.com/monitors/) give the ability to know when critical changes in your system are occurring. These triggered alerts appear in Datadog's [Event Stream](https://docs.datadoghq.com/graphing/event_stream/), allowing collaboration around active issues in your applications or infrastructure.

One potential performance issue is that a System Under Test(SUT) has high CPU consumption when under stress. This tutorial will show you how to fail your load test for this type of condition by using [Datadog's API](https://docs.datadoghq.com/api) and [thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %}) in LoadImpact.

- TOC
{:toc}

***

## Requirements

* A site/system to test. In this example, we will test a site already running as a ECS Service. This site is available at https://httpbin.test.loadimpact.com.
* An already configured Datadog integration with a platform your site is running on. In our case it is Datadog integration with AWS, please refer to the official [Datadog AWS Integration Guide](https://docs.datadoghq.com/integrations/amazon_web_services) for details.
* An account in LoadImpact with an appropriate [subscription](https://www.loadimpact.com/pricing).
* An account in Datadog that allows us to create [monitors](https://docs.datadoghq.com/monitors).

***

## Create a Monitor in Datadog

First, we want to create a monitor in Datadog which triggers an alert if CPU utilization reaches 100 units or more on the ECS Service. You may wish to monitor something else, so feel free to adjust this to meet your needs.
While creating a monitor make next actions:
* Choose  `Threshold alert` as a detection method
* Choose `aws.ecs.service.cpuutilization` metric from `servicename:<your_service_name>` in "Define the metric" step
* Configure "Alert threshold" to be 100
* Edit message and notification steps and save Monitor

![datadog_pull_2]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_2.png)
![datadog_pull_1]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_1.png)

Now the monitor will appear in the Datadog Event Stream if the metric threshold is reached. This is what we will look for when we evaluate the LoadImpact Thresholds later.

***

## Create a test in LoadImpact

Next, we will need a test script to run. Here is our example that we will use in this test:

{% highlight js linenos %}
import http from "k6/http";
import { Counter } from "k6/metrics";
import { check, group, sleep } from "k6";

export const datadogHttpbinCpu = new Counter("Httpbin_CPU_Alert");
export let options = {
    stages: [
        { duration: "30s", target: 50 },
        { duration: "600s", target: 50 }
    ],
    thresholds: {
        "http_req_duration": ["p(95)<200"],
        "Httpbin_CPU_Alert": ["count < 1"]
    }
};

const datadogApi = "https://api.datadoghq.com/api/v1/"; // DataDogs API endpoint
const datadogApiKey = "<YOUR_DATADOG_API_KEY>";         // DataDogs API key, read below how to get it
const datadogAppKey = "<YOUR_DATADOG_APPLICATION_KEY>"; // DataDogs application key, read below how to get it
const getDataDogHeader = tagName => {
    return {
        headers: { ["Content-Type"]: "application/x-www-form-urlencoded" },
        tags: { name: tagName }
    };
};

export function setup() {  // function for getting start time of test, executed before actual load testing
    let time = Date.now();
    return time;
}

export default function () {
    let res = http.get("https://httpbin.test.loadimpact.com/");
    check(res, {
        "is status 200": (r) => r.status === 200
    });
    sleep(1);
}

export function teardown(time) { // function which queries DataDogs event stream for alerts in time window of test run, executed after actual load testing
    let endTime = Math.floor(Date.now() / 1000);
    let startTime = Math.floor(time / 1000);
    let monitorTags = [
        "servicename:demosites-httpbin"
    ];

    let reqString =
        `events?api_key=` + datadogApiKey +
        `&application_key=` + datadogAppKey +
        "&start=" + startTime +
        "&end=" + endTime +
        "&tags=";

    monitorTags.forEach(tag => {
        let response = http.get(
            datadogApi + reqString + tag,
            getDataDogHeader("Datadog Event Stream")
        );
        let body = JSON.parse(response.body);
        body.events.forEach(event => {
            if (event.tags.includes("servicename:demosites-httpbin")) {
                datadogHttpbinCpu.add(true);
            }
        });
    });

}
{% endhighlight %}

You can find how to manage your Datadog API and Application keys [here](https://docs.datadoghq.com/account_management/faq/api-app-key-management/).

Go to [LoadImpact](https://app.loadimpact.com) and push button "CREATE NEW TEST". Choose "SRIPTING" in "WEBSITE/APP TESTING" section:
![datadog_pull_3]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_3.png)

Paste your script body, make a suitable name for test and save.

![datadog_pull_9]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_9.png)

***

## Run a test in LoadImpact

Run your test and in our case since we configured our script to run only 50 VUs, that load will be not enough to trigger a CPU alert, therefore 1st run will be passed:

![datadog_pull_7]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_7.png)

Let's update our script to produce more load on our system under test. For this example, we achieve this by increasing number of VUs from 50 to 150:

{% highlight js linenos %}
export let options = {
    stages: [
        { duration: "30s", target: 150 }, // increasing number of VUs from 50 to 150
        { duration: "600s", target: 150 } // increasing number of VUs from 50 to 150
    ],
    thresholds: {
        "http_req_duration": ["p(95)<200"],
        "Httpbin_CPU_Alert": ["count < 1"]
    }
};
{% endhighlight %}

As we can see, after increasing load our test was failed due to exceeding our defined threshold value:

![datadog_pull_10]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-use-datadog-alerts-to-fail-k6-test/datadog_pull_10.png)

***

## See also
- [Datadog](https://www.datadoghq.com/)
- [Datadog Alerts](https://docs.datadoghq.com/monitors/)
- [Datadog Event Stream](https://docs.datadoghq.com/graphing/event_stream/)
- [Datadog API](https://docs.datadoghq.com/api)
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})
