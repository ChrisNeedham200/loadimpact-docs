---
layout: classic-docs
title: Using the LoadImpact Chrome Extension
description: "How do I use the LoadImpact Chrome Extension to record user scenarios?"
categories: [user-scenarios]
order: 3
redirect_from: /knowledgebase/articles/174664-how-do-i-use-the-chrome-extension-to-record-user-s
---

***


Create a complex User Scenario with just a few clicks. Record all HTTP traffic and use those recordings to simulate real user traffic under realistic load.

The LoadImpact Chrome extension will capture everything – every single HTTP(s) request being loaded into the browser as you click – including ads, images, documents, etc., so you get a far more accurate read of what’s going on. Just press “record”, start browsing and when complete, the script will automatically upload to your LoadImpact account.

_**Consider this:** The Chrome extension will not record other tabs or pop up windows. If you need to capture this information, you should check out our Proxy Recorder._

Here's how to start:

1. Install the [LoadImpact Chrome Extension through the Chrome Web Store ](https://chrome.google.com/webstore/detail/load-impact-user-scenario/comniomddgkfgfaebhidfgcjgoecbbda)or by navigating to "User Scenarios" on loadimpact.com and select New user scenario.
2. Configure your settings to include different URL patterns _(Optional/Useful to only capture requests to certain domains)_
3. Start recording your session by clicking "Start recording". At any point, you can choose to stop, pause or reset the recording
4. Once you've started recording, begin to browse and behave as a typical user would. Click stop when you are done.
5. As long as you are logged in, you will be brought into the platform and are able to give your script a name and assign it to the proper Project and Organization
6. You can now edit your script as necessary, [validate it]({{ site.baseurl }}/3.0/user-scenarios/validation-function/) and then include it in a [test configuration]({{ site.baseurl }}/3.0/test-configuration/what-is-a-test-configuration/).



Based on your needs, you may want to further edit the script to parameterize data, handle tokens, add more advanced business logic then edit, update, and include in your test configurations.
