---
layout: classic-docs
title: Creating tests in LoadImpact
description: How to create a test within LoadImpact's web interface using the different methods avaiable
categories: [getting-started]
order: 1
redirect_from:
  - /4.0/getting-started/create-a-user-scenario/
  - /4.0/getting-started/create-a-load-impact-test/
---

***

## Purpose

At the heart of every test in LoadImpact is a JavaScript file(we call it a test script).  This file controls the test configuration as well as the requests being made.  There are multiple ways you can generate this script through our web interface.  You can also write it by hand completely.

## Things to consider

As mentioned in [load test preparations]({{ site.baseurl }}/4.0/getting-started/load-test-preparations/), a key to good testing is understanding what your users are doing, what services are most critical to your service, and what things are most critical to your organization. Knowledge of these things will guide you to create tests that are realistic and meaningful. If you are new to testing, you may want to read our [Testing Methodologies]({{ site.baseurl}}/4.0/testing-methodologies/)

We will not explicitly cover options within this article.  Options allow you to configure the ramping profile and other built in functionality. A default structure is provided for tests created from either the Chrome Extension or uploading a HAR file. For more information on options, refer to [this article]({{ site.baseurl}}/4.0/reference/test-configuration-options/). To alter the test length or number of VUs, refer to the stages property within the test script.

## Creating your test scripts

From within the LoadImpact web interface, there are a few different ways to create your test scripts. The test script itself is where your entire test is defined. Every test in LoadImpact is driven from a JavaScript file.  Since test scripts are expressed as code, this gives you flexiblity and fine tune control over how they execute. You can start creating your tests in app, [here](https://app.loadimpact.com/k6/tests/new).

### Entering Website URLs (URL Generator)

This method of test script creation is the simpliest. It allows you to enter multiple URLs, select a number of Virtual Users, test duration, set a ramping profile, and
load zones where the test will execute from. You are also able to whitelist so you are only testing certain domains.

**Important**:  This test will only make GET requests and is not intended to test APIs. If you are testing APIs, please select [API test scripting option](https://app.loadimpact.com/k6/tests/custom/editor?type=api). Scripting allows you to define payloads, make different HTTP requests, alter headers, etc.

### Recording a browser session - Chrome extension

The LoadImpact Chrome Extension is an easy way to create a test script, simply by browsing like a user would. To use the chrome exstenion you would:

1. Start recording
2. Browse across multiple pages/actions, like a user would
3. Stop recording
4. Edit your script, if necessary

To start using the chrome extension, you can start in app [here](https://app.loadimpact.com/k6/tests/recording-instructions)

### Recording a browser session - HAR file

The HAR file converter allows you to convert a HAR file into a test script.  This enables you to create a HAR file from any method you would like. The LoadImpact web interface allows you to upload a HAR file for us to convert. For more information on create HAR files please refer to [this article]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-do-browser-recording/)

### Scripting

The final way to create your test would be to write it by hand.  When choosing one of the in app options, we will provide a sample script for you to follow.  Test scripts
are written as JavaScript code, so feel free to do programmatic things as you build your proof of concept. Some of the most common scripting tasks are covered in our
[Scripting Examples]({{ site.baseurl }}/4.0/examples/)


## Next steps

After your have recorded/written/edited your test script, go ahead and run it. The platform will read your test configurations and start launching the test on our
infrastructure. Once the first results are returned, you will start seeing them within Insights Result Analysis. While the test is running our
[Performance Alert]({{ site.baseurl }}/4.0/result-analysis/insights-smart-results/) algorithms will automatically detect patterns associated with performance problems.

*Remember:* Testing is an iterative process.  You should expect to run your tests multiple times to identify/fix performance issues, debug your script, etc.

Once your first test results are in, refer to our articles on [Result Analysis]({{ site.baseurl }}/4.0/result-analysis/)

## Common Questions

**I need to parameterize data from a JSON or CSV file, how do I do that?**
In order to include external files in your test script, you would need to trigger your test from the command line. For purposes of a proof of concept, many users
will create a table with necessary data in their test script before moving on to that.  We have code samples of parameterization [here]({{ site.baseurl }}/4.0/examples/parameterizing-data/).

**I'm testing a site with form submissions and keep getting HTTP errors. Why isn't this working?**
Assuming you've used one of the browser recording methods, you likely have captured a CSRF style token associated with your browser session.  You will need to write some
JavaScript code to instruct the VUs how to retrieve and use this value to make it dynamic. We provide a code sample of correlating these types of values [here]({{ site.baseurl}}/4.0/examples/correlating-tokens-dynamic-data-load-test/)

**How are cookies handled? Do I need to do anything special?**
Cookies are handled automatically.  The browser recorders are thorough in what they capture.  This means you may want to remove cookies from your script.  If
you are handling any session tokens in cookies, this is an important step to consider.
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTMzMDE5NjQwOF19
-->
