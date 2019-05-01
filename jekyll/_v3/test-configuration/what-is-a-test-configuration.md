---
layout: classic-docs
title: Test Configuration
description: In depth guide on what a test configuation is and how to use it in LoadImpact 3.0
categories: [test-configuration]
order: 1
redirect_from:
  - /knowledgebase/articles/174522-what-is-a-test-configuration
  - /knowledgebase/topics/118842-test-configuration
  - /knowledgebase/articles/174522-what-is-a-test-configuration-
---

***

A test configuration allows you to define how the load test will execute:
- How many Virtual Users will be simulated
- What User Scenarios(journeys) they will be taking
- What load zones they originate from
- How they should ramp up and down and for what length of time the test should run
- Other optional features such as server monitoring, URL grouping and thresholds

Start by click on "New Test" from your Dashboard

![Click create new test]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-1.png)


In test configuration you have to supply [User Scenarios]({{ site.baseurl }}/3.0/user-scenarios/what-is-a-user-scenario/) to the site you want to test. You can configure your test to use one or more user scenarios. It is recommended to use our [Chrome Extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension/) or [Proxy Recorder]({{ site.baseurl }}/3.0/user-scenarios/load-impact-proxy-recorder/)  to create User Scenarios based on real user behavior.
Start by clicking "Add User Scenario"
![Click add User Scenario]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-2.png)

After clicking, you can select scenarios you've already created from the drop down, Alternatively, you can enter a URL for a quick scenario. URL generated scenarios will only load content from the target domain.
![Choose your scenario]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-3.png)


You are also able to assign the User Scenarios to different Load Zones using the drop down menu below the scenario name. You are able to add the same scenario multiple times if needed. Each unique User Scenario/Load zone will be given it's own Load Generator at minimum, you can have up to 10 individual User Scenarios in a single test.

![Select your Load Zones]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-4.png)

After adding your User Scenarios you are able to further configure and change Traffic Simulation settings.

Virtual User Distribution across the scenarios added (if you have two or more scenarios in a single test). A Stress Test pattern is used in the example below. See our article on [ramping configurations]({{ site.baseurl }}/3.0/test-configuration/load-test-ramping-configurations/) for more information.

![Define your distribution]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-5.png)



Under Advanced Settings:

**Multi-step ramping** - Allows for granular control of how many Virtual Users are active, up to 10 steps. In the below picture, we use a stress test ramping pattern. More patterns can be found in this article.

**Network Emulation **- Select a browser or network speeds to emulate for the entire test. You are also able to set custom User Agents and Max Connections using our Load Script API on a per User Scenario basis

**Source IP Multiplier** - Up to 2x for Standard plans and above. This will increase the number of Load Generators in the test. Virtual Users will be distributed as evenly as possible, based on current distribution.

![Define your ramping pattern and network options]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-6.png)





Finally, we also have three optional configuration options for you to use:

**Thresholds** - Allow you to set a binary pass/fail criteria that can also automatically abort the test

**Server Monitoring Agents** - Using either our Open Source Agents or our integration with New Relic you are able to report back on various server side metrics for correlation in your test results in LoadImpact

**URL Grouping** - Recommended for tests against systems that generate unique identifiers in the URL. This allows you to group similar URLs together and stay organized when analyzing results.

![Configure your other options]({{ site.baseurl }}/assets/img/v3/test-configuration/what-is-a-test-configuration/what-is-a-test-configuration-7.png)
