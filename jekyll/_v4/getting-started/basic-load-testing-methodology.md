---
layout: classic-docs
title: Basic Load Testing Methodology
description: A simple methodology to help you start testing
categories: [getting-started]
order: 4
redirect_from: /4.0/getting-started/load-test-preparations/
---

***

## Purpose


This methodology is intended to walk through the high level steps that will help you get started and running more meaningful tests, faster. The best tests are the ones that simulate the most realistic conditions and user behavior. However, simple testing is better than no testing. It's easy to become overwhelmed if you attempt to do everything all at once. Treat testing like you would development, _start small and iterate, iterate, iterate._

## What am I testing?

If you have not already, you start thinking about any or all of the following:

- What are my users doing?
  - What specific actions do they take the most?
  - What actions are most critical on my app/website?
- What API endpoints are utilized the most?
  - What API endpoints are most critical?
- What response times are acceptable to my users?
  - What response times are acceptable to me?
- What SLAs, contracts, or regulations must I adhere to?

### When do I run tests?

As the world shifts left with DevOps, testing earlier in the development cycle is becoming the best practice. After an initial testing project, many users decide to continue to run tests, at a smaller scale, so they can identify performance issues immediately after the line of code that caused it, is built.

### Virtual Users or Requests Per Second?

This depends on what you are testing, and there is a good chance you want to think in terms of both. Virtual Users are complex workers that can open multiple connections in parallel, thus making multiple requests per second.

#### Virtual Users
When testing anything that is "User journey" related, webapps, websites, or API endpoints in a specific order you should think in terms of Virtual Users. It's important to also note that Virtual Users are concurrent, unless specified otherwise, they will continue to iterate through their script until the test is complete. A small number of Virtual Users can create a number of sessions magnitudes higher than their count. _This is a very common point of overestimation we see._

#### Requests per second
When testing individual API endpoints it makes more sense to think in terms of request per second. As mentioned above, LoadImpact's virtual users are complex and can make multiple requests per second each. For example, if you wanted to test an endpoint with 100 rps you wouldn't need 100 virtual users in nearly all cases.

### Calculating Virtual Users

We recommend looking into any analytics tools you may have. If they list any metric for concurrent users, you can likely use that. If not, you can instead look for:

- Hourly Sessions
- Average session duration for those Users

If you are looking to stress your system to see what happens as you exceed your busiest traffic, you should look for the Peak hourly sessions and it's coordinating average session duration.

For any tests you may run on a regular basis, as part of a CI pipeline or otherwise, you should instead look for your average hourly sessions, or lower, depending on your needs.

With these two metrics, you can determine the number of Virtual Users needed, using the following formula:

`VUs = (Peak Hourly Sessions * Average Session Duration in Seconds) / 3600`

Depending on your goals, you may want to increase this number to provide a cushion beyond your expectations (15-30% is typical).

### Plan your first tests

You should plan to run the following tests:

1. Baseline Test "Performace under ideal conditions"
  - Small test relative to your total Virtual User needs
  - Run for at least 5-10 minutes
  - Used to set a baseline metric for you to compare future tests against
  - You may want to set Thresholds based on this test
2. Stress test "Where do things break?"
  - The test you likely will run the most
  - Used to gradually increase load and identify performance problems
  - Iterate often-  analyze results, make changes, verify with another test
3. Load Test "Are my changes working?"
  - The test you run to see if all your hard work in step 2 paid off
  - Used to verify your system handles the load through the entire test
4. Continuous test "Is my performance regressing as new code/infrastructure updates are deployed?"
  - Used to monitor for performance regressions over time
  - Larger than a baseline test but smaller than a load test
  - Most commonly integrated as part of automation, such as a CI pipeline

Next step: [Create a user scenario]({{ site.baseurl }}/4.0/getting-started/create-a-user-scenario/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTQ2MTgzNjgyOV19
-->
