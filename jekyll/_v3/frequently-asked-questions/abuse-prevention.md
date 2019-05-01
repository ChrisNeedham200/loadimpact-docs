---
layout: classic-docs
title: How does LoadImpact prevent abuse of it's service?
description: Explanation of how LoadImpact prevents abuse
categories: [frequently-asked-questions]
order: 2
redirect_from: /knowledgebase/articles/173797-how-do-you-prevent-abuse-of-the-service
---

***

LoadImpact might be used by someone to run illegal load tests on a site, effectively causing a denial-of-service attack on the site in question. We implement a number of measures to make our service unattractive to potential attackers. These measures include, but are not limited to:

- Anonymous tests are limited in size and duration
  - The anonymous tests ramp up from 1 to 50 VUs during a 5-minute period. The maximum load level at the end of the 5 minutes will be equal to about 15 real users accessing the site simultaneously.
- Byte and transaction counters
  - We remember every byte transferred to/from sites (IP addresses) we test, and for anonymous tests we allow only a certain number of tests, bytes or transactions to be executed for a particular target site per 48 hours. This means that an abuser will not be able to run an arbitrary number of anonymous load tests for a particular site. They will be limited to running (small-scale, see above) tests for a total period of 10-15 minutes every 48 hours. This means they will be able to test a site less than 1% of the time, worst case.
- Black listing
  - We continuously monitor the system for abusive behaviour, and if we see anyone testing a site excessively, we will black list the site being tested so noone can test it. We adopt a shoot-first-ask-questions-later policy when it comes to black listing. We will also black list email providers if we detect that people using that provider are registering multiple accounts with us just to gain free credits.
- Disabling accounts
  - In the event that abuse is reported, we disable the account which ran the test from running additional tests. We then reach out to the account owner and open up a dialogue to determine if this was intentional, a misunderstanding or something else. While abuse reports are extremely rare, nearly all cases are a result of a miscommunication between teams.


We welcome people to contact us in case they notice any abusive behaviour, which will be swiftly dealt with.
