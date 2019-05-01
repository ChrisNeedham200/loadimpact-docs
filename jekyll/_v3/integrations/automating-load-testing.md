---
layout: classic-docs
title: Automating Load Tests with the LoadImpact API
description: Methods on how to automatically trigger load tests utilizing LoadImpact's API or CLI
categories: [integrations]
order: 1
redirect_from:
  - /knowledgebase/topics/43408-integrations
  - /knowledgebase/articles/833856-automating-load-testing-with-the-load-impact-api
  - /knowledgebase/articles/265475-using-the-load-impact-api-to-run-tests-programmati
  - /knowledgebase/articles/337173-install-and-configure-the-load-impact-plugin-for-t
---

***

Automating load testing as part of a CI Pipeline/build process is becoming increasingly popular and a best practice recommendation. How often you run these automated tests will depend on the individual needs of your organization. Our general recommendation is [with your nightly builds](http://blog.loadimpact.com/how-often-you-should-load-test) as that seems to be the closest to a one-size-fits-all approach. If you are using a Continuous Integration tool or want to build something yourself, we recommend one of the options below. You can also [schedule tests]({{ site.baseurl }}/3.0/test-configuration/scheduling-tests/) to run at regular intervals within our tool.

Before you start with either method below, you should have a test that you want to automate as well as [thresholds]({{ site.baseurl }}/3.0/test-configuration/thresholds/) set up to be your pass/fail criteria. The exact criteria will depend on your needs, but you should think about:

- What's an acceptable response time for a resource/page/API call?
- What's an acceptable failure rate?
- Are any failures acceptable?
- What are my most critical requests?



### A. Using our CLI

To use the CLI, we have built [this walkthrough](https://app.loadimpact.com/integrations?appcue=-KxO0Dac8E6nZT9p9Fiy&utm_campaign=Appcues%20related%20links&utm_source=CI-tutorial) you can use in app. (You must be logged in)

If you don't like guided walkthroughs, here are the steps for you to follow:

1. Get your v3 token [here](https://app.loadimpact.com/account/token) (If you already have a token, regenerating one will replace the old one. Only do that if necessary)
2. Download the CLI to the CI server. You can access our [GitHub repo](https://github.com/loadimpact/loadimpact-cli) for specific instructions. If you use pip, you can run the command `pip install loadimpact-cli`
3. Once installed and configured with your token. Add a CLI call as a build step loadimpact test run `TEST_CONFIG_ID`
Your test ID can be found by navigating to your desired test and grabbing the the test ID from the URL. i.e. : `https://app.loadimpact.com/tests/XXXXXXX`

### B. Using our Developer API

[LoadImpact provides an API](http://developers.loadimpact.com/api/) that other applications can use to access the LoadImpact platform.
To get access to the API (version 2) you need an API token.

Your API token provides access to your LoadImpact account, so do not make it public and be careful with whom you share it.
If you have an existing API token you can deactivate it by generating a new one.

Each token for the API (version 2) is unique to a user and organization and is restricted based on organization roles as follows:
- Owner or Admin members can manage API tokens.
- Read/Write members can not access API tokens.

You will need to use an API token (version 2) when using the [LoadImpact API ](http://developers.loadimpact.com/api/)to programmatically manage your user scenarios, test configurations and test runs.

`$ curl -X POST https://api.loadimpact.com/v2/test-configs/X/start -u "API_TOKEN:" {"id": TEST_RUN_ID_OF_STARTED_TEST}`

The LoadImpact API is commonly used when you need to automate your load testing efforts by integrating with
your **Continuous Integration & Deployment** process to detect problems and performance regressions early in the development cycle.

LoadImpact can be [integrated](https://loadimpact.com/integrations/) with different services using our [SDKs](http://developers.loadimpact.com/sdk) and [examples](https://github.com/loadimpact/loadimpactapi-samples) in different programming languages.

Note: The [Command line interface](https://github.com/loadimpact/loadimpact-cli) is for API version 3 (Token is available in app on the [integrations tab](https://app.loadimpact.com/account/token)).
The version 3 API will replace the version 2 in the near future but for now it can only be used in the LoadImpact CLI.
