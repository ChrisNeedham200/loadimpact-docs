---
layout: classic-docs
title: What are LoadImpact's definitions of concepts and terms?
description: Definitions of concepts and terminology commonly used in Load Testing as it relates to LoadImpact.
categories: [frequently-asked-questions]
order: 1
redirect_from:
  - /knowledgebase/topics/29118-common-issues
  - /knowledgebase/articles/173814-load-impact-s-definition-of-concepts-and-terminolo
  - /knowledgebase/articles/173814-load-impact-s-definition-of-concepts-and-terms
---

***

Most load testing tools have the same basic functionality but choose to call them different things. Below, you will find a summarized list of the terms and concepts we use and how we define them.

**VU** or **User** or **Client** or **Concurrent User** – Virtual User. A simulated user. This User will use multiple TCP connections when requesting resources from the server, allowing it to fetch objects in parallel, just like an actual browser. This user will follow the User scenario associated with it over and over until the test is finished or aborted.

**User scenario** – The User scenario is the script that determines how the Users behave, what they will do and what resources to load, along with any Data stores used by the script. Every User scenario has a name associated with it.

**Test configuration** – A Test configuration is a collection of settings that determines the number of Users to test with and how the number should vary (See Ramping) throughout the test. The Test configuration also specifies what User scenarios to use in the test, what Load zones to run the User scenarios from and the percentage allocation to each Load zone User scenario combination.

**Test result** – The Test result is the actual data collected during the test. The Test result will include load times for individual resources as well as aggregated values such as User load time and Accumulated load time. Bandwidth, requests per second and failure rate is also reported in the result. Non numeric values such as custom log data are also a part of a result.

**Load zone** – A Load zone is a geographical location from where LoadImpact can generate load. These are usually given a describing name, i.e. “Dublin, IE”, “Ashburn, US” etc. Currently all public Amazon AWS regions are available as load zones.

**Load generator** – A cloud server instance that is used to generate load during the load test. Load generators will host a maximum of 500 VUs

**Credits** – Credits are a virtual currency used on LoadImpact to run tests. The number of Credits required to run a test depends on the number of Users in the test and for how long the test is set to run. Credits are usually bought in packages with increasing discount levels.

**Target URL** – The Target URL has two purposes. In connection with User scenarios the Target URL is used when generating a script via either the auto-generate feature or the Proxy recorder. The Target URL is the page that will be analyzed when we use the Page analyzer to create a load script, or the starting point when doing a recording using the Proxy recorder. A Test configuration also has a Target URL. Here, the Target URL is only used to geographically determine where the target system is, so that we can place markers on the world map that shows where traffic is being generated and where it is going when the test is running. If there are no User scenarios specified for a Test configuration (i.e. if there is only one User scenario in the Test configuration, and that one is not changed from its default
value – `[Auto-generate user scenario]`), the Target URL will be used to dynamically create a User scenario every time the test is started, in the same way if the user had used the Analyze page functionality previously mentioned.

**User load time** – User load time is an aggregated result metric. It represents the time it took to load all resources in the User scenario. This excludes any time spent sleeping. If more than one User scenario is used the average value for all User scenarios will be reported. This metric along with the number of Users currently active are the default graphs displayed in the default chart on the test result page.

**Accumulated load time** – The Accumulated load time is a result metric representing the total load time of all individual objects in the User scenario, added together. This is also excluding any sleeps as well as any time spent looking up hostnames in DNS. When multiple User scenarios are used the reported value will be an average of the Accumulated load time for all the User scenarios.

**Ramping up/down** – Ramping up means increasing the number of active Users over a period of time. Ramping down means reducing the number of active Users.

**Queue** – The Queue is a holding area where tests are stored while waiting to be started by a test worker. Whenever a test is in this state, it will appear as “Queued” in the LoadImpact user interface.

**Test worker** – The Test worker is an internal LoadImpact service that is responsible for reserving and booting load generator instances, transferring the needed Test configurations, starting load generation, aggregating results etc. In general, it runs the load test.

**Page analyzer** – A tool for creating User scenarios of single pages. Also has an interface that can be used for analyzing and optimizing client side code, accessed through the footer menu at LoadImpact.

**Chrome Extension** - A tool for creating User scenarios. Records the HTTP requests of a real browser and turns them into a User scenario.

**Proxy recorder** – Another tool for creating User scenarios. Records the HTTP requests of a real browser and turns them into a User scenario.

**Data store** – A Data store is a set of data, parsed from a CSV file. Used in User scenarios and nice to have when handling large amounts of unified data such as usernames and passwords, lists of user agents or similar.
