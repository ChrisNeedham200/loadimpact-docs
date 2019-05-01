---
layout: classic-docs
title: HTTP Authentication and Authorization
description: Code samples of different HTTP Authentication and Authorization methods to be used in your load test
categories: [examples]
order: 1
redirect_from: /4.0/test-scripting/http-authentication/
---

***

<h2>Background</h2>

Scripting examples on how to use different authentication or authorization methods in your load test. 


***

## Authentication/Authorization
Examples of various HTTP Authentication methods that can be used with k6. These, plus other examples can be found within the [k6 GitHub Repo](https://github.com/loadimpact/k6/tree/master/samples/auth)

### Basic authentication
{% include 4.0/scripting-examples/http-auth-basic.md %}

### Digest authentication
{% include 4.0/scripting-examples/http-auth-digest.md %}

### NTLM authentication
{% include 4.0/scripting-examples/http-auth-ntlm.md %}

### AWS Signature v4 authentication
Requests to the AWS APIs requires a special type of auth, called AWS Signature Version 4. k6 doesn't support this authentication mechanism out of the box, so we'll have to resort to using a Node.js library called [awsv4.js](https://github.com/mhart/aws4) and [Browserify](http://browserify.org/) (to make it work in k6).

There are a few of steps to make this work:

1. Make sure you have the necessary prerequisites installed:
    * [Node.js](https://nodejs.org/en/download/)
    * [Browserify](http://browserify.org/)
2. Install the `awsv4.js` library:

    <kbd>npm install aws4</kbd>
3. Run it through browserify:

    <kbd>browserify node_modules/aws4/aws4.js -s aws4 > aws4.js</kbd>
4. Move the `aws4.js` file to the same folder as your script file and you'll be able to import it into your test script:

    `import aws4 from "./aws4.js"`

Here's an example script to list all the regions available in EC2. Note that the AWS access key and secret key needs to be provided through [environment variables]({{ site.baseurl}}{% link _v4/core-concepts/environment-variables.md %}).

<div class="callout callout-warning" role="alert">
    <b>Note that using this browserified Node.js library will be CPU and memory heavy</b><br>
    As the browserified version of this Node.js library includes several Node.js APIs implemented in pure JS (including crypto APIs) it will be quite heavy on CPU and memory hungry when run with more than just a few VUs.
</div>

{% include 4.0/scripting-examples/http-auth-aws4.md %}
