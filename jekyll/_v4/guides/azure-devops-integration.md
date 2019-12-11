---
layout: classic-docs
title: How to Integrate Azure DevOps/Pipelines With LoadImpact/k6
description: Guide on how to integrate your Azure DevOps Pipelines with LoadImpact and k6.  Shift left and automate your load testing in CI/CD.
categories: [guides]
order: 4
redirect_from: /4.0/integrations/azure-devops-integration/
redirect_to: https://blog.loadimpact.com/integrating-load-testing-with-azure-pipelines
---

***

<h2>Purpose</h2>

The intent of this guide is to walk you through the steps of triggering tests as part of your Azure Pipelines within Azure DevOps. Before you get started go to [https://dev.azure.com/](https://dev.azure.com/) and click _Start Free_ or login with your existing Microsoft account.

After a successful login, follow the instructions below.


## Run k6 using Azure Pipelines from your GitHub project

Create a new project (or use an existing one)

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/01.png)

Click on _New Pipeline_ button.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/02.png)

For this example, we will use GitHub as the repository for our code. If your code is elsewhere, the next few steps will vary for you.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/03.png)

You will need to have a project on GitHub for this exercise.  Either create a new one or choose one of your existing ones. Click the _Authorize_ button to connect your repo to Azure Pipelines.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/04.png)

If you are a member of multiple organizations select the organization that contains your project.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/05.png)

Choose to install Azure Pipelines into all repositories or just into a single repository

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/06.png)

Once you finish giving Azure Pipelines access to your project, you can begin configuring your your integration.

## Azure Pipelines config

Create a new file in the root of your repo named `azure-pipelines.yml`.

Azure provides us with ready-made pools to run our pipeline agents in. Having a choice between a Windows, MacOS and Ubuntu agent I've selected `Ubuntu 16.04`. That gives us the easiest way to install k6 tool through shell commands.

We can start our example by defining the base image for our agent:

azure-pipelines.yml
{% highlight yaml linenos %}
pool:
  vmImage: 'Ubuntu 16.04'
{% endhighlight %}

Commit this new file and push the code to remote. On each push, a build is automatically triggered.

## Installing k6

For now, our build does nothing useful. Let's change that.
We are going to create our first script task within our pipeline step. On it's GitHub page, k6 provides us with clear instructions [how to install k6 on a Debian based system](https://github.com/loadimpact/k6#linux) so we can copy that verbatim as a multiline script with a descriptive `displayName` attribute.

To make sure that k6 is installed properly we can add a new script task that just outputs version of our k6 install. This task is optional and can be removed in real-life scenarios.

For this example, we are using a single-job build so the `jobs` section is omitted.

At this point, we have the following:

azure-pipelines.yml
{% highlight yaml linenos %}
pool:
  vmImage: 'Ubuntu 16.04'

steps:
- script: |
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61
    echo "deb https://dl.bintray.com/loadimpact/deb stable main" | sudo tee -a /etc/apt/sources.list
    sudo apt-get update
    sudo apt-get install k6
  displayName: Install k6 tool

  - script: |
    k6 version
  displayName: Check if k6 is installed
{% endhighlight %}

Commit and push the code. Go to Pipeline's web UI and check the output for the steps. You should see something similar to the following:

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/07.png)

## k6 run

We have k6 installed in our agent so it's time to run a proper load test.
k6 uses JavaScript as it's scripting language making it very flexible to write any kind of load testing scenario.

In this example, we will be testing a single webpage. We are ramping up for 10s from 1 to 15 virtual users, stay at that number of virtual users for 20 seconds, and then ramp the test down to 0 virtual users. The duration of your test and number of virtual users used will depend on your needs.

We have named this test `local.js` since it is being run locally straight from Azure Pipelines agent.  Local execution is helpful for script validation or even testing behind the firewall.  **Note:** Locally executed tests can stream results into LoadImpact's cloud service for analysis.  You are able to do this by setting the flag `-o cloud`

Since we are planning to have multiple test scenarios, create a separate directory `loadtest` in the root of the repo and place `local.js` file within it.

loadtests/local.js
{% highlight js linenos %}
import { check, group, sleep } from "k6";
import http from "k6/http";

export let options = {
    stages: [
        { duration: "10s", target: 15 },
        { duration: "20s", target: 15 },
        { duration: "10s", target: 0 }
    ],
    thresholds: {
        "http_req_duration": ["p(95)<250"]
    },
    ext: {
        loadimpact: {
            name: "test.loadimpact.com"
        }
    }
};

export default function() {
    group("Front page", function() {
        let res = http.get("http://test.loadimpact.com/");
        check(res, {
            "is status 200": (r) => r.status === 200
        });
        sleep(5);
    });
}

{% endhighlight %}

In `azure-pipelines.yml` config file we add a new script task to run k6 load test directly from Azure Pipelines agent:

{% highlight yaml linenos %}
steps:
# ...
- script: |
    k6 run loadtests/local.js
  displayName: Run k6 load test within Azure Pipelines
{% endhighlight %}

Once again: commit and push.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/08.png)

If you see the above screen, congrats! You now know how to set up a GitHub project CI build to run on Azure Pipelines and use LoadImpact for load testing!

## k6 cloud run

k6 can also be used to execute tests directly on the LoadImpact's cloud service.  This enables you to geographically distribute the origin of your load and not worry about maintaining any load generators.

Our test script is nearly identical to the one presented above, with a small change to our options section. We have now defined to run our load test from two datacenters (Ashburn and Dublin). For a list of all available Load Zones from which a load test can be run refer to [this article]({{ site.baseurl }}/4.0/guides/cloud-execution/#load-zones).

Let's create a new file in our `loadtests` dir named `cloud.js`.

loadtests/cloud.js
{% highlight js linenos %}
import { check, group, sleep } from "k6";
import http from "k6/http";

export let options = {
    stages: [
        { duration: "10s", target: 15 },
        { duration: "20s", target: 15 },
        { duration: "10s", target: 0 }
    ],
    thresholds: {
        "http_req_duration": ["p(95)<250"]
    },
    ext: {
        loadimpact: {
            name: "test.loadimpact.com",
            distribution: {
                loadZoneLabel1: { loadZone: "amazon:us:ashburn", percent: 60 },
                loadZoneLabel2: { loadZone: "amazon:ie:dublin", percent: 40 }
              }
        }
    }
};

export default function() {
    group("Front page", function() {
        let res = http.get("http://test.loadimpact.com/");
        check(res, {
            "is status 200": (r) => r.status === 200
        });
        sleep(5);
    });
}

{% endhighlight %}

In `azure-pipelines.yml` config file add an additional step or modify your existing step that was running k6 locally.

{% highlight yaml linenos %}
steps:
# ...
- script: |
    k6 login cloud --token $(k6cloud.token)
    k6 cloud --quiet loadtests/cloud.js
  displayName: Run k6 cloud load test within Azure Pipelines
{% endhighlight %}

**Important Note:** Notice the usage of token provided from LoadImpact. You will need to get this before you commit your code.
First, we need to get the LoadImpact token and set Azure Pipelines secret variable.

You can obtain your token from your account at [loadimpact.com](https://loadimpact.com). Go over to [_Integrations_ section](https://app.loadimpact.com/integrations) of the page and click [_Use your token_](https://app.loadimpact.com/account/token) link. Copy the provided token.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/09.png)

Now we need to add an Azure Pipelines variable that will be available within the build.

Go to Azure Pipelines web UI and from the left side menu select _Pipelines_ and then click the _Edit_ button next to the name of your project.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/10.png)

Once you enter the options for your project switch to _Variables_ tab within the web UI and enter a new secret variable that will be used during k6 cloud execution.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/11.png)

Click _Add_ to add a new variable.
Under name enter `k6cloud.token` and for its value paste LoadImpact's token.
Don't forget to set the variable as secret so that it's not visible as plain text in your pipelines output.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/12a.png)

After entering the values click _Save & queue_ button above and into field _Save comment_ enter `adding k6cloud.token env var`.

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/12b.png)

Now, you can push your new `loadtests/cloud.js` script alongside new Pipelines script task to trigger a new build.
You can see some basic output from k6 in Azure Pipelines web UI, but for a more in-depth view and analysis of your test go to [LoadImpact's web UI](https://app.loadimpact.com).

![]({{ site.baseurl }}/assets/img/v4/integrations/azure-devops-integration/13.png)

## See also/Reference

 - [All code used in this article is available in a public GitHub repo](https://github.com/loadimpact/k6-azure-pipelines-example).
 - [Your LoadImpact token](https://app.loadimpact.com/account/token)
 - [Load Zones available for Cloud Execution]({{ site.baseurl }}/4.0/guides/cloud-execution/#load-zones)
 - [Create an account or Login to Azure DevOps](https://dev.azure.com/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzMzEzOTQwNDhdfQ==
-->
