---
layout: classic-docs
title: Testing a SOAP API
description: Basic example on load testing a SOAP API endpoint and handling the response.
categories: [user-scenario-scripting-examples]
order: 22
redirect_from: /knowledgebase/articles/828378-testing-a-soap-api
---

***

This is a basic SOAP example which uses the http and xml load script APIs to test a SOAP server. Since testing API endpoints typically has slightly different requirements than testing an app or website. We also suggest our article on [How to load test an API]({{ site.baseurl }}/3.0/how-to-tutorials/how-to-load-test-an-api/)


{% highlight lua linenos %}
local headers = {
  ["Content-Type"]="text/xml; charset=UTF-8",
  ["SOAPAction"] = "http://tempuri.org/Service/Download"
}
local data = [[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:tem="http://tempuri.org/">
   <soapenv:Header/>
   <soapenv:Body>
      <tem:Download>
         <tem:IsLast>false</tem:IsLast>
      </tem:Download>
   </soapenv:Body>
</soapenv:Envelope>]]

local response = http.request({"POST",
    "http://api.com/Service",
    headers=headers,
    data=data,
    response_body_bytes=1000000
})

if response.status_code ~= 200 then
  log.error('login API error')
  do return end
end


local doc = xml.parse(response.body)
-- same content than
--local doc = xml.parse("<?xml version=\"1.0\"><books><book><author>LoadImpact</author><title>Load testing with XML</title></book></books></xml>")


-- Print title of first matched book
local node = doc:xpath_query("//books/book/title", { xml.XPATH_QUERY_SINGLE_MATCH })
if node ~= nil then
  log.info(node.content)
else
  log.error("no found book title")
end
{% endhighlight %}
