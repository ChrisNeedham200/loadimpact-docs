---
layout: classic-docs
title: Simple Crawl of a Website
description: Example script on how to script Virtual users to "crawl" a website
categories: [user-scenario-scripting-examples]
order: 20
redirect_from: /knowledgebase/articles/265432-simple-crawl-of-website
---

***

It is possible to simulate loading a random next URL from the current pages. If done enough times in a row it would basically be a simple "spider" crawl of the site. This could perhaps be useful in conjunction with custom metrics to record the average load time of random pages. Here is an example which loads the LoadImpact front page and then picks a random link and loads that:
{% highlight lua linenos %}
-- Site base URL.
local base_url = "http://loadimpact.com"

-- List of common static URLs.
local static_urls = {
    "http://loadimpact.com/static/css/W.reset.css+text.css+960.css,Mcc.O9LhVpeYZL.css.pagespeed.cf.S2-NuOsLoj.css",
    "http://loadimpact.com/static/css/W.base.css.pagespeed.cf.Q7iimHh-dU.css",
    "http://loadimpact.com/static/css/qtip/W.jquery.qtip.min.css.pagespeed.cf.4ARCmIGz4I.css",
    "http://loadimpact.com/static/js/loadimpact.js.pagespeed.jm.5rQKx4ParQ.js",
    "http://loadimpact.com/static/js/loadimpact.ui.js.pagespeed.jm.Kirtw3CMNO.js",
    "http://loadimpact.com/static/images/xlogo_white.png.pagespeed.ic.u8jhfnNIVs.png"
}

-- Function to build batch of static URLs to request.
function build_static_request_batch()
    batch = {}
    for _, p in pairs(static_urls) do
        table.insert(batch, { "GET", p })
    end
    return batch
end

-- Function to retrieve random link from page body.
function random_link(page_body)
    -- Extract links matching <a href="..."...>
    local links = {}
    for link in string.gmatch(page_body, '<a[%s]+href=\"([^\"]-)\"') do
        links[link] = link -- Use table as a set to avoid duplicates
    end
    local count = 0
    local idx_to_link = {}
    for l in pairs(links) do
        count = count + 1
        idx_to_link[count] = l
    end

    -- Get random link and make absolute URL of it.
    local rnd_link = idx_to_link[math.random(count)]
    if string.sub(rnd_link, 1, 4) ~= "http" and string.sub(rnd_link, 1, 1) ~= "/" then
        rnd_link = "/" .. rnd_link
    end
    if string.sub(rnd_link, 1, 4) ~= "http" then
        rnd_link = base_url .. rnd_link
    end
    return rnd_link
end

-- Request first page.
http.page_start("Front page")
local response = http.request_batch({
    { "GET", "http://loadimpact.com/", response_body_bytes=32000 }
})
http.request_batch(build_static_request_batch())
http.page_end("Front page")

-- Some sleep/think time between page views, between 10 and 20 seconds.
client.sleep(math.random(10, 20))

-- Request next random page found on first page.
local rnd_link = random_link(response[1].body)

http.page_start("Random page")
response = http.request_batch({
    { "GET", rnd_link }
})
http.request_batch(build_static_request_batch())
http.page_end("Random page")
{% endhighlight %}


It is important to note that, depending on your testing goals and objectives, "crawling" a site for load testing is not often encouraged. Load tests often fail to give an accurate picture of a website's performance because user actions (user scenarios) are not simulated accurately. As such, if you're looking at discovering page load times from realistic user interactions with your site, we would recommend recording several sessions over crawling a website.
