---
layout: classic-docs
title: Will load tests show up in Google Analytics?
description: Explanation on why load tests do not show up in Google Analytics and a code sample on how to make them show up, if desired.
categories: [frequently-asked-questions]
order: 6
redirect_from: /knowledgebase/articles/174526-will-load-testing-transactions-show-up-on-google-a
---

***


Our load testing transactions will not show up on Google Analytics. Usually, a web page refers to the Google Analytics javascript “ga.js”, which is loaded as part of the loading of a web page. The script is loaded, then executed by the browser, and then the script tells the browser to make a request to Google, telling Google about the page access so they can store the statistics in the GA database there.

In the case of LoadImpact, there are two things preventing Google Analytics from seeing our transactions:

(1) LoadImpact does not execute JavaScript on the client side. This means that even if we load ga.js from Google it won’t be executed, which means Google will not get any statistics from the simulated clients in our load test.

(2) If you use LoadImpact to make a recording or just enter a URL for a page you want to test, we will by default filter out any requests to external sites from the resulting load script. I.e. If your site is test.loadimpact.com, any requests to google.com (either the request to fetch ga.js, or the AJAX request made by ga.js) will be removed from the load script.

If you want your transactions to show up on Google Analytics**(NOT RECOMMENDED)**, you will have to:

(1) Do a proxy recording (using our Session Recorder) with a real browser when creating your user scenario. This will allow you to execute javascript on the client side by capturing the request made by ga.js to Google that sends statistics to Google. This is the only way to create the request that generates the GA statistics. You will then need to make sure that the requests are properly made with the dynamic data in your script.

(2) Make sure dynamic data in the requests to Google Analytics get emulated. ga.js will request __utm.gif with a bunch of request parameters and a couple of them have to be unique for each client or transaction. See https://developers.google.com/analytics/resour ces/concepts/gaConceptsTrackingOverview for more details.

 We provide a sample which you are free to modify and use:

 {% highlight lua linenos %}
-- The only things you need to set is Google-Analytics ID (ga_id) and then make a ga_request() to get them into Google-Analytics
-- GOOGLE ANALYTICS STUFF START.
-- Return a random number
function rnd()
  return math.random(1004138391, 9904138391)
end
-- Generate UUID v4 function - needed to generate a CID when one isn't available
function ga_gen_uuid()
  return string.format( '%04x%04x-%04x-%04x-%04x-%04x%04x%04x',
    -- 32 bits for "time_low"
    math.random( 0, 0xffff ), math.random( 0, 0xffff ),
    -- 16 bits for "time_mid"
    math.random( 0, 0xffff ),
    -- 16 bits for "time_hi_and_version",
    -- four most significant bits holds version number 4
    math.random( 0, 0x0fff ) , --| 0x4000,
    -- 16 bits, 8 bits for "clk_seq_hi_res",
    -- 8 bits for "clk_seq_low",
    -- two most significant bits holds zero and one for variant DCE1.1
    math.random( 0, 0x3fff ), -- | 0x8000,
    -- 48 bits for "node"
    math.random( 0, 0xffff ), math.random( 0, 0xffff ), math.random( 0, 0xffff )
  )
end
-- return encoded URL
function url_escape(str)
    return string.gsub(str, "([^A-Za-z0-9_%.~%-])", function(c)
        return string.format("%%%02x", string.byte(c))
    end)
end
-- Return a random User Agent
function get_random_ua()
    local uas = {
    	"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
    	"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:24.0) Gecko/20100101 Firefox/24.0",
    	"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9_0) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
    	"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_9) AppleWebKit/537.71 (KHTML, like Gecko) Version/7.0 Safari/537.71",
    	"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
    	"Mozilla/5.0 (Windows NT 6.1; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.69 Safari/537.36",
    	"Mozilla/5.0 (Windows NT 6.2; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
       	"Mozilla/5.0 (Windows NT 6.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
       	"Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_5) AppleWebKit/536.30.1 (KHTML, like Gecko) Version/6.0.5 Safari/536.30.1",
        "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_8_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.69 Safari/537.36",
       	"Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.1; WOW64; Trident/6.0)",
    	"Mozilla/5.0 (Macintosh; Intel Mac OS X 10.8; rv:24.0) Gecko/20100101 Firefox/24.0",
    	"Mozilla/5.0 (Windows NT 6.1; WOW64; rv:25.0) Gecko/20100101 Firefox/25.0",
    	"Mozilla/5.0 (Windows NT 6.1; rv:24.0) Gecko/20100101 Firefox/24.0",
    	"Mozilla/5.0 (Windows NT 5.1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/30.0.1599.101 Safari/537.36",
    	"Mozilla/5.0 (X11; Linux x86_64; rv:12.0) Gecko/20100101 Firefox/21.0",
  	}
	return uas[math.random(#uas)]
end
-- Make a Google-Analytics request
function ga_request(url, referer, cid)
  http.request_batch({
      {
        "GET",
        "http://www.google-analytics.com/collect?v=1&_v=j14&a=" .. rnd() .."&t=pageview&_s=1&dl=".. url_escape(url) .."&ul=en-us&de=UTF-8&sd=24-bit&sr=1440x900&vp=1440x736&je=1&_u=MAC~&cid="..cid.."&tid=" .. ga_id .. "&z=" .. rnd() .."",
        headers = {
       		["Referer"] = referer,
          	["User-Agent"] = ua,
          	["Accept-Encoding"] = "gzip, deflate",
          	["Accept"] = "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
          	["Pragma"] = "no-cache",
          	["Cache-Control"] = "no-cache"
        },
        report_results = false,
      },
  })
end

-- Set Client ID
local cid = ga_gen_uuid()
-- Set User Agent
local ua = get_random_ua()

-- Set Goggle-Analytics ID
local ga_id = "UA-1234567-8"
-- GOOGLE ANALYTICS STUFF END

http.page_start("page 1")
-- Get ga javascript
http.request_batch({
    {"GET", "http://www.mydomain.com/path"},
    {"GET", "http://www.google-analytics.com/analytics.js"},
})
-- Set URL, Referrer and Client ID
ga_request("http://www.mydomain.com/path", "", cid)
http.page_end("page 1")
http.page_start("page 2")
http.request_batch({
    {"GET", "http://www.mydomain.com/path/2"},
})
-- Set URL, Referrer and Client ID
ga_request("http://www.mydomain.com/path/2", "http://www.mydomain.com/path", cid)
http.page_end("page 2")
{% endhighlight %}
