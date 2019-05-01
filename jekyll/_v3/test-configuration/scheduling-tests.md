---
layout: classic-docs
title: Scheduling Tests
description: How to schedule tests in LoadImpact. Scheduled tests can be one time or recurring.
categories: [test-configuration]
order: 5
redirect_from: /knowledgebase/articles/602886-scheduling-tests
---

***

LoadImpact allows the option of scheduling a test in advance and scheduling recurring test runs. Since we automatically graph performance trending for test runs, we recommend using the scheduling feature to run tests on a regular basis. This provides you with an easy to understand view of how your [performance is trending]({{ site.baseurl }}/3.0/test-results/interpreting-the-performance-trending-graph/) over time.

**Note:** LoadImpact can be integrated with Automation Pipelines. To integrate and automate tests, refer to [this article]({{ site.baseurl }}/3.0/integrations/automating-load-testing/).

You can schedule tests to recur:
- Hourly
- Daily
- Weekly
- Monthly



Tests can be scheduled by clicking on "Schedule" in the top right corner within a test result overview:

![Scheduling tests]({{ site.baseurl }}/assets/img/v3/test-configuration/scheduling-tests/scheduling-tests-1.png)


**Note:** The time zone for scheduling will be dependent on the Time Zone you've selected under your [User Profile Settings](https://app.loadimpact.com/account)



After clicking, the test schedule pop-up window will appear

![Scheduling tests]({{ site.baseurl }}/assets/img/v3/test-configuration/scheduling-tests/scheduling-tests-2.png)

You may want to setup Notifications to be aware when a test has been started and completed.


Once complete, click `Save Schedule`.
