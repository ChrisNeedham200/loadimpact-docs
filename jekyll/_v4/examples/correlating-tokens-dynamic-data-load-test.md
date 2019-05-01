---
layout: classic-docs
title: Correlating Tokens and Dynamic Data
description: Code samples on how to correlate tokens and other dynamic data. These methods work for hidden fields such as CSRF, Nonce, VIEWSTATES, etc.
categories: [examples]
order: 3
redirect_from: /4.0/test-scripting/handling-dynamic-tokens-in-k6-scripts/
---

***

<h2>Background</h2>

Scripting examples on how to correlate dynamic data in your test script.  Correlation is often required when using the Chrome Extension or HAR converter to generate your test script.  This is due to the fact that those tools will capture session IDs, CSRF tokens, VIEWSTATE, wpnonce, and other dynamic values from your specific session.  These tokens typically expire very quickly.  This is one of the most common things that users will script for when testing user journeys across websites or web apps. 

***


## Correlation
In a load testing scenario, correlation means extracting one or more values from the response of one request and then reusing them in subsequent requests. Often times this could be getting a token or some sort of ID necessary to fulfill a sequence of steps in a user journey.

The [browser recording]({{ site.baseurl }}{% link _v4/guides/how-to-do-browser-recording.md %}) will for example capture things like CSRF tokens, VIEWSTATES, nonce, etc. from your session. This type of data is likely to no longer be valid when you run your test, meaning you'll need to handle the extraction of this data from the HTML/form to include it in subsequent requests. This issue is fairly common with any site that has forms and can be handled with a little bit of scripting.

### Extracting values/tokens from JSON response
{% include 4.0/scripting-examples/correlation-json-resp.md %}

**Relevant k6 APIs**:
- [Response.json()](https://docs.k6.io/docs/response-k6http)
- [JSON.parse()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/JSON/parse) (An alternative API that can be used for parsing JSON data)

### Extracting values/tokens from form fields
There are primarily two different ways you can choose from when deciding how to handle form submissions. Either you use the higher-level [Response.submitForm([params])](https://docs.k6.io/docs/responsesubmitform-params) API or you extract necessary hidden fields etc. and build a request yourself and then send it using the appropriate `http.*` family of APIs, like [http.post(url, [body], [params])](https://docs.k6.io/docs/post-url-body-params).

#### Extracting .NET ViewStates, CSRF tokens and other hidden input fields


**Method 1** using the k6 HTML parsing and query APIs:
{% include 4.0/scripting-examples/correlation-form-hidden-input1.md %}

Note:  Take note if `discardResponseBodies` is set to true in the options
section of your script. If it is, you can either make it `false` or save the response per
request with `{"responseType": "text"}` as shown in the example.

<!--**Method 2** using standard JS regex APIs:
{% include 4.0/scripting-examples/correlation-form-hidden-input2.md %}

Important Notes: The above example is a working one for the site specified.
You will need to determine which page the hidden form field is present on, save
the response, search the DOM for the correct element and assign that to a variable.
The final step is to concatenate that in future requests.

Update the above note as neccessary once code is updated.

-->
**Relevant k6 APIs**:
- [Selection.find(selector)](https://docs.k6.io/docs/responsesubmitform-params) (the [jQuery Selector API](http://api.jquery.com/category/selectors/) docs are also a good resource on what possible selector queryies can be made)
- [Selection.attr(name)](https://docs.k6.io/docs/selectionattrname)
