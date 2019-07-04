---
layout: classic-docs
title: How to use LoadImpact request builder
description: A guide on how to use and create load tests with LoadImpact request builder
categories: [guides]
order: 6
redirect_from: /4.0/guides/load-impact-request-builder/
---

***

Going from zero to hero in load testing without breaking a sweat.
In this guide we will go through the mechanics and parts of the LoadImpact request builder UI.

* [Basic usage](#basic-usage)
* [Advanced usage](#advanced-usage)
* [Bailing out of the UI](#bailing-out-of-the-ui)
* [Request headers](#request-headers)
* [Query parameters](#query-parameters)
* [Variables](#variables)
  * [Declaring variables](#declaring-variables)
  * [Using variables](#using-variables)
* [Sending a payload](#sending-a-payload)
* [Checks](#checks)
* [Importing HAR file](#importing-har-file)
* [Tips and tricks](#tips-and-tricks)
* [Troubleshooting](#troubleshooting)

<!-- If are not familiar with JavaScript but need to load test your API, single endpoint or website  -->

## Basic usage
Creating a single endpoint test is dead simple, all that is required is basically the `URL` you want to test.<br>
If the _run test_ button is enabled it means that the configuration is solid and can be run. Hit _run test_ and the test will start running.<br>

The following example is a simple test hitting one endpoint and making sure that the HTTP status code is _200 OK_.
![basic test]({{ site.baseurl }}/assets/img/v4/guides/request-builder/basic-test.png)
![basic test loading]({{ site.baseurl }}/assets/img/v4/guides/request-builder/basic-test-loading.png)
![basic test starting]({{ site.baseurl }}/assets/img/v4/guides/request-builder/basic-test-starting.png)
If you want to change the duration of the test and how much load it should generate press the _configure_ button and a config screen will appear.
![load test config]({{ site.baseurl }}/assets/img/v4/guides/request-builder/load-config.png)
In this screen you can change the duration of the test, how many VUs (virtual users or "traffic" "load") you want, the ramping profile (how the traffic is ramped up and down) and from what geographical regions the load should be generated from.
If you are not familiar with these settings here are a couple of links for you.
* [What are VUs?]({{ site.baseurl }}/4.0/core-concepts/what-are-virtual-users/#what-are-vus-virtual-users)
* [Ramping configurations]({{ site.baseurl }}/4.0/core-concepts/types-of-load-performance-tests)
* [Available load zones]({{ site.baseurl }}/4.0/guides/cloud-execution/#load-zones)

-----------

## Advanced usage
Creating a test with payloads, headers, authentication and asserts might sound difficult but with the request builder UI it is actually pretty straight forward.<br>
In the following example we will authenticate, declare [variables](#variables), set [headers](#headers), [send JSON data](#sending-a-payload) to create and update resources, delete resources and make [checks](#checks) to validate HTTP response codes and verifu response data values.

Add a JSON payload to send with the request
![add payload]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-1.png)

Set request headers
![set headers]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-2.png)

Declare a variable `userToken` that can be used for authorized consecutive requests
![declare variable]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-3.png)

Create checks to verify that
1. HTTP status code is 200
2. The property token is present in the data returned from the server
![create checks]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-4.png)

Add a new post request to create a resource
![create a new record]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-5.png)

Use the previously declared variable in a request header
![variable in header]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-6.png)

Check that the HTTP status code is _201 created_
![check status code]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-7.png)

Declare a new variable `crocodileId`
![declare a new variable]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-8.png)

Create a new PATCH request to update the resource we just created. Note that the variable `crocodileId` is being used as a part of the URL
![new patch request]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-9.png)

Again, add the auth header
![auth header]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-10.png)

Create a new GET request to get the information of the resource we just updated. Note that the variable `crocodileId` is being used as a part of the URL
![new get request]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-11.png)

Add checks to verify that the data was actually updated
![verify data]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-12.png)

Create a new DELETE request to delete the resource we created
![delete request]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-13.png)

Check the status code to see that it is _204 No Content_
![status code check]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-14.png)

This is what the final result looks like. You can switch over to code mode and preview the Javascript generated from your config, you switch mode by clicking the `</>` symbol in the top right corner.<br>
If you want to do even more advanced things you can create a test based on your configuration too! Check out the [Bailing out of the UI](#bailing-out-of-the-ui) section if that sounds interesting.
![final result]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-15.png)

Hit __run test__ to run the test with default load settings or __configure__ to change settings like _duration_, [VUs (virtual users)]({{ site.baseurl }}/4.0/core-concepts/what-are-virtual-users/#what-are-vus-virtual-users), load [ramping profile]({{ site.baseurl }}/4.0/core-concepts/types-of-load-performance-tests) or geographical [load zones]({{ site.baseurl }}/4.0/guides/cloud-execution/#load-zones).
![load config]({{ site.baseurl }}/assets/img/v4/guides/request-builder/advanced-test-16.png)

Hit save and run and the test will start running.

-----------

## Bailing out of the UI
The request builder UI is pretty flexible and can be configured in many different ways. Would you reach a point where you cannot configure the test in the way you want you can always drop down into JavaScript and modify the code directly.
By switching over to the _script preview mode_ you can press the "create test from script" button, this will create a new test based on your configuration.
![javascript]({{ site.baseurl }}/assets/img/v4/guides/request-builder/script-preview.png)

-----------

## Request headers
Add information about the client or the resource being fetched. Set a Authorization header with access token to make authorized requests to your API.
![using headers]({{ site.baseurl }}/assets/img/v4/guides/request-builder/using-headers.png)

----------

## Query parameters
Query parameters can either be added in this panel or typed directly in the URL.
![query parameter]({{ site.baseurl }}/assets/img/v4/guides/request-builder/query-params.png)

----------

## Variables
In some situations it is useful to use the data was returned in one of the previous request, here is where variable come in handy.

### Declaring variables
Variables require two things a _name_ of which the variable will be available as in consecutive requests and an _expression_ to know what value to assign the variable.

There are two different expression types you can use when defining variables
* [JSONPath](#jsonpath)
* [Regex](#regex)

#### JSONpath
Specifying a "JSON path" of which the value you wish to assign the variable is.
The JSONPath expression always starts with a `$.` followed by the actual path to the value.

Here is an example:

__Data returned from server__
{% highlight js linenos %}
{
  user: {
    name: "Batman",
    token: "xyz123"
  }
}
{% endhighlight %}

__Corresponding JSON paths for name and token__

![json path example]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-definition-jsonpath.png)

You can read more about JSONPath syntax [here](https://github.com/dchester/jsonpath#jsonpath-syntax).


#### Regex
Specifying a regular expression. This can be useful if the data returned is not JSON but for example plain text.
If you are not familiary with regular expressions here are some useful links:
* [Regex validator](https://regex101.com)
* [Regex Cheat Sheet](https://www.rexegg.com/regex-quickstart.html)


Here is an example using regex:

__Data returned from server__
{% highlight html linenos %}
Hello this is a message of somekind
phone_number:000000000
sender:Bruce Wayne
{% endhighlight %}

__Regex to extract the number__
![regex example]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-definition-regex.png)

Notice that there are "(..)" parentheses around `[0-9]{10}`, this is how you specify what part of the match _(if any)_ you want to assign to the variable.<br>
In the case of multiple matches the first match will assigned to the variable.

<!-- _NOTE: Notice that the "/" slashes are omited._ -->


### Using variables
Variables can be used in many different ways, here is a complete list of how you can use variables.
<!-- Places allowing for the use of variables are [headers](headers), [request payload](sending-a-payload-to-your-endpoint), [query parameters](query-parameters) and in the [url](url). -->


* __Headers__ - this is probably the most common usecase. Imagine that you have a couple of protected endpoints which require authorization.
![using variable in headers]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-headers.png)

* __URL__ - using one of the declared variables as a part of the request URL.<br>
This can be useful if you want to perform an action on a specific resource.
<!-- `[PATCH] https://test-api.loadimpact.com/my/crocodiles/${crocodileId}/` -->
![using variable in url]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-in-url.png)

* __Query parameter__ - query parameters are a part of the URL so this is very similar to usage previous example.<br>
Using variables as query parameter can be accomplished in two ways, either add the query parameter directly in the URL input or in the query parameter panel.
![using variable in url]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-query-param.png)


* __Request payload__ - using a variables value as a part of a request payload.
![variable request payload]({{ site.baseurl }}/assets/img/v4/guides/request-builder/variable-request-payload.png)

----------

## Sending a payload
In some cases you might want to make a request to create or update a resource, you can easily create a configuration for this.
Select a HTTP method that supports a request payload then select what type of data you want to send.
There are three different choices _JSON_, _text_ and _file content_.

* __JSON__ - send a JSON data structure to your api.
![payload json]({{ site.baseurl }}/assets/img/v4/guides/request-builder/payload-json.png)

* __Plain text__ - send plain text payload.
![payload plain text]({{ site.baseurl }}/assets/img/v4/guides/request-builder/payload-text-plain.png)

* __File content__ - upload a file and send a _base64encoded_ payload.
![payload file content]({{ site.baseurl }}/assets/img/v4/guides/request-builder/payload-file-data.png)

----------

## Checks
Checks are like asserts, pass or fail conditions.
Checks are great for codifying assertions relating to HTTP requests/responses, making sure the response code is 2xx for example.

There multiple type of checks you can add and each of them have their place.<br>
These are the different types of checks available:

__HTTP status code__ - this is more of a shorthand for the check type _text_.
You can easily test that a status code is 2xx, 5xx and 4xx.. etc.
![check http status code]({{ site.baseurl }}/assets/img/v4/guides/request-builder/checks-http.png)

__JSONPath value__ - check againt properties in a JSON data structure.<br>
If you are not familiar with _JSONPath_ take a look at the earlier section about [JSONpath](#jsonpath).

Here is an example where we are are checking the value of the users name and email.

{% highlight js linenos %}
// Data returned from server
{
  user: {
    name: "Batman",
    email: "batman@wayneenterprises.corp"
  }
}
{% endhighlight %}

Corresponding JSONPath expressions.
![jsonpath value expression]({{ site.baseurl }}/assets/img/v4/guides/request-builder/checks-jsonpathvalue.png)

__JSONPath assert__ - check for the existence of a specific property in a JSON data structure.<br>
If you are not familiar with _JSONPath_ take a look at the earlier section about [JSONpath](#jsonpath).

Here is an example where we are are checking that the data returned from the server contains the properties _token_ and _secret_.

{% highlight js linenos %}
// Data returned from server
{
  token: "xyz123",
  secret: "supersecret"
}
{% endhighlight %}

Corresponding JSONPath expressions.
![jsonpath check]({{ site.baseurl }}/assets/img/v4/guides/request-builder/checks-jsonpath.png)

__Regex__ - just like _text_ check this also lets you select a subject to check against.<br>
The subjects available are _response body_, _response headers_ and _HTTP status code_.

In the following example we are checking that:
1. _response body_ contains a string `timestamp:` followed by a timestamp e.g `02-22-2019`.
2. HTTP status code is 2xx.
3. there was a header in the response with `uid=` followd by four digits e.g `1234`.

![regex check]({{ site.baseurl }}/assets/img/v4/guides/request-builder/checks-regex.png)

----------

## Importing HAR file
To quickly populate the request builder UI you can import a [_HAR file_](http://www.softwareishard.com/blog/har-12-spec).
![import har file]({{ site.baseurl }}/assets/img/v4/guides/request-builder/import-config.png)

-----------

## Tips and ticks

__Change order of requests__<br>
Hover the request and a ![varr]({{ site.baseurl }}/assets/img/v4/guides/request-builder/varr.png) will appear, click and drag this symbol to change order of your requests<br>
![drag and drop]({{ site.baseurl }}/assets/img/v4/guides/request-builder/drag-and-drop.png)

__Remove and duplicate requests__<br>
Click the ![vellip]({{ site.baseurl }}/assets/img/v4/guides/request-builder/vellip.png) symbol to reveal a menu with options to duplicate and remove the request<br>
![drag and drop]({{ site.baseurl }}/assets/img/v4/guides/request-builder/duplicate-or-delete.png)

-----------

## Troubleshooting
__The _configure_ and _run test_ buttons are disabled!?__<br>
Whenever there is a conflicting configuration or someting is missing in order for the test to be run a red light will show up.
Hover or click the red light and you will see the error that needs to be fixed in order for the test to be run.<br>
![validation error]({{ site.baseurl }}/assets/img/v4/guides/request-builder/validation-error.png)

Here is another example of a error message where we are trying to use a variable without it being declared.<br>
![validation error variable]({{ site.baseurl }}/assets/img/v4/guides/request-builder/validation-error-variable.png)

<!--stackedit_data:
eyJoaXN0b3J5IjpbNDIwNTEyODY5XX0=
-->