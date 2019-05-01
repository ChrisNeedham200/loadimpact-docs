---
layout: classic-docs
title: Scripting Introduction
description: Introduction to scripting in Lua for LoadImpact 3.0
categories: [user-scenario-scripting-examples]
order: 1
redirect_from:
  - /knowledgebase/topics/117699-user-scenario-scripting
  - /knowledgebase/articles/835701-scripting-introduction
  - /knowledgebase/topics/23898-scripting-examples
  - /knowledgebase/articles/174637-lua-load-script-tutorial
---

***

In order to create much more complex behavior from the simulated clients in a load test, you might want to take a look at the scripting functionality of LoadImpact. By using the high-level programming language Lua together with the LoadImpact API you can make your simulated clients perform very advanced tasks on the site being tested.

The User Scenario (load script) is a program that describes what each simulated client should do during the load test. Every Virtual User will run its own execution thread, executing the User Scenario until it runs to completion. After that, the VU will run the load script again and again, repeating it infinitely until the load test is finished. During ramp downs, we will stop VUs from executing during the ramp down period.

In this section we will provide you with a set of examples on how to create load scripts that perform several common actions.

Refer to our full list of examples: [Scripting Examples]({{ site.baseurl }}/3.0/user-scenario-scripting-examples/)
