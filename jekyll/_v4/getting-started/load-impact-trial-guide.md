---
layout: classic-docs
title: LoadImpact Trial Guide
description: Tutorial to utilize all the features within LoadImpact, both in the cloud and locally with it's companion, k6.
categories: [getting-started]
order: 0
redirect_from: /knowledgebase/articles/738684-load-testing-with-bluemix-and-load-impact
---

***

<h2>Purpose</h2>
The purpose of this document is to aid you in exploring the primary features available in LoadImpact as you evaluate our service, build your proof of concept, or just explore the world of load and performance testing.  If you complete the guide, you will start with the simpliest test that requires only inputting a URL and end with triggering tests from the command line.

LoadImpact is built around an open source load generator which also functions as a command line interface to communicate with our cloud platform. This load generator is named k6, it is built in Go and allows you to express your test cases with code written in JavaScript ES6. At the core, with everything as code, you gain full control over how your tests run, and what is happening during tests. **Every LoadImpact test has a JavaScript file that will control it's configuration and it's execution.**


- TOC
{:toc}

***

## Creating tests

There are multiple ways to create tests within our web application. Most users leverage the web app to build their proofs of concept, get a feel for scripting/testing, or to quickly put load on their target system with minimal effort. As the needs and processes for testing APIs and user journeys varies, we've split this section into API Testing and Website/Web App testing. We recommend starting with the "Request Builder" for testing APIs and "Recording a Browser Session" when testing user journeys. In both cases, we will generate base scripts for you.  You may wish to edit them further to add business logic.

To create a new test, click **Create New Test** in the left side bar. You will be presented with the following options for API or website testing:

![Figure 1]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/create-new-test-choices-v2.png)

## API Testing

The LoadImpact Request builder was designed to make it as easy as possible to create working test scripts to test your APIs.  You can simply add your endpoints, select the HTTP method, and specify POST bodies, headers, and more.  You can also verify responses with Checks, save variables from a response to reuse later and more.  Best of all, you can also view the generated script to understand how all these things are implemented into a k6 script.  Once you are ready to move to running the test from the command line, simply save the script locally and run it. For a more in depth guide, please refer to our guide on [Using the Request Buider]({{ site.baseurl }}/4.0/guides/request-builder/)

![Request Builder]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/advanced-test-1.png)

If you are feeling adventurous, you can also jump right into scripting for API testing within the in app IDE or locally in your IDE of choice.

## Website and Webapp testing

LoadImpact's tools for testing websites and webapps focus on helping you generate scripts quickly so you don't have to write them by hand. The majority of users will choose to do a browser recording to create their test script. However if your use case is simple or you want to get right to looking at the results we offer, entering website urls may make sense for you.

Browser recordings allow you to generate a script simply by browsing like a user would. We will capture all HTTP(s) requests made during your "session". You may need to edit the generated script if you've done more complex things, such as logins and form submissions to deal with hidden form fields, session tokens, and more.


### Enter Website URLs

The simplest way to run your first load test is to use our interface to enter website URLs. This method **will only** make GET requests. Practically speaking, it is a good way to quickly put some load on a system you want to test or emulate users refreshing a series of pages for the full test duration.

Behind the scenes, after you input URL(s) into the interface and click run we will first load those pages in a separate process, analyze what HTTP(s) requests are made to completely load the page and then generate a JavaScript file which will run on our load generators.  This all happens automatically and you will not need to edit any code.

**Important**: Reiterating a point above, if you are testing APIs you should **NOT** use this method.



Select **Enter Website URLs**, you will then proceed to this screen:

![Figure 2]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/enter-website-urls.png)


1. **Input** the target URL(s) for the pages you want to test.
2. **Set** the number of Virtual Users and duration of your test.
3. **Select** a ramping pattern you want the Virtual Users to follow.
4. **Pick** which Load Zones you want traffic to originate from.
5. **Whitelist** the domains you are testing so we ignore third party resources.
6. **Save and run** to execute your test and view results in real time.

**Note**: We recommend that you use the option to whitelist domains. This allows you to specify which domains you **want to be included** when we generate the underlying JavaScript file. Generally speaking, you should not test third party resources nor should you test sites you don't have permission to test. For example, trackers, analytic tools, advertising pixels, etc. Content Delivery Networks (CDN) are sometimes an exception to this note.  If you are specifically testing how well your CDN works for you or to compare CDNs, then those are good reasons to keep them included.

**Domain filtering example**: if you are testing test.loadimpact.com, you would use loadimpact.com as the domain whitelist.

_**Reasons you should not test third party resources**_
1. It may violate TOS you have with that resource.
2. If they throttle you or their requests perform poorly, it will skew you test data.
3. You typically won't have the ability to correct a performance issue with the resource.
4. Some third parties charge based on usage, thus it could increase your costs
5. External resources do not impact the performance of your System Under Test(SUT).


Next, take a look at your **test results**:

![Figure 3]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/test-result-overview.png)


- This test ramps to 50 Virtual Users over 1 minute, stays at 50 Virtual Users for 1 minute, ramps down to 0 Virtual Users over 1 minute.
- If Thresholds were configured, the number of passed `Thresholds / Total Thresholds`
  - **Failed Thresholds return non zero exit codes**
- If Checks were configured, the number of passing `Checks / Total Checks`
  - Checks **will not** fail a test, nor can they halt execution
  - Checks are often used with a Threshold and Custom Metrics
    - e.g. `"check_failure_rate": ["rate<0.3"]`
- The number of URLs with a status code `<400 / Total requests`

**Some Ideal performance result correlations**
- Flat response times with rising Virtual Users
- Increasing throughput/request rate with rising Virtual Users
- Low number of errors/error rate not increasing with rising Virtual users

Additional resources can be found in the [Results Analysis](#results-analysis) section at the bottom of this page.

***

## Creating tests from Browser Recordings

When testing web apps or websites, you often want to emulate real user behavior as they make their journey through your app or site.  LoadImpact has two methods that can be used to generate scripts from browser activity.  A Chrome Extension and a HAR file converter. Both allow you to record a journey you take in a local session and convert that to a JavaScript file to be used for your tests.

These methods can save an enormous amount of time, by letting you focus on fine tuning and adding programmtic logic to your test(if applicable!) rather than hand writing each request.

***

### Using the LoadImpact Chrome Extension to create a test

The LoadImpact [Chrome Extension](https://chrome.google.com/webstore/detail/load-impact-k6-test-scrip/docmmckkhiefiadappjepjllcoemijpj) allows you to very quickly generate a test by browsing like a user would on your web app or website.

Once you have downloaded the Chrome Extension, using it is extremely simple:

1. **In a new tab**, navigate to the page you wish to start on.
2. **From the extension menu**, click `Start Recording`.
3. **Refresh** the current page you are on.
4. **Browse** and follow a journey a user would take / that you want to test.
5. **Once Finished**, click `Stop Recording` in the extension menu.
6. **Automatically**, we will launch a new tab and begin to convert the recording into JavaScript.
7. **Select** a project and organization to save your test script and give it a meaningful name.
8. **Edit** your script as necessary. You can write real JavaScript code in our Web IDE to do programmatic things


**Common Questions**

_What programmatic things might I want to do with my script?_

- Forms, logins, and other means of submitting data are the most common. Submitting the same information (or using the same login) on every script iteration typically isn't realistic and in many cases will just test your systems caching capabilities. For proof of concept purposes, we recommend creating a JavaScript object to iterate over in the Web IDE. To use external sources, please refer to this [code sample on parameterization]({{ site.baseurl }}/4.0/examples/parameterizing-data).
- Logins and form submissions often contain hidden fields to prevent CSRF style attacks. The chrome extension would have captured the value from your session, which will likely be expired when you run your test. To solve this you must [correlate the token]({{ site.baseurl }}/4.0/examples/correlating-tokens-dynamic-data-load-test/) from the response body

_The script is long, do I really need all this detail?_

The Chrome Extension purposely records exact details of the requests including Headers and Cookies. You may need to access some data from either source, so knowing what is present can be helpful if you plan to add programmatic logic to your script. As we will handle cookies automatically, it's actually recommended that you remove them from your script (especially if session IDs are handled).  You may also want to remove third party requests for the same reasons mentioned earlier.

**Under the Hood Note**: The Chrome Extension is creating a HAR file and then using the same conversion operation that is available with `k6 convert` locally to generate a JavaScript test.

***

### Converting a HAR file to create a test

A second way to create realistic tests is to capture activity in your browser and save it as a HAR file. Here is a list of [tools that can output HAR files]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/#tools-that-can-output-har-files) from another document in this knowledge base. The HAR file converter is preferred when needing to record HTTP requests outside of Chrome.

To use the HAR file conversion method, click on **[Create New Test](https://app.loadimpact.com/k6/tests/new)**, select **[Browser Recording](https://app.loadimpact.com/k6/tests/browser-recording)**, then **[HAR File Upload](https://app.loadimpact.com/k6/tests/custom/config)**.

The HAR file conversion tool presents options similar to **Entering Website URLs** for you to specify your test configuration.

![Figure 4]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/har-file-upload.png)

1. **Upload** the your HAR file.
2. **Set** the number of Virtual Users and duration of your test.
3. **Select** a ramping pattern you want the Virtual Users to follow.
4. **Pick** which Load Zones you want traffic to originate from.
5. **Whitelist** the domains you are testing so we ignore third party resources.
6. **Save and run** to execute your test and view results in real time.

**Under the Hood Note:** k6 has a built-in HAR converter that will read HAR files and convert them to k6 test scripts that can then be executed. Refer to [this document]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-convert-har-to-k6-test/) on converting HAR files locally. The conversion done in app is the same process as `k6 convert`.

***

## Using the Web IDE to create a Test Script in Javascript

One of the powerful features of LoadImpact 4.0 is the ability to create test scripts directly in JavaScript. There’s an in-app editor (IDE) where you can create, edit, and run your test scripts right in the LoadImpact SaaS platform. This means you don’t need to download and install k6 to create or run your initial tests as you evaluate our platform.

To get started with scripting using the Web IDE, click on **[Create New Test](https://app.loadimpact.com/k6/tests/new)** and select **Scripting for APIs** or **Scripting** under the Website/App Testing heading. Both options are **pre-populated with example scripts** that you can edit as required.

![Figure 5]({{ site.baseurl }}/assets/img/v4/getting-started/load-impact-trial-guide/in-app-scripting.png)


Previously, we mentioned that at the heart of every test is a JavaScript file.  If you have followed this guide this may be your first look at a script.  Take note of the **Options** section, which defines the test configuration and the **Default Function** where the requests the Virtual Users will make are. As long a test is running, Virtual Users will iterate over the default function. For all intents and purposes the code you write in that function is the program controlling Virtual Users in your test.


***

## Using k6 locally with the LoadImpact Cloud

The LoadImpact web interface we have explored thus far has plenty of functionality to serve as a web based Load Testing tool and many users use it just for that purpose.  While the local execution mode isn't appropraite for everyone, many users enjoy the power they gain from it.


_What do I gain by using k6 locally compared to the Web IDE/Interface?_

While not an all inclusive list, there are a few things that become noticeably more flexible when working locally:

- You can modularize your test script and/or include existing external libraries for use in your test.
- When parameterizing data you can open files, such as a CSV or JSON to be your source for data.
- You can utilize your local version control systems with your scripts.
- You can utilize "local execution" mode to debug your test.
- You can run tests locally and stream results into LoadImpact Insights for analysis.
- k6, being a command line tool, easily integrates into CI/CD pipelines
- and more...

Aside from where the load generates from and the results output, the script itself remains unchanged between execution modes. This allows you to shift testing left to be available in the development cycle. The following section will cover the different execution options available.

**Note**: When running a cloud test from the command line, k6 will automatically archive all dependencies for your test and upload it to the LoadImpact cloud for execution.

***


### Downloading k6

k6 is available directly from our GitHub repository. You may also utilize the following install methods:

<div class="row platform-tabs">
    <div class="col-12">
        <ul class="nav nav-pills mb-3" role="tablist">
            <li class="nav-item">
                <a class="nav-link active" id="platform-tabs-link-linux" data-toggle="pill" href="#platform-tabs-content-linux" role="tab">Linux</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" id="platform-tabs-link-macos" data-toggle="pill" href="#platform-tabs-content-macos" role="tab">Mac</a>
            </li>
            <li class="nav-item">
                <a class="nav-link" id="platform-tabs-link-windows" data-toggle="pill" href="#platform-tabs-content-windows" role="tab">Windows</a>
            </li>
        </ul>
        <div class="tab-content">
            <div class="tab-pane fade show active" id="platform-tabs-content-linux" role="tabpanel" aria-labelledby="platform-tabs-link-linux">
                <p>
                    <h4>Debian/Ubuntu</h4>
                    <pre><code>$ sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 379CE192D401AB61
$ echo "deb https://dl.bintray.com/loadimpact/deb stable main" | sudo tee -a /etc/apt/sources.list
$ sudo apt-get update
$ sudo apt-get install k6</code></pre>
                </p>
                <p>
                    <h4>Redhat/CentOS</h4>
                    <pre><code>$ wget https://bintray.com/loadimpact/rpm/rpm -O bintray-loadimpact-rpm.repo
$ sudo mv bintray-loadimpact-rpm.repo /etc/yum.repos.d/
$ sudo yum install k6</code></pre>
                </p>
            </div>
            <div class="tab-pane fade" id="platform-tabs-content-macos" role="tabpanel" aria-labelledby="platform-tabs-link-macos">
                <pre><code>brew tap loadimpact/k6
brew install k6</code></pre>
            </div>
            <div class="tab-pane fade" id="platform-tabs-content-windows" role="tabpanel" aria-labelledby="platform-tabs-link-windows">
                <a href="https://dl.bintray.com/loadimpact/windows/k6-v0.25.0-amd64.msi">Download the latest k6 installer</a>
            </div>
        </div>
    </div>
</div>

Refer to [installation options](https://docs.k6.io/docs/installation) in the k6 docs for additional information

#### Utilizing k6 locally to trigger tests

Once you've installed k6, try these commands to try the different execution modes:

##### Local Execution with local output
This mode is useful for validations and smoke tests. Load generated from your local machine with output to stdout
{% highlight shell %}
k6 run -u 1 -d 10s github.com/loadimpact/k6/samples/http_get.js
{% endhighlight %}

##### Local Execution with cloud streaming output
This mode is useful for testing behind the firewall. Cloud output enables you to analyze your results within LoadImpact's cloud platform. In stdout, you are given a link to the test result to watch in real-time.
{% highlight shell %}
k6 login cloud
k6 run -u 25 -d 5m github.com/loadimpact/k6/samples/http_get.js -o cloud
{% endhighlight %}

##### Cloud Execution triggered from local command line

This mode allows you to scale to a large number of Virtual Users on demand, utilizing LoadImpact's cloud infrastructure. Your script and it's dependencies (if applicable) are uploading to our cloud service and then executed. In stdout, you are given a link to the test result to watch in real-time.
{% highlight shell %}
k6 login cloud
k6 cloud -u 25 -d 5m github.com/loadimpact/k6/samples/http_get.js
{% endhighlight %}

***

## Results analysis
Refer to the following resources for more information on result analysis:
- Take an [in-app tour](https://app.loadimpact.com/k6/anonymous/9b480b664bee46c3bf1a9d1ffb57328d)
- [LoadImpact insights]({{ site.baseurl }}/4.0/result-analysis/insights-overview/)
- [Thresholds]({{ site.baseurl }}{% link _v4/core-concepts/thresholds.md %})

***

## Next Steps/Automating testing

As a best practice, building automation into your development cycle for testing provides a multitude of benefits, such as:
- Deploying higher quality code, faster
- Improving efficiencies
- Detecting performance issues, before they become problems in production

LoadImpact 4.0 and k6 are designed to fit nicely into automation pipelines or custom processes you've already created. The most popular CI tools, such as Circle CI, GitLab, Jenkins, Team City, etc. support making command line calls. This allows k6 to fit nicely into these workflows and build processes. Enabling you to run tests as part of the build process, and halt those builds, should a failing result be returned.

The frequency in which you run tests varies from organization to organization. Our best practice recommendation is to _run tests with your nightly builds_.




***

## How can I get help?
The LoadImpact Support and Client Success teams are here to help should you have questions. You can utilize the icon in the lower right corner of this page to start a chat or email us at support [at] loadimpact.com.

***

## Additional resources

- [This Knowledge Base]({{ site.baseurl }}/4.0/)
- [k6 docs](http://docs.k6.io/docs)
- [k6 project page](https://k6.io/)
- [GitHub Repo](https://github.com/loadimpact/k6)
- [LoadImpact v4.0 Chrome Extension ]({{ site.baseurl }}/4.0/how-to-tutorials/load-impact-version-4-chrome-extension/)
- [How to create a HAR recording]({{ site.baseurl }}/4.0/how-to-tutorials/how-to-do-browser-recording/)
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTIwMjc5MzU0OTddfQ==
-->
