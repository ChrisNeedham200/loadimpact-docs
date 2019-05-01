---
layout: classic-docs
title: Simulating realistic Load with LoadImpact
description: "Simulating realistic load with LoadImpact"
categories: [user-scenarios]
order: 2
redirect_from: /knowledgebase/articles/265464-simulating-realistic-load-using-load-impact
---

***

LoadImpact’s basic functionality is geared towards simulating user behavior on your site in the most realistic way possible.

In a real-life situation, if you were to have concurrent users on your website at the same time, you probably won’t have all of them on the same page doing precisely the same things. This is why you can run a test with multiple user scenarios. This allows you to create a variety of realistic user scenarios(test scripts) and have them all execute simulatenously.

Your first step is to create realistic User Scenarios that recreate this behavior. The easiest way to start is to use our [Chrome Extension]({{ site.baseurl }}/3.0/user-scenarios/load-impact-chrome-extension) to browse, like a user would, on your site/app. From here, you should consider:

- What other journeys are most popular for my users?
- Do my users complete forms or login? This data should be [parameterized]({{ site.baseurl }}/3.0//user-scenarios-scripting-examples/data-stores/)
- Are my sleep times representative of real user behavior? i.e. Is client.sleep() included at appropriate parts of your script/journey


The HTTP requests will then be translated to a script when the recording is over, which you can further edit if necessary.
### FAQ

**What happens when I actually start the test?**

So when you start a test, what actually happens is that we will start by running your script with one user, and ramp up to the number of VUs based on your [ramping configurations]({{ site.baseurl }}/3.0/test-configuration/load-test-ramping-configurations/). When the script runs to completion, the VUs will essentially start again. Please note that nothing is saved between iterations. This continues until the test completes. **A small number of VUs can create a large amount of total sessions.**

Now if you look at your script, you will see that there is something called “client.sleep()”. That is the time that the user spends browsing each page, going idle in terms of HTTP(s) requests, and serves as a simulation of what real users do.

Since we add clients to your site in a ramp up situation, what you will get is that some users would be on Page 3 of your script while others are just starting on Page 1. This will mean that each page will never be subject to a load of X number of concurrent users loading resources at the same time.

However, it is important for you to know what your testing goals and objectives are prior to the scripting of the test. If your test objectives are to simulate a marketing campaign where you expect most of your traffic to flood to one page. In this case, you might want to dedicate one user scenario which simulates the repetitive loading of one or two pages.


**What's the difference between an "auto-generated" load script and the one I record with the Chrome Extension or Proxy Recorder?**

Testing a single URL though the "auto-generated" function will not capture the complexity and mix of behaviors your users are likely to exhibit in real-life. The script generated through the auto-generate function will only load objects from the target URL or page (i.e. images, files, HTML), and will not capture the pattern of behavior. This type of test is typically useful for some high level baseline data or if you are testing a landing page.
