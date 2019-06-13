---
layout: classic-docs
title: How to Load Test a RESTful API with LoadImpact version 4.0 and k6
description: Guide on how to load test RESTful APIs with LoadImpact and k6 using Django and Django REST framework as an example
categories: [guides]
order: 0
redirect_from: /4.0/how-to-tutorials/how-to-load-test-a-restful-api/
---

***

<h2>Background</h2>

This guide will demonstrate how to utilize [k6](https://k6.io/) to test your API endpoints. But first, let's consider some possible reasons why we'd want to do that:
1. To ascertain that all our API endpoints produce correct results.
2. To have a feel for the experience that the consumers of our API are having.
3. To gauge the limits and capabilities of our API, and by extension, our infrastructure at large.
4. To enable Continuous Integration and automation practices that will allow to establish a baseline for comparing our API performance over time.
5. To move towards Continuous Delivery and [Canary Deployment](https://martinfowler.com/bliki/CanaryRelease.html) processes.

**Note:** This guide assumes that `k6` is already [installed](https://docs.k6.io/docs/installation) and ready for use. If not, we advise you to first read this [article]({{ site.baseurl }}/4.0/getting-started/product-overview/) and this [article](https://docs.k6.io/docs/welcome). Both of these articles cover various concepts that we will be mentioned below.


## Assumptions, first steps, and caveats
For this guide, the target system (with the API) will run locally in a modest and very restricted environment. The various parameters and result values are going to be significantly lower than those one would anticipate from a real, production-like, environment. To maintain focus on creating test cases for APIs, we will not be outputting our results to Insights nor will be executing on the LoadImpact [Cloud infrastructure]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %}). For the purposes of this guide, this will work fine. The general steps will remain the same. As you start testing production-like environments, you will likely need to make use of the data analysis provided by Insights and cloud execution on the LoadImpact infrastructure.

Our stack consists of [Django](https://www.djangoproject.com/) and [Django Rest Framework](https://www.django-rest-framework.org/) and a [PostgreSQL](https://www.postgresql.org/) 9.6 database. To avoid skewing our results, there is no caching involved whatsoever. Our test system requires Token-based authentication, so we'll assume we have already equipped ourselves with a valid token.

**Important concept:** A RESTful API service has numerous endpoints. These endpoints should each be tested in isolation. The performance characteristic of each endpoint should have not lead to any assumptions about other endpoint. This simple fact leads to the realization that every individual endpoint should be tested with different assumptions, metrics, and thresholds. After testing various endpoints in isolation, you may start to move towards a testing the emulates user behavior or requesting the endpoints in a logical order. Starting with individual endpoints is a **smart** way to begin your testing.

## Load Testing
With the above in mind, we'll start load testing the endpoint `v3/users` of our API.

This endpoint returns a JSON list representing an entity we call `User`. As a first step, we are going to perform some ad-hoc load tests to get a "feel"
of this endpoint. We do this in order to discern some realistic performance thresholds that it could possibly reach. Most people would refer to this as baseline or smoke testing.

### Performing GET requests
We first need to create a script. In this example, it is a file named `script.js`. Then, we add the following JavaScript code.

**Note:** this is an intentionally incomplete script that focuses on some key concepts. You shouldn't blindly copy it for your own work. Read on to understand why!:

```javascript
import http from "k6/http";
import { check } from "k6";
import { Rate } from "k6/metrics";


export let errorRate = new Rate("errors");

export default function() {
  var url = "http://api.dev.loadimpact.com/v3/users";  // This URL is provided for example purposes only
  var params = {
      headers: {
          "Authorization": "Token ffc62b27db68502eebc6e90b7c1476d29c581f4d",
          "Content-Type": "application/json"
      }
  };
  check(http.get(url, params), {
    "status is 200": (r) => r.status == 200
  }) || errorRate.add(1);
};

```

In plain english, the above script [imports]({{ site.baseurl }}{% link _v4/core-concepts/module-imports.md %}) the k6 library resources and uses them to check that every response to that endpoint returns a status code of 200. Additionally, we record any failed requests so that we will get the percentage of the successful operations in the final output.

We recommend that you should start with modest expectations (2-5 Virtual Users). This allows you to get a grasp on the system's baseline performance and work upwards from that. But let's suppose we are new at this and we also feel a bit optimistic, so we start a load test of the above script with 30 Virtual Users(VUs) for the duration of 30 seconds.

**Note:** In this example, we pass command line flags to adjust our VUs and duration. Refer to this [article]({{ site.baseurl }}/4.0/reference/test-configuration-options/#stages) for additional methods to set the number of concurrent users and length of test.

We execute the `k6` with the following parameters:
```shell
$ k6 run -d 30s -u 30 ./script.js
```

This starts `k6` and executes our script locally, using 30 users for 30 seconds as desired. The below, partial, output of our load test indicates there is an error.

![14_percent]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-load-test-a-restful-api/14_percent_30s_30vus_nosleep.png)

We see that only 14% of our requests were successful. This is abysmally low!

OK, so what happened? Well, were we to show the full output of the load test, we'd notice that we get a lot of warnings of the type:
```shell
WARN[0067] Request Failed error="Get http://api.dev.loadimpact.com/v3/users: net/http: request canceled (Client.Timeout exceeded while awaiting headers)"
```

So most requests timed-out. This happened because the default `k6` timeout value is set to 60 seconds and the responses were exceeding this limit. We could increase
the timeout by providing our own [Params.timeout](https://docs.k6.io/docs/params-k6http), in our [http.get](https://docs.k6.io/docs/get-url-body-params) call. While this would remove the error, it doesn't solve the performance issue.

In almost all cases, 60 seconds is plenty of time for a complete response. Now we must take a step back and figure out under which conditions our API can return proper and error-free responses for this endpoint.

We need to understand something about our load test's definition. The way we wrote it, _every_ VU performs the `GET` requests, in a _continuous_ loop, as fast as it can. Not only is this clearly inducing an unbearable burden to our system, it's also probably unrealistic as an example of real-world usage. We need to manage our expectations. To aide us, we can add a `sleep` statement to our code. In fact, a good rule of thumb is to *always* add a `sleep` statement even if it's short. This will pace the Virtual Users and help prevent a race condition in your test. The necessary code changes are as follows:

```javascript
// ... omitted for brevity
// add "sleep" in the import statement
import { check, sleep } from "k6";

// ... omitted for brevity
  check(http.get(url, params), {
    "status is 200": (r) => r.status == 200
  }) || errorRate.add(1);

  // We add it after each check()
  sleep(0.5);

};

```

Additionally, we will lower the number of VUs down to 20 and re-run the test:
```shell
$ k6 run -d 30s -u 20 ./script.js
```

This produces:

![62_percent]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-load-test-a-restful-api/62_percent_30s_20vus_half_sleep.png)

Now we are getting somewhere, things improved, but still, 38% of our requests timed-out.

We proceed by increasing the `sleep` value for each VU to 1 second:
```javascript
// ... omitted for brevity
  sleep(1);

```

And we re-run the test, while keeping the same number of VUs:
```shell
$ k6 run -d 30s -u 20 ./script.js
```

This produces a more desirable picture for our system:

![100_percent]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-load-test-a-restful-api/100_percent_success_30s_20vus_whole_sleep.png)

Some things we notice from the above output:
* All requests finished in a timely manner, with the correct status code
* 95% of our users got served a response in under `283.43ms`
* In the duration of 30 seconds we served 539 responses, at a rate of ~18 requests per second.

So now we have some more realistic expectations with regards to the capabilities of this endpoint while responding to `GET` requests, in our particular environment. We can utilize this information to scale up to larger on demand tests using LoadImpact [Cloud execution]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %}).  For example:

```shell
$ k6 cloud -d 300s -u 200 ./script.js
```

Your needs will vary here, but this would run for a test that scales to 200 Virtual users for 5 minutes and provide more in depth results within LoadImpact Insights.


### Performing POST requests
Our example system has another endpoint called `v3/organizations`. This allows `POST` requests when one would want to create a new `Organization` entity. Let us test it:

```javascript
import http from "k6/http";
import { check, sleep } from "k6";
import { Rate } from "k6/metrics";


export let errorRate = new Rate("errors");

export default function() {
  var url = "http://api.dev.loadimpact.com/v3/organizations";
  var params = {
      headers: {
          "Authorization": "Token ffc62b27db68502eebc6e90b7c1476d29c581f4d",
          "Content-Type": "application/json"
      }
  };

  var data = JSON.stringify({"name": `Organization Name ${__VU}: ${__ITER}`});

  check(http.post(url, data, params), {
    "status is 201": (r) => r.status == 201
  }) || errorRate.add(1);
  sleep(1);
};
```

A few things of note:
1. We changed the `http.get` to `http.post`. There's a whole range of supported methods; you can find them in the [k6 documentation](https://docs.k6.io/docs/k6http).
2. We now expect a `201` status code, something quite common for endpoints that create resources.
3. We introduced 2 environments variables, `__VU` and `__ITER`. We use them to generate unique dynamic data for our post data. These are special variables made available by `k6` at run-time. Read more about them [here](https://docs.k6.io/docs/execution-context-variables).

Armed with data from our previous test runs, we decide to keep the same VUs and duration values when running the script:
```shell
$ k6 run -d 30s -u 20 ./script.js
```

This produces the following results:

![post]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-load-test-a-restful-api/100_percent_30s_20vus_post.png)

We notice from the results above that we managed to serve all requests successfully. We also notice there was an increase in the duration of our responses and a decrease in the total amount of requests we could handle under a 30 second duration. This is to be expected though, as writing to a DB will always be a slower operation than reading from it.

### Putting it all together
Now we have enough data to create a script that tests both endpoints while at the same time providing some individual [thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %}) for them.

```javascript
import http from "k6/http";
import { check, sleep } from "k6";
import { Trend, Rate } from "k6/metrics";


export let listErrorRate = new Rate("List Users errors");
export let createErrorRate = new Rate("Create Organization errors");

export let ListTrend = new Trend("List Users");
export let CreateTrend = new Trend("Create Organization");

export let options = {
    thresholds: {
        "List Users": ["p(95)<500"],
        "Create Organization": ["p(95)<800"],
    }
};

export default function() {
    let urlUsers = "http://api.dev.loadimpact.com/v3/users";
    let urlOrgs = "http://api.dev.loadimpact.com/v3/organizations";
    let params = {
        headers: {
            "Authorization": "Token ffc62b27db68502eebc6e90b7c1476d29c581f4d",
            "Content-Type": "application/json"
        }
    };

    // Data for the POST request
    let createOrgData = JSON.stringify({"name": `Organization Name ${__VU}: ${__ITER}`});

    let requests = {
        "List Users": {
            method: "GET",
            url: urlUsers,
            params: params
        },
        "Create Organization": {
            method: "POST",
            url: urlOrgs,
            params: params,
            body: createOrgData
        },
    };

    let responses = http.batch(requests);

    let listResp = responses["List Users"];
    let createResp = responses["Create Organization"];
    check(listResp, {
        "status is 200": (r) => r.status === 200
    }) || listErrorRate.add(1);
    ListTrend.add(listResp.timings.duration);

    check(createResp, {
        "status is 201": (r) => r.status === 201
    }) || createErrorRate.add(1);
    CreateTrend.add(createResp.timings.duration);

    sleep(1);
};

```
In the above example take note of the following:
1. We created separate rates and trends [custom metrics]({{ site.baseurl }}{% link _v4/core-concepts/custom-metrics.md %}) for each endpoint.
2. We defined custom thresholds via the `options` variable. We increased our thresholds because we don't want to be too close to our system's limit.
3. We introduce the [batch()](https://docs.k6.io/docs/batch-requests) call, that helps us perform multiple types of requests simultaneously.

Because now we introduce more concurrent load in our system, we also decide to drop the VUs down to 15:
```shell
k6 run -d 30s -u 15 ./script.js
```
And here are the results:

![thresholds]({{ site.baseurl }}/assets/img/v4/how-to-tutorials/how-to-load-test-a-restful-api/thresholds_30s_15vus.png)

We observe that all requests were successfully processed. Additionally, we now have 2 extra rates ("Create Organization", "List Users") with visual indications about their
threshold status. More specifically, the first succeeded but the other one failed, because the threshold was exceeded.

Logically we should take action about the failed threshold. Should we increase the threshold value, or should we try to make our code more efficient? In any case, we now at least have all the necessary tools and knowledge to make adjustments and verify if they were impactful or not.

If you have been running these small tests locally to produce some working scripts and baseline results, like we did this guide, you should run larger tests on the LoadImpact [Cloud infrastructure]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %}).  You can simply change `k6 run` to `k6 cloud`.  That will tell k6 to archive and upload the necessary scripts to our cloud service and automatically start execution. No need to worry about managing load generators and other resources.

Finally, you should also consider integrating these tests into your CI pipeline. We have some guides with some of the popular CI tools [here]({{ site.baseurl }}/4.0/integrations/).

***

## See also
- [Getting Started with Performance Testing](http://blog.loadimpact.com/getting-started-with-performance-testing-for-developers)
- [Cloud execution]({{ site.baseurl }}{% link _v4/guides/cloud-execution.md %})
- [Authenticating with LoadImpact cloud]({{ site.baseurl }}{% link _v4/guides/logging-into-cloud-service-from-k6.md %})
- [Custom metrics]({{ site.baseurl }}{% link _v4/core-concepts/custom-metrics.md %})
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})
- [Module Imports]({{ site.baseurl }}{% link _v4/core-concepts/module-imports.md %})
<!-- - [integrations]({{ site.baseurl }}/4.0/integrations/) -->
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE5NTM3MDIwNzldfQ==
-->
