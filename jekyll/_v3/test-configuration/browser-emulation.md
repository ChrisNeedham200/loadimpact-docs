---
layout: classic-docs
title: Browser Emulation
description: Using browser emulation you are able to change the user agent, max connections and max connections per host that the Virtual Users use during a test. This allows you to emulate specific user behavior for your use case.
categories: [test-configuration]
order: 10
redirect_from: /knowledgebase/articles/1113808-browser-emulation
---

***

LoadImpact has the ability to emulate different browsers. This can be changed directly per user scenario using **http.set_user_agent_string()** and **http.set_max_connections()** from our [Load Script API](https://loadimpact.com/load-script-api). Alternatively, you can change the emulation for the entire test using the preconfigured options within Test Configuration:



## Desktop Browsers:


LoadImpact

```
        Max connections: 30
        Max connections per host: 4
        User agent: LoadImpactRload/3.2.0 (LoadImpact; http://loadimpact.com);
```



Internet Explorer
```
        Max connections: 17
        Max connections per host: 13
        User agent: "Mozilla/5.0 (Windows NT 6.3; Trident/7.0; rv:11.0) like Gecko"
```


Firefox
```
        Max connections: 17
        Max connections per host: 6
        User agent:"Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:25.0) Gecko/20100101 Firefox/25.0"
```


Chrome
```
        Max connections: 10
        Max connections per host: 6
        User agent: "Mozilla/5.0 (Windows NT 6.2; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/32.0.1667.0 Safari/537.36"
```


Safari
```
        Max connections: 17
        Max connections per host: 6
        User agent: "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_6_8) AppleWebKit/537.13+ (KHTML, like Gecko) Version/5.1.7 Safari/534.57.2"
```
LoadImpact Max(for API testing)
```
        Max connections: 30
        Max connections per host: 30
```


## Mobile Browsers:


Android
```
        Max connections: 10
        Max connections per host: 6
        User agent: "Mozilla/5.0 (Linux; Android 4.1.2; GT-I9300 Build/JZO54K) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166 Mobile Safari/535.19"
```


Android Tablet
```
        Max connections: 10
        Max connections per host: 6
        User agent: "Mozilla/5.0 (Linux; Android 4.1.1; Nexus 7 Build/JRO03D) AppleWebKit/535.19 (KHTML, like Gecko) Chrome/18.0.1025.166 Safari/535.19"
```


iPad
```
        Max connections: 24
        Max connections per host: 6
        User agent: "Mozilla/5.0 (iPad; CPU OS 7_0_2 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A501 Safari/9537.53"
```


iPhone
```
        Max connections: 23
        Max connections per host: 6
        User agent: "Mozilla/5.0 (iPhone; CPU iPhone OS 7_0_2 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A4449d Safari/9537.53"
```


BlackBerry
```
        Max connections: 9
        Max connections per host: 7
        User agent: "Mozilla/5.0 (BlackBerry; U; BlackBerry 9900; en) AppleWebKit/534.11+ (KHTML, like Gecko) Version/7.1.0.346 Mobile Safari/534.11+”
```


IE Mobile:
```
        Max connections: 60
        Max connections per host: 6
        User agent: "Mozilla/5.0 (compatible; MSIE 9.0; Windows Phone OS 7.5; Trident/5.0; IEMobile/9.0”
```
