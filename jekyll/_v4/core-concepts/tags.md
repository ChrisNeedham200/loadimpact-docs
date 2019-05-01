---
layout: classic-docs
title: Tags
description: Tags are used to specify requests or metrics interesting to you. Built in tags allow you to filter on existing meta data of a test.
categories: [core-concepts]
order: 7
redirect_from: /4.0/test-scripting/tags/
---

***

<h2>Background</h2>

Tags are a powerful tool that allow you to analyze your data from a few different perspectives.  Some examples of this include:

- Thresholds: specify thresholds on a subset of data points, based on things you 'tag'
- Result analysis: slice and dice the data in Insights by filtering metric data points based on tags

## Tags with thresholds
Let's have a look at a test script with a few different URLs:

{% highlight js linenos %}
import http from "k6/http";
import {sleep} from "k6";

export let options = {
    stages: [
        { target: 10, duration: "10s" }
    ]
};

const baseURL = "https://test.loadimpact.com";

export default function(data) {
    // Load page
    let res = http.get(baseURL);

    // Load static assets
    res = http.batch([
        ["GET", `${baseURL}/style.css`],
        ["GET", `${baseURL}/images/logo.png`]
    ]);

    sleep(3.0);
}
{% endhighlight %}

Now, imagine we want to set a threshold on the response time of the static assets separately from the page. The easiest way to accomplish that is by adding tags to the static asset requests and then setting up a threshold to only look at reponse times from requests tagged with that tag, something like this:

{% highlight js linenos %}
import http from "k6/http";
import {sleep} from "k6";

export let options = {
    stages: [
        { target: 10, duration: "10s" }
    ],
    thresholds: {
        // A threshold that only looks at requests with a tag "staticAsset" with a value of "yes"
        "http_req_duration{staticAsset:yes}": ["p(95)<500"]
    }
};

const baseURL = "https://test.loadimpact.com";

export default function(data) {
    // Load page
    let res = http.get(baseURL);

    // Load static assets (tagging them with "staticAsset"="yes")
    res = http.batch([
        ["GET", `${baseURL}/style.css`, null, { tags: { staticAsset: "yes" } }],
        ["GET", `${baseURL}/images/logo.png`, null, { tags: { staticAsset: "yes" } }]
    ]);

    sleep(3.0);
}
{% endhighlight %}

## Tags for result analysis

In the "Filters" section of the result analysis page you can enter one or more tags to use as filters for the metrics data displayed under the "Breakdown tree", "URL table" and "Analysis" tabs.

Any tag added will immediately filter the display metrics data in these tabs to only show data points that have been tagged with the selected tag name and value.

### Results by load zone for Cloud Execution

With Cloud Execution all metrics data points are automatically tagged with the load zone from which they originated. This is very useful for slicing the results by load zone, just select the "load zone" tag and choose a load zone in the filter section to filter all the results in the breakdown, URL and Analysis tabs:

![Insights results by load zone]({{ site.baseurl }}/assets/img/v4/test-scripting/v4-insights-tagging-load-zones.png)

See also:
- k6 docs on [tags](https://docs.k6.io/docs/tags-and-groups) for more information.
