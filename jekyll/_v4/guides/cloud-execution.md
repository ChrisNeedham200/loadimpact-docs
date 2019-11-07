---
layout: classic-docs
title: How to Run Cloud Tests From the Command Line
description: A guide on how to trigger k6 tests on the command line to run within the LoadImpact Cloud
categories: [guides]
order: 2
redirect_from: /4.0/test-running/cloud-execution/
---

***

# Purpose

Explanation of LoadImpact's cloud service and how to utilize it with your k6 test scripts.

Cloud execution is a convenient extension to the [local/on-premise execution]({{ site.baseurl }}/4.0/guides/local-on-premise-execution) capability of k6. Great when you need to run larger tests or distribute traffic generation across several geographic locations (aka "load zones").

## Executing a cloud test

When you want to run a k6 test using the LoadImpact cloud you simply change the k6 command used from `run` to `cloud`. For example, if you have a test script named `script.js`, you'd then trigger a cloud test by executing the following in your terminal:

`k6 cloud script.js`

Executing this does the following:
1. k6 runs locally to read the initialization context.  If modules are required, they are gathered
2. We create an archive and upload that to our cloud
3. The cloud unarchives the file and reads the initialization context so we can deploy to the correct load zones
4. Once load generators are online and ready, your test starts running. You are given a link to your results under `output` on the command line

### Authenticating with LoadImpact cloud service

Before you can execute `k6 cloud ...` you'll need to authenticate with the LoadImpact cloud service. You can login with your LoadImpact credentials by entering the following command into your terminal:

`k6 login cloud` (for more authentication options, [see here]({{ site.baseurl }}/4.0/guides/logging-into-cloud-service-from-k6))

<div class="callout callout-warning" role="alert">
    <b>Google/Github Single-Sign On Users</b><br>
    For Single-Sign On (SSO) users logging in with <code>k6 login cloud</code> won't work as it requires a LoadImpact account email and password. You'll instead need to <a href="https://app.loadimpact.com/account/token">get your API authentication token from the app</a> and supply that explicitly: <code>k6 login cloud --token YOUR_API_AUTH_TOKEN</code>.
</div>

<div class="callout callout-warning" role="alert">
    <b>Docker Users</b><br>
    If you're running k6 in a Docker container you'll need to make sure that the k6 config file where the LoadImpact API authentication information (an API authentication token) will be stored to is persisted via a Docker volume to the host machine using the <code>-c/--config PATH/TO/CONFIG_FILE</code> CLI flag, e.g. <code>docker run -i -v /path/on-host:/path/in-container/ loadimpact/k6 login cloud -c /path/in-container/config.json</code>.
</div>

### Options and Configurations

Many different options are available for you to control your test. Please refer to our article on [Test Configuration Options]({{ site.baseurl }}/4.0/reference/test-configuration-options/) for a complete listing. Please refer to this sample for the next following sections:

{% highlight js linenos %}
export let options = {
    stages: [
        { target: 200, duration: "1m" },
        { target: 200, duration: "3m" },
        { target: 0, duration: "1m" }
    ],
    thresholds: {
        "http_req_duration": ["p(95)<500"],
        "http_req_duration{staticAsset:yes}": ["p(95)<100"],
        "check_failure_rate": ["rate<0.3"]
    },
    ext: {
        loadimpact: {
            name: "My k6 Test",
            projectId: 123456,
            distribution: {
                scenarioLabel1: { loadZone: "amazon:us:ashburn", percent: 50 },
                scenarioLabel2: { loadZone: "amazon:ie:dublin", percent: 50 }
            }
        }
    }
};
{% endhighlight %}


## Stages

Stages enable you to ramp Virtual Users up and down in a linear fashion. In the above example, the test will:
1. Ramp from 0 - 200 VUs over 1 minute.
2. Stay at 200 VUs for the next 3 minutes.
3. Ramp down to 0 VUs over the final minute.

Note: Ramping is completely linear from stage to stage.  During ramp down and at the end of the test, VUs do not currently gracefully exit. They stop execution no matter where they are in the script. VUs will iterate the script continuously while they are online.


## Thresholds

These are binary pass/fail criteria.  It's important to define thresholds to aide in result analysis. Thresholds can also be used to automatically pass/fail tests that are run in CI.  Tests that fail by threshold exit with non zero exit codes. For more information on Thresholds, refer to [this article]({{ site.baseurl }}/4.0/core-concepts/thresholds/).


## LoadImpact specific options

Within the options object, `options.ext.loadimpact` allows you to specify various things related to the LoadImpact cloud.

- `name` allow you to name your test so you can easily identify it later
- `projectId` allows you run your test from a specific project. If you have been invited to an organization, this is required!
- `distribution` allows you to spread your test across multiple regions in a single test. The number of Load Zones you can use depends on your subscription.

Each entry, or scenario, in the `distribution` object specifies an arbitrary label (aka "distribution label") as the key and an object with keys `loadZone` and `percent` as the value. The label ("scenarioLabel1" and "scenarioLabel2" above) will be injected as an [environment variable]({{ site.baseurl }}/4.0/core-concepts/environment-variables) (`__ENV["LI_DISTRIBUTION"]`) into the k6 processes running in the corresponding load zone ([see below]({{ site.baseurl }}/4.0/guides/cloud-execution/#load-impact-environment-variables) for more details).

The `percent` specifies how VUs should be distributed across the different scenarios, and the `loadZone` the origin of the traffic for the scenario.

### Load zones

Valid values for `loadZone` are:

Location                        | Value
--------------------------------|--------------------
Ashburn, Virginia, US (Default) | amazon:us:ashburn
Columbus, Ohio, US              | amazon:us:columbus
Dublin, Ireland                 | amazon:ie:dublin
Frankfurt, Germany              | amazon:de:frankfurt
London, UK                      | amazon:gb:london
Paris, France                   | amazon:fr:paris
Stockholm, Sweden               | amazon:se:stockholm
Montreal, Canada                | amazon:ca:montreal
Mumbai, India                   | amazon:in:mumbai
Palo Alto, California, US       | amazon:us:palo alto
Portland, Oregon, US            | amazon:us:portland
Seoul, South Korea              | amazon:kr:seoul
Singapore                       | amazon:sg:singapore
Sydney, Australia               | amazon:au:sydney
Tokyo, Japan                    | amazon:jp:tokyo
Hong Kong, China                | amazon:cn:hong kong
{: class="table table-striped"}


## Other tips for the cloud

### Using environment variables

To use environment variables when running a cloud executed test you use one or more `-e KEY=VALUE` (or `--env KEY=VALUE`) CLI flags. This is helpful if you need to change specific information per test run, such as a host name.

{% highlight shell %}

k6 cloud -e MY_HOSTNAME=test.loadimpact.com script.js

{% endhighlight %}



{% highlight js linenos %}
import { check, sleep } from "k6";
import http from "k6/http";

export default function() {
    var r = http.get(`http://${__ENV.MY_HOSTNAME}/`);
    check(r, {
        "status is 200": (r) => r.status === 200
    });
    sleep(5);
}
{% endhighlight %}


<div class="callout callout-warning" role="alert">
    <b>Use the CLI flags to set environment variables</b><br>
    With cloud execution you must use the CLI flags (<code>-e/--env</code>) to set <a href="{{ site.baseurl }}/4.0/core-concepts/environment-variables" class="alert-link">environment variables</a>. Environment variables set in the local terminal before executing k6 won't be forwarded to the LoadImpact cloud service, and thus won't be available to your script when executing in the cloud.
</div>

### LoadImpact environment variables

When running a cloud executed test with LoadImpact the following environment variables will always be set:

Name|	Value|	Description
-|-|-
LI_LOAD_ZONE	|string|	The load zone from where the the metric was collected. Values will be of the form: amazon:us :ashburn (see list above).
LI_INSTANCE_ID	|number|	A sequential number representing the unique ID of a load generator server taking part in the test, starts at 0.
LI_DISTRIBUTION	|string|	The value of the "distribution label" that you used in `ext.loadimpact.distribution` corresponding to the load zone the script is currently executed in.
{: class="table table-striped"}

See below for an example on how the `LI_DISTRIBUTION` environment variable can be used to create multi-scenario tests.


## Multi scenario load tests

There are a few ways to distribute Virtual Users to do "different scenarios" in a single test. Here are two commons ways to achieve this functionality:

### Multi scenario test with a switch statement

In this example, we want Virtual Users to do 1 of 3 journeys evenly from two load zones.

{% highlight js linenos %}
import { frontpageScenario } from "./scenarios/frontpage.js"
import { searchScenario } from "./scenarios/search.js"
import { shoppingScenario } from "./scenarios/shop.js"

export let options = {
    ext: {
        loadimpact: {
            name: "My shop test",
            distribution: {
                labelOne: { loadZone: "amazon:us:ashburn", percent: 50 },
                labelTwo: { loadZone: "amazon:ie:dublin", percent: 50 }
            }
        }
    }
};

export default function() {
  let userDistro = Math.floor(Math.random() * 100);

  switch (true) {
    case (userDistro <= 33):
      frontpageScenario();
      break;
    case (userDistro > 33 && userDistro <= 66):
      searchScenario();
      break;
    case (userDistro > 66 && userDistro < 100):
      shoppingScenario();
      break;
    default:
      break;

  sleep(1);


  }

};
{% endhighlight %}


### Multi scenario load test using labels

As described above the distribution of traffic across load zones is specified by first assigning an arbitrary label to each entry. This label can be viewed upon as a "scenario label", and you can use it, together with [modules]({{ site.baseurl }}{% link _v4/core-concepts/module-imports.md %}) to setup a test with several different scenarios.  In this example, we want only our Virtual Users from Ashburn to do 1 journey while users in Dublin do 1 of 2 different journeys.

{% highlight js linenos %}
import { frontpageScenario } from "./scenarios/frontpage.js"
import { searchScenario } from "./scenarios/search.js"
import { shoppingScenario } from "./scenarios/shop.js"

export let options = {
    ext: {
        loadimpact: {
            name: "My shop test",
            distribution: {
                frontpageScenarioLabel: { loadZone: "amazon:us:ashburn", percent: 50 },
                searchScenarioLabel: { loadZone: "amazon:ie:dublin", percent: 25 },
                shoppingScenarioLabel: { loadZone: "amazon:ie:dublin", percent: 25 }
            }
        }
    }
};

export default function() {
    if (__ENV["LI_DISTRIBUTION"] === "frontpageScenarioLabel") {
        frontpageScenario();
    } else if (__ENV["LI_DISTRIBUTION"] === "searchScenarioLabel") {
        searchScenario();
    } else if (__ENV["LI_DISTRIBUTION"] === "shoppingScenarioLabel") {
        shoppingScenario();
    }
}
{% endhighlight %}

Where scenario modules (frontpage.js, search.js and shop.js) would look something like this:

{% highlight js linenos %}
import { group, sleep } from "k6";
import http from "k6/http";

export function frontpageScenario() {
    group("Front page", function() {
        http.get("https://example.com/");
        sleep(10);
    });
}
{% endhighlight %}

### Tags

When running a k6 test in the cloud two [tags]({{ site.baseurl }}/4.0/core-concepts/tags/) are added to all metrics:


Tag name|	Type|	Description
-|-|-
load_zone	|string|	The load zone from where the the metric was collected. Values will be of the form: amazon:us :ashburn (see list above).
instance_id	|int|	A unique number representing the ID of a load generator server taking part in the test.
{: class="table table-striped"}


![Insights tags]({{ site.baseurl }}/assets/img/v4/test-running/cloud-execution-tags.png)


## See also
- [Local and On-premise execution]({{ site.baseurl }}/4.0/guides/local-on-premise-execution)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwMTUyODk1Ml19
-->
