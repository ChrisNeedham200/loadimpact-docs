---
layout: classic-docs
title: How to Integrate LoadImpact With Jenkins
description: Guide to integrate LoadImpact and k6 into your Jenkins pipeline. Shift left and automate your load testing in CI/CD.
categories: [guides]
order: 2
redirect_from: /4.0/integrations/jenkins-integration/
---

***

<h2>Purpose</h2>

This guide and sample describes how to integrate LoadImpact 4.0 with Jenkins v2 and above. Your exact installation and needs may vary and your business needs should be taken into consideration when designing your integration. This document follows a sample. Supporting files for the below sample are available in our [GitHub repo](https://github.com/loadimpact/k6-jenkins-example).

- TOC
{:toc}

## Background

LoadImpact and the open source load generator, k6, integrate nicely with Jenkins, a leading continuous delivery and integration automation platform. Utilizing the robust and extensible k6 load generator you can integrate testing into your automated Jenkins pipeline, build, and test process. This enables you to automatically run tests against your websites, APIs, microservices, web apps, or anything else you may want to monitor for performance regressions over time.

It's highly advised to utilize thresholds in automated tests, so you can pass or fail your builds based on the outcome of your performance test. Refer to [this article]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %}) for more information on thresholds.

## Prerequisites

- A LoadImpact account
  - Trial or paid plan
- LoadImpact API key
- Test scripts for what you want to test
  - You may utilize our script in lieu of one of yours
- Familiarity with Jenkins

## Your test script

Here is a sample test script to use in context of this example. It tests our example site, `test.loadimpact.com`. If you are following along exactly, you don't need to save this anywhere. This example will pull the script from out [GitHub repo.](https://github.com/loadimpact/k6-jenkins-example)

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

Here is a description of what the script does:
1. The test will ramp up from 0 to 10 users over 60s, stay flat at 10 users for another 60s, before ramping down to 0 users again over 60s
2. We've set a goal that the 95th percentile response time should be below 500ms. **This step, k6 [thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %}), is essential, it's how we define our goals which is the basis for the pass/fail mechanism to work that is built into k6**. If a threshold fails, k6 will end with a non-zero exit code, which in turn indicates a failed build step to Jenkins (and other CI tools).
3. In the load test "main" function we define a group Front page inside which we make a request, [check]({{ site.baseurl }}{% link _v4/core-concepts/checks.md %}) the response status code and sleep for 10s before letting the user loop from the top of the function.



## API Token

Before you jump into Jenkins, you should acquire our API token. This token allows you to communicate with the LoadImpact platform. To get your token, go [here](https://app.loadimpact.com/integrations) and select User Token.

IMPORTANT:  Generating a new token will discard any previous token you may have. You may reuse the same token for multiple services.

Copy your API token and keep it handy, you will need for the next part, for inserting in your Groovy script.

## Configuration

Next, create a Jenkins pipeline with the name `k6_jenkins_demo`

![Step 1, create a jenkins pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-pipelines-create-step-1.png)

A blank pipeline isn't too interesting to look at, here it is after a few executions:

![Step 2, example of the pipeline after a few executions]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-pipelines-execute-step-2.png)

Now, lets look at the configuration of the pipeline:

![Step 3, configuration of the pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-pipelines-config-step-3.png)

For purposes of this sample, we've set up a pipeline that only executes a load test. You can include additional things, as required. This is done within the pipeline section:

![Step 4, the Groovy script in our pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-pipelines-script-step-4.png)

The above is a Groovy script. In the next section, we will look at that in more detail.

## The pipeline

{% highlight groovy linenos %}
node {

    stage ("Build stuff") {
    }

    stage ("test") {
        env.K6CLOUD_TOKEN="INSERT_MY_API_TOKEN_HERE"
        if (isUnix()) {
            sh "k6 run --quiet -o cloud github.com/loadimpact/k6-circleci-example/loadtests/main.js"
        } else {
            bat 'k6.exe run --quiet -o cloud github.com/loadimpact/k6-circleci-example/loadtests/main.js'
        }
    }

    stage ("Done") {
    }
}
{% endhighlight %}

IMPORTANT: Before running, make sure k6 is installed in the executable PATH of your build node. Install instructions can be found in [here.]({{ site.baseurl }}/4.0/getting-started/hello-world/#download-and-install-k6)

The code has three parts/stages. "Build stuff", "test", and "Done". If you are familiar with Jenkins pipelines, you are likely aware that the stages will be visible in Jenkins when you execute your pipeline. k6 will pick up the API key from the environment variable `K6CLOUD_TOKEN` when executed.

The first stage, "Build Stuff" is just a placeholder in this example. The same is true for the last stage, "Done". In your implementation you will likely have a number of stages where you do different tasks or run other tests.

In this example, the load test is triggered in the "test" stage. We first set our `K6K6CLOUD_TOKEN` as an environment variable, if you are following this example exactly, replace `INSERT_MY_API_TOKEN_HERE` with your actual token. Next, we check if the build node the pipeline is being executed on is a windows or unix box. We then execute the correct command based on the outcome of that.

Note: This test will execute k6 locally, and stream results to the LoadImpact cloud service. Alternatively, you can execute solely on the LoadImpact cloud service by

## Execute

We can now move back to Jenkins and execute the pipeline. In our dashboard, we only have a single pipeline, for demonstration purposes.

![Our Jenkins dashboard]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-blue-ocean-dashboard.png)

Open the pipelines

![The opened pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-blue-ocean-pipeline.png)

Run the pipeline and open the running pipeline

![Our running pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-blue-ocean-pipeline-running.png)

Once it moves into the running the state, it will look something like this

![Result of the running pipeline]({{ site.baseurl }}/assets/img/v4/integrations/jenkins-integration/jenkins-blue-ocean-pipeline-results.png)

If the load test is successful, our build will also be successful and the build will continue on. If the test fails, it will return a non-zero exit code which will fail the build step.

## Result analysis

Passing builds aren't too interesting. When builds fail, you will want to determine why. When it comes to load tests, there is no exception to be made. Insights, our result analysis tool in the LoadImpact cloud is built to be the perfect companion to k6. Insights is designed to be error driven, this helps you to quickly find the cause of the failed load test. Refer to [this article]({{ site.baseurl }}/4.0/result-analysis/insights-overview/) for more information on result analysis.

## See also
- [GitHub repo for this example](https://github.com/loadimpact/k6-jenkins-example)
- [Installing k6]({{ site.baseurl }}/4.0/getting-started/hello-world/#download-and-install-k6)
- [Your API token](https://app.loadimpact.com/integrations)
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})
- [Insights Overview]({{ site.baseurl }}{% link _v4/result-analysis/insights-overview.md %})
- [Checks]({{ site.baseurl }}/4.0/core-concepts/checks/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTc1MTk1MTcwMV19
-->
