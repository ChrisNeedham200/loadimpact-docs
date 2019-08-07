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

When you want to run a k6 test from the cloud you simple change the k6 command used from `run` to `cloud`. For example, if you have a test script named `script.js`, you'd then trigger a cloud test by executing the following in your terminal:

`k6 cloud script.js`

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

### GUI based approach

If you want a simpler approach to creating and running tests, cloud tests in particular, you can use the in-app "Create test" feature to create and run tests based on one or more URLs (that we create scenarios from using Chrome):

![Create test from URL(s)]({{ site.baseurl }}/assets/img/v4/test-running/url-based-test-in-app.png)

1. Enter one or more URLs
2. Select a max number of VUs to rampup to (starts at 1) and a total test duration (in seconds)
3. Select which load zones you want the traffic to be generated from.
4. Optionally add domain filters that will filter out potentially unwanted URLs from the script (think third-party content like JS trackers, Facebook and Twitter widgets etc.).

See also the second option, [uploading a HAR file from a browser recording]({{ site.baseurl }}/4.0/guides/how-to-do-browser-recording), for creating tests without doing any scripting.

## Test configuration options

When running a cloud execution test you can configure from which load zones the traffic should be generated. You specify the load zones as part of the `ext.loadimpact.distribution` option:

{% highlight js linenos %}
export let options = {
    ext: {
        loadimpact: {
            name: "My k6 test",
            distribution: {
                scenarioLabel1: { loadZone: "amazon:us:ashburn", percent: 50 },
                scenarioLabel2: { loadZone: "amazon:ie:dublin", percent: 50 }
            }
        }
    }
};
{% endhighlight %}

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


## Tags

When running a k6 test in the cloud two [tags]({{ site.baseurl }}/4.0/core-concepts/tags/) are added to all metrics:


Tag name|	Type|	Description
-|-|-
load_zone	|string|	The load zone from where the the metric was collected. Values will be of the form: amazon:us :ashburn (see list above).
instance_id	|int|	A unique number representing the ID of a load generator server taking part in the test.
{: class="table table-striped"}


![Insights tags]({{ site.baseurl }}/assets/img/v4/test-running/cloud-execution-tags.png)

## Using environment variables

To use environment variables when running a cloud executed test you use one or more `-e KEY=VALUE` (or `--env KEY=VALUE`) CLI flags.

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

As described above the distribution of traffic across load zones is specifed by first assigning an arbitrary label to each entry. This label can be viewed upon as a "scenario label", and you can use it, together with [modules]({{ site.baseurl }}{% link _v4/core-concepts/module-imports.md %}) to setup a test with several different scenarios:

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

## See also
- [Local and On-premise execution]({{ site.baseurl }}/4.0/guides/local-on-premise-execution)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwMTUyODk1Ml19
-->
