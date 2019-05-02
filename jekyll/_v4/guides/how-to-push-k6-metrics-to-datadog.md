---
layout: classic-docs
title: How to push k6 metrics to DataDog
description: A guide on how to push k6 metrics to DataDog.
categories: [guides]
order: 13
---

***

<h2>Background</h2>

This article will show how you use [k6](https://k6.io/) to **store the results of your load tests into DataDog**. 

[DataDog](https://www.datadoghq.com/) is a monitoring and analytics platform that can help you to get full visibility of the performance of your applications. We ❤️ DataDog at [LoadImpact](https://loadimpact.com/) and use it to monitor the different services of our [Load Testing platform](https://loadimpact.com/). 

k6 is our open source load testing tool for testing the performance of your applications. One of the attractive features of k6 is that you can [output the result of your load tests](https://docs.k6.io/docs/results-output) to different sources; for example:

- stdout/console
- JSON
- Kafka
- LoadImpact

And now, you can send data from your k6 load test into DataDog for further analysis and correlation with DataDog analytics/metrics.

***

## Requirements

* k6 v0.24.0 (or above) is installed. If you do not have this installed, please refer to the official [k6 installation page](https://docs.k6.io/docs/installation). You can verify your current k6 version by command `k6 version`.
* Docker is installed. If you do not have this installed, please refer to the official [docker installation page](https://docs.docker.com/install/).

***

## Setup DataDog Agent

According to DataDog [documentation](https://docs.datadoghq.com/developers/dogstatsd/): "The easiest way to get your custom application metrics into DataDog is to send them to DogStatsD, a metrics aggregation service bundled with the DataDog Agent". That means that we need to setup a DataDog Agent in order to utilise DogStatsD service. For the sake of simplicity we will run DataDog Agent v6 (latest at the moment) as a docker container. Here is official [DataDog Docker Agent page](https://docs.datadoghq.com/agent/docker/).
To run a container execute next command in shell:

```shell

docker run \
            -e DD_API_KEY=<YOUR_DATADOG_API_KEY> \
            -e DD_DOGSTATSD_NON_LOCAL_TRAFFIC=1 \
            -p 8125:8125/udp \
            datadog/agent:latest
```
Here we add environment variables `-e DD_DOGSTATSD_NON_LOCAL_TRAFFIC=1` since it is required to send custom metrics and `-e DD_API_KEY=<YOUR_DATADOG_API_KEY>` for authorization. Also by `-p 8125:8125/udp` we bind 8125 UDP port of a container to 8125 UDP port of our Host so that k6 will be able to send metrics to DogStatsD. That's it, from now DataDog Agent container will be listening on 8125/UDP port until we close it (by hitting Ctrl+C in the shell where we started a container).

***

## Run k6 script

Next step is to send our k6 metrics to already running DataDog agent.
Lets create a simple k6 script.js:

```javascript

import http from "k6/http";
import { check, sleep } from "k6";

export let options = {
    stages: [
        { duration: "300s", target: 50 }
    ],
    thresholds: {
        "http_req_duration": ["p(95)<3"]
    }
};

export default function() {
    let res = http.get("https://test.loadimpact.com/");
    check(res, {
        "is welcome header present": (r) => r.body.indexOf("Welcome to the LoadImpact") !== -1
    });
    sleep(5);
}
```
Let's run this script locally, execute next command in shell:
```console
k6 run script.js
```

![datadog_push_5]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-push-k6-metrics-to-datadog/datadog_push_5.png)

To output k6 metrics to DataDog, we need to add `--out datadog` option and define some env variables (find env variables description below):

```console
K6_DATADOG_ADDR=localhost:8125 \
K6_DATADOG_NAMESPACE=custom_metric. \
k6 run --out datadog script.js
```

Here we specify next environment variables to configure k6:
 * `K6_DATADOG_ADDR=localhost:8125` configures at which address DataDog agent is listening. Default value is `localhost:8125` too, so we can omit it in our case.
 * `K6_DATADOG_NAMESPACE=custom_metric.` configures a prefix before all the metric names. Default value is `k6.`.

 Other available environment variables:
 * `K6_DATADOG_PUSH_INTEVAL` configures how often data batches are sent. The default value is `1s`.
 * `K6_DATADOG_BUFFER_SIZE` configures buffer size. The default value is `20`.
 * `K6_DATADOG_TAG_BLACKLIST` configures tags that should NOT be sent to DataDog. Default is equal to `''` (nothing). This is a comma separated list, example: `K6_DATADOG_TAG_BLACKLIST="tag1, tag2"`.

![datadog_push_4]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-push-k6-metrics-to-datadog/datadog_push_4.png)

***

## View and analyze metrics in DataDog

While k6 is running, metrics should be already available in DataDog, we can verify that by looking at [DataDog Metrics Explorer](https://docs.datadoghq.com/graphing/metrics/explorer/):

![datadog_push_3]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-push-k6-metrics-to-datadog/datadog_push_3.png)

As seen from the picture above our metrics are available with prefix `custom_metric.`, which we specified in `k6 run` command. Also we see that DataDog tags are assigned (in our case we picked `method:get` and `status:200`). K6 sends [k6 tags](https://docs.k6.io/docs/tags-and-groups#section-tags) as [datadog metric tags](https://docs.datadoghq.com/tagging/). Since metrics are available in DataDog you can monitor or analyze them as usual DataDog metrics, creating [monitors](https://docs.datadoghq.com/monitors/) or [dashboards](https://docs.datadoghq.com/graphing/dashboards/).

***

## Conclusion

If you followed along, you should now have a running DataDog agent and used the environment variables to define the output to DataDog from your k6 load test. We ❤️ DataDog here at LoadImpact and developing a new k6 feature to be able push metrics to DataDog easily was something we thought was important, this tutorial has shown how to do it in practice.

Many thanks to [@ivoreis](https://github.com/ivoreis) and [@MStoykov](https://github.com/MStoykov) for their work on DataDog output feature of k6 v0.24.0. 

As always, we are looking forward to hearing about your load testing experience; if you have any feedback, don’t hesitate to contact our support team or any k6 channel:  [community forum](https://community.k6.io/), [k6 github repository](https://github.com/loadimpact/k6) or [Slack](https://k6.io/slack). 

***

## See also
- [DataDog](https://www.datadoghq.com/)
- [DataDog Docker Agent](https://docs.datadoghq.com/agent/docker/)
- [DataDog Tags](https://docs.datadoghq.com/tagging/)
- [k6 Tags]({{ site.baseurl }}{% link _v4/core-concepts/tags.md %})