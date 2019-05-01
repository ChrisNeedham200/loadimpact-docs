---
layout: classic-docs
title: Testing a site with file uploads
description: One example on how to script your User Scenario to upload files during a test.
categories: [user-scenario-scripting-examples]
order: 21
redirect_from: /knowledgebase/articles/814794-testing-a-system-that-requires-uploading-a-file
---

***

In LoadImpact's Lua scripted product(version 3), there is no way to upload a file and make it available in a user scenario for testing file uploads. However, there are a few methods for testing systems where file uploads is a requirement.

**NOTE**: k6 supports file uploads, so LoadImpact suggests using that for testing this type of scenario. The following article and code sample is provided for posterity and gives options when using the Lua based product.

If you need to send the right data (the real file contents) you have to somehow get the file to the load generator. There are some ways to do this:

1. If the file is very small you can base64-encode it and then copy and paste it into your user scenario, assigning it to a local variable that you then use to construct the POST request. When doing the POST you have to use a special parameter to tell our system that it needs to decode the base64-encoded body before sending it to the target site.
2. If the file is larger, base64-encode it and upload it as a data store, then assign that data store to your user scenario and you will be able to access the data from your user scenario.



If you can send random data, that is the easiest thing to do. It means you can just generate the right amount of random data in your user scenario and then send that as the body of the POST request that is doing the file upload.


We have the following example code that will help you with scripting your scenarios to allow for this:
{% highlight lua linenos %}
-- LOADIMPACT script example for file upload

local boundary = "dV4Dzl7FuKKkC94lMJgmmw"
local postData = ""
local ds = datastore.open("random-string-datastore")
local noOfRows = 26 -- datastore:get_length() -- hardcoded to work in sandbox


-- generate a random string for a given length
randomString = function (length)
  local rndString = ""
  local index = 1
  while(string.len(rndString) < (length * 1000000)) do
    if(index > noOfRows) then
      index = 1
    end
    row = ds:get(index)
    index = index + 1
    rndString = rndString..row[1]
  end
  return rndString
end

  local uid = util.unique()
-- for writing json data to post
writeJSONContent = function ()
  local jsonData =  '{\"description\":\"Audio Upload\", \"uid\":\"'..uid..'\"}'
  postData = postData..'--'..boundary..'\r\n'
  postData = postData..'Content-Type:application/vnd.playground.audio+json; version=1.0; charset=UTF-8\r\n'
  postData = postData..'\r\n'
  postData = postData..jsonData..'\r\n'
end

-- for writing random string to post request
writeFileContent = function (contentType, size)
    fileData = randomString(size)
    postData = postData.."--"..boundary..'\r\n'
    postData = postData..'Content-Type: '..contentType..'\r\n'
    postData = postData..'Content-Transfer-Encoding: binary'..'\r\n'
    postData = postData..'Content-Length: '..string.len(fileData).."\r\n"
    postData = postData.."\r\n"
    postData = postData..fileData.."\r\n"
end

-- generate unique profile id
--
local id = util.unique()
local service = "Facebook"
local post_data = "{\"service\":\""..service.."\",\"personID\":\""..id.."\"}"

-- request to create a profile
local response = http.post({
  url = "https://api.loadimpactsample.com/profiles",
  data = post_data,
  headers= {
      ["Accept"]="application/vnd.playground.profile+json; version=1.0; charset=UTF-8",
      ["Authorization"]="x-token oFpXeHV5E-WTswHQqC0rPQ",
      ["Content-Type"]="application/vnd.playground.profile-query+json; version=1.0; charset=UTF-8"
    },
  response_body_bytes = 10000
})

log.info(response.body)
local profileID = json.parse(response.body).profileID

--time for es to refresh
client.sleep(1)

-- writing post body
writeJSONContent()
writeFileContent("image/png", 1) -- size in MB
writeFileContent("audio/ogg", 2) -- size in MB
postData = postData.."--"..boundary.."--"


--video upload request
local response = http.post({
    url = "https://api.loadimpactsample.com/profiles/"..profileID,
    data = postData,
    auto_redirect = false,
    headers = {
      ["Accept"] = "application/vnd.playground.audio+json; version=1.0; charset=UTF-8",
        ["Authorization"] = "x-token oFpXeHV5E-WTswHQqC0rPQ",
        ["Content-Type"] = "multipart/mixed; boundary="..boundary,
        ["Transfer-Encoding"] = "chunked"
    },
    response_body_bytes = 100000
})

if(response.status_code == 504) then
  log.info(uid)
end
log.info(response.status_code)
{% endhighlight %}
