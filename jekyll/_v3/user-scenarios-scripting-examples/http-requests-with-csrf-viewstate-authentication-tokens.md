---
layout: classic-docs
title: Testing HTTP requests with Authentication tokens
description: In this article we provide a method to test a site with an CSRF token. The same principle applies for VIEWSTATE or other authentication tokens.
categories: [user-scenario-scripting-examples]
order: 17
redirect_from:
  - /knowledgebase/articles/174607-testing-a-site-with-csrf-token-or-viewstate
  - /knowledgebase/articles/389413-advanced-login-w-dynamic-data-extraction-correlati
---

***

<h2>Background</h2>



If your site is using some kind of CSRF token and you do a recording using our session recorder, the token recorded will most likely not be valid for simulated users in the load test. The same is true for ASP.NET sites using a VIEWSTATE.  Other names for this type of token are form_key in Magento, wpnonce in woocommerce, and more.

To fix this, you will need to do a little bit of scripting. The first thing you need to do is to save the body data when requesting the page with the form. By default Load Impact will not save any of the data from the requests, so you will need to specify the number of bytes you want to save. Once you have the body of the response, you can start look for the token. This is easiest to do with simple string matching. If you find the token, you can use it as one of the parameters in the following request, usually a POST.

#### Example code
This is a theoretical example. You will need to identify the page where the token is created and adjust the `string.match()` criteria.
{% highlight lua linenos %}
-- Make sure you save enough bytes of the reply to include the form, in this case 100000
local pages = http.request_batch({
    {"GET", "http://mydomain.com/myform.html", response_body_bytes=100000}
})

-- Get the body of the first page
local body = pages[1]['body']

-- Find the actual value of the token
-- We are looking for the string between the single quotes below
-- The value where (.-) is will be captured to our variable, "token"
local token = string.match(body, 'input id="token" value="(.-)"')

-- Make the POST request if a the token was found
if token ~= nil then
    -- Use the token value in the following POST
    http.request_batch({
        {"POST", "http://mydomain.com/post", headers={
                ["content-type"]="application/x-www-form-urlencoded"
            },
            data="token=" .. token .. "&foo=bar"
        }
    })
else
    log.error("Failed to find token")
end
{% endhighlight %}

*Consider:* The following characters `( ) . % + - * ? [ ^ $` are `Lua magic characters` and need to be escaped with a `%` if a part of your string being matched.  e.g. `csrf-token` would need to be escaped as `csrf%-token`.  Refer to this [document](https://www.lua.org/pil/20.2.html) for more information


Note: You will probably want to check the token variable before using it, or you will risk Lua errors in case the page no longer returns the expected content.
