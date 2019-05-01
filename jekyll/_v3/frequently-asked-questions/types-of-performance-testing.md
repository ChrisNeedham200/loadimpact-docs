---
layout: classic-docs
title: What are the different types of performance tests?
description: Definitions of the most common load and performance tests
categories: [frequently-asked-questions]
order: 3
redirect_from: /knowledgebase/articles/265462-different-types-of-website-performance-testing
---

***


There are plenty of types of testing to keep track on out there, and it doesn’t make it easier that many companies are coming out with their own load testing names. In this series of articles, we will talk about several common types of performance tests that you can carry out on your website and how you can configure your test execution plan to carry out these tests.

Check out this series here:
- [Part 1: The Basics](http://blog.loadimpact.com/blog/different-types-of-website-performance-testing-part-1-the-basics/)
- [Part 2: Load Testing](http://blog.loadimpact.com/blog/different-types-of-website-performance-testing-part-2-load-testing/)
- [Part 3: Spike Testing](http://blog.loadimpact.com/blog/different-types-of-website-performance-testing-part-3-spike-testing/)

If you prefer to not read the complete blog series, here are some hard and fast definitions for you. Please refer to our article on [Ramping configurations]({{ site.baseurl }}/3.0/test-configuration/load-test-ramping-configurations/) for further information:
  - **Smoke Tests:** These tests are run in order to uncover obvious flaws in test scripts, platforms, code, etc. before committing the resources of a full test. This is done at a very basic level when Validating a User Scenario. Depending on your needs, you may want to run a smoke test with more VUs and for a longer period of time.
  - **Load Tests:** These tests are run to evaluate whether or not performance goals are met and to confirm that all system issues have been identified and resolved. 
  - **Max Capacity Tests:** These tests are run to define the maximum number of concurrent users the system can accommodate while continuing to perform as expected.
  - **Stress Tests:** These tests are run to determine a breaking point and to evaluate what happens when the system breaks.
  - **Endurance tests:** These tests are designed to find problems that occur when a system is put under slight pressure for a long period of time (e.g. memory leaks). If a business needs to be functional 24/7, Endurance Tests are essential.
  - **Robustness Tests:** This “functional testing under load” is designed to validate that a system continues to function as expected when put under abnormal pressure. They are useful for evaluating failover and disaster recover procedures.

While there are many different ways to configure and run your tests with the most common one being the Stress Test. However, testing just one way or one thing doesn't give you the full picture. To have the greatest chance of meeting all of your testing goals, we recommend a combination of the above. If you have more questions about which tests will work best for you or how to configure them, please do not hesitate to reach out to the LoadImpact support team.
