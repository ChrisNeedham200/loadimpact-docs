---
layout: classic-docs
title: New Relic APM in your LoadImpact Test
description: LoadImpact's integration with New Relic allows you to view data from New Relic's APM.
categories: [monitoring-agents]
order: 2
redirect_from: /knowledgebase/articles/752553-monitoring-new-relic-metrics-while-running-a-load
---

***

Monitoring New Relic Server and Application metrics in real time while running a LoadImpact load test gives you a clear picture of your application and infrastructure’s performance under load. This is a helpful aid when identifying or verifying performance problems.

If you are a New Relic client and have the Application Performance Monitoring (APM) product, you can view detailed performance metrics right in your LoadImpact test result, including CPU and APDEX.

### Step 1
Begin by adding your New Relic API key to your LoadImpact account. You can [find your API key](https://docs.newrelic.com/docs/apis/rest-api-v2/requirements/rest-api-key) in your New Relic account, go to: Account settings > Integrations > data sharing > API access.

### Step 2

To add your API key to LoadImpact, in the left side bar, click Integrations then "New Relic"

![Add New Relic API Key]({{ site.baseurl }}/assets/img/v3/monitoring-agents/new-relic-integration/navigate-new-relic.png)


### Step 3

Add your API key in the area shown:

![Add New Relic API Token]({{ site.baseurl }}/assets/img/v3/monitoring-agents/new-relic-integration/add-api-token.png)

Once your API key has been validated by our system, we will add all your New Relic applications/servers to the list under Monitoring on the left sidebar menu. Your API key will be validated nearly instantly. If you don't see any agents after adding your key, you can utilize [this tool](https://rpm.newrelic.com/api/explore/applications/list) from New Relic to investigate/see the response that your API key will generate. If you don't see any applications listed, please check that your installations are correct in New Relic

To collect metrics from New Relic during a test, simply go to the  [test configuration]({{ site.baseurl }}/3.0/test-configuration/what-is-a-test-configuration/) page, under Server monitoring click "add monitoring" and select the desired New Relic application/server you wish to monitor during the test. If you receive an error when adding a metric, please double check that your subscription with New Relic allows you to access their full API.
