---
layout: classic-docs
title: Load Testing with Postman
description: Guide on how to create load tests in LoadImpact from your Postman collections
categories: [integrations]
order: 2
redirect_from: /knowledgebase/articles/894636-load-testing-with-postman
redirect_to: https://blog.loadimpact.com/load-testing-with-postman-collections
---

***

## Background

[Postman](https://www.getpostman.com/) is one of the best-in-market tools for functional testing of APIs. Postman makes it easy for you to develop, document, and test your APIs. But what about when you want to test those APIs beyond single requests. That is where the LoadImpact Postman converter can help.

The **Postman to LoadImpact converter** allows you to utilize your Postman collections as load tests on the LoadImpact platform. Specifically, you can convert them to either a Lua(LoadImpact 3.0) based or JavaScript(LoadImpact 4.0) based script for use with the LoadImpact platform. You can then execute these scripts within the context of a load test, where you decide how many virtual users, length of test, and ramping profile.

Note: this article is about LoadImpact 3.0. If you are looking for LoadImpact 4.0, [click here]({{ site.baseurl }}/4.0/how-to-tutorials/load-testing-with-postman-collections/)

## How to use the converter

Note: Please be sure to sign up for a free [LoadImpact account!](https://app.loadimpact.com/account/register)


1 - Install the Command Line Tool.

  LoadImpact provides CLI tools to convert your Postman tests to both versions of our platform:

  - [Postman to Lua converter](https://github.com/loadimpact/postman-to-loadimpact#installation-and-usage)
  - [Postman to k6/JavaScript converter](https://github.com/loadimpact/postman-to-k6#installation-and-usage)


2 - As a Postman user, you organize your API tests into collections of requests. First, you have to export your Postman collections.

  ![Download Postman collection]({{ site.baseurl }}/assets/img/v3/integrations/load-testing-with-postman/download-postman-collection-1.png)


3 - Use the command line to convert the Postman collection:
  `postman-to-loadimpact path/my-collection.json -o path/my-collection.lua`
  `postman-to-k6 path/my-collection.json -o path/k6-script.js`

4 - _Only LUA tests/scripts:_ Copy the content of the command output into a new or existing user scenario in LoadImpact.

5 - Based on your script, you may need to do some additional scripting for it to validate. Here are common cases:

  5.1  -  Assign Lua variable values.
    The transformer will convert variables as:

    {% highlight lua linenos %}
    {% raw %}
    `{{url}}/repos/{{username}}/{{repository}}/contributors`
    ..url.."/repos/"..username.."/"..repository.."/contributors
    {% endraw %}
    {% endhighlight %}


  At the top of the script, we have auto-generated variables. You must assign values to these. If these are dynamic, you may wish to use a [Data Store]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/data-stores/)

  {% highlight lua linenos %}
      local url = “YOUR_VALUE”
      local username = “YOUR_VALUE”
      local repository = “YOUR_VALUE”
  {% endhighlight %}

  5.2  -  Replicate the behaviour defined in your Postman pre-request and response test.

  This code will be inserted as a comment before and after the Lua request.
  {% highlight lua linenos %}
      --[[
      tests["Body matches string"] = responseBody.has("API endpoint authorized");
      tests["Status code is 200"] = responseCode.code === 200;
      --]]
  {% endhighlight %}
  You could easily replace the code to simulate this behaviour:


  **LUA tests using the Load script API**
  {% highlight lua linenos %}
      if response.status_code ~= 200 then
        log.error("Status code is 200")
         -- or
        result.custom_metric("Status code is 200", 1)
      end
      if not string.find(response.body, "API endpoint authorized") then
        log.error("Body does not match")
        -- or
        result.custom_metric("Body does not match", 1)
      end
  {% endhighlight %}

  **k6 tests using the k6 API.**
  {% highlight js linenos %}
      check(res, {
        "status was 200": (r) => r.status == 200
      });
  {% endhighlight %}
6 - Validate your script and run your test!



See also:
- [Virtual Users, VUs]({{ site.baseurl }}/3.0/test-configuration/what-are-virtual-users-vus/)
- [Requests Per Second, RPS]({{ site.baseurl }}/3.0/test-configuration/what-are-requests-per-second-rps/)
- [How to load test an API]({{ site.baseurl }}/3.0/how-to-tutorials/how-to-load-test-an-api/)
