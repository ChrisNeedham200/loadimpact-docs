---
layout: classic-docs
title: Why is my response body empty?
description: Explanation on why your response body is empty during a test
categories: [frequently-asked-questions]
order: 9
redirect_from: /knowledgebase/articles/1166368-why-is-my-response-body-empty
---

***


By default, LoadImpact does not save any responses from the requests we send. Saving this information would consume extra resources on the load generator and generally are not needed when testing.

However, there are cases where you need to access the content response:
- [to verify the response content.]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/verifying-resource-contents/)
- [to look for a CSRF token or similar.]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/http-requests-with-csrf-viewstate-authentication-tokens/)
- [to log the body content.]({{ site.baseurl }}/3.0/user-scenarios-scripting-examples/printing-debug-messages/)
- other reasons as defined by you

In order to save the response, you will need to add 'response_body_bytes=' to your request. For example:
{% highlight lua linenos %}
res = http.request_batch({
{'GET',
 'http://www.example.com',
 response_body_bytes=1024000
} -- be sure to set the response body large enough for what you need
})

log.debug(res[1].body) -- The log window is limited to 1024 bytes!
{% endhighlight %}

Do note that the log window is limited to 1024 bytes and we recommend that you limit logging during a test. However, the full response is available if you needed to access it with a `string.match()` to find what you are looking for.
