---
layout: classic-docs
title: Printing debug messages
description: How to utilize the log console to print debug messages in LoadImpact. Helpful for debugging or catching specific pieces of data where a custom metric doesn't make sense.
categories: [user-scenario-scripting-examples]
order: 12
redirect_from: /knowledgebase/articles/174227-how-can-i-set-cookies-in-load-impact
---

***

The [Log module](https://loadimpact.com/load-script-api#log) provides you with functionality to log and display arbitrary (small) text messages during script execution. This is useful while debugging a load script, but also if you want to e.g. verify content that is returned from a server and get an alert if some content is not what you expected. All log messages are displayed on the log tab of the test result page. Of course, if you use the “validate” functionality to validate a user scenario, you never visit the test result page and in that case your log messages will be displayed in the right part of the editor window instead. It's generally a best practice to limit logging during a test. If you are keeping track of something of interest, i.e. a failed response. It is better to create a [custom metric]({{ site.baseurl}}/3.0/user-scenarios-scripting-examples/creating-custom-metrics/).

There are several log functions that perform the same, basic action (log text) but that formats the text slightly differently. You have:

Function    | Definition
------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
log.info()  | Standard log function. Causes text to be displayed at the bottom of the test result page for the test that was executed
log.debug() | This does the same as the log.info() function, but the text will be displayed with a slightly different background color, to make it stand out from regular log.info() messages
log.error() | This logs text with a red background, to make it really stand out from other text. Only use this to log situations you consider an error.
{: class="table table-bordered"}


#### Example:
{% highlight lua linenos %}
-- Make an HTTP request and verify returned content. If it is not what we expect
-- we log an error using the log.error() function
local request = http.request({"GET",     "http://test.loadimpact.com/pi.php?decimals=18",
    response_body_bytes=100  -- Tell the system it should store up to 100 bytes of body data for us
})
if request.body ~= "3.141592653589793238" then
    log.error(
      "PI calculator returned unexpected result when stressed with "
         .. test.get_clients() .. " simulated clients: " .. request.body
    )
end
{% endhighlight %}

Limitations
In order to prevent excessive logging by a malfunctioning or badly programmed script, we limit the number of messages that can be logged in any given 3-second reporting time period (refer to - [What result metrics are reported for a URL]({{ site.baseurl }}/3.0/test-results/what-metrics-are-reported-for-a-url/)? for more info about how reporting data is stored and transferred) to 100 messages. This means that if your script logs 1000 messages within 3 seconds, chances are you will only see the first 100 of those messages. The rest will be quietly discarded by the load generator application.

It's also important to note that you must set `response_body_bytes=` when capturing the response. Failure to set this will result in an empty response body. In the example above, we set the response body to 100 bytes. The full response body or the limit up to the number of bytes captured is available. That is, you can run a string.match on the entire response even though we only will display 1024 bytes in the log window(explained below).

Each log message can also be max 1024 bytes (characters) in length before it is truncated. I.e. if you try to log a text message that is 1100 bytes long, the actual text that is logged will only be the first 1024 bytes of your message, the remaining 76 bytes will be lost.

If you know the data you are looking for you can workaround this limit by conducting a string search. Once you find the position of the data, you can then avoid logging the output before that point. Here is an example of a string search:
{% highlight lua linenos %}
local s = response[1].body
local position = string.find(s, "csrf%-token")
if position == nil then
log.info("Failed to find string: csrf-token")
else
log.info(string.sub(s, position))
end

-- note the % to escape the Lua magic character "-"
{% endhighlight %}
