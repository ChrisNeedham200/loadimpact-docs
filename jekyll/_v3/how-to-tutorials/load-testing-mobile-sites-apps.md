---
layout: classic-docs
title: Testing Mobile Sites/Apps
description: Guide on testing mobile sites/apps
categories: [how-to-tutorials]
order: 5
redirect_from: /knowledgebase/articles/363175-how-to-load-test-a-mobile-site-or-app
---

***


Load testing a mobile application is not very different from load testing a desktop web application or site.

What you need to do is figure out how to generate a realistic traffic pattern, that simulates real user behavior as closely as possible, and also decide on what load levels to subject your system to in the load test. All this is then specified in your user scenario(s) and your test configuration.

Creating a mobile user scenario from a session recording

Recording usage sessions is almost as simple as with a desktop application. You use the LoadImpact proxy recorder and set your mobile device to use that proxy when fetching HTTP(s) content from the Internet. This is how you do it:

1. Connect your mobile device to Internet through a WiFi or cable connection. This is necessary because you are usually not able to set an HTTP proxy for the device when it is connected over a mobile network

2. Login at loadimpact.com with a desktop computer, Go to User Scenarios and click New User Scenario.

3. Click the "Choose" button under Proxy recorder. You will now get a new dialog box showing you which proxy settings to use. The proxy server name is "proxy.loadimpact.com" and the proxy port number varies but is always between 20,000 and 30,000.



4. Configure your desktop browser to use the proxy server. Here is some more info on how to do this on both Windows and MacOS X.


5. Configure the same proxy settings on your mobile device. Here's how it looks on Android:

   a. Open the Settings screen
   b. Find the WiFi network you're connected to, press and hold your finger on the entry until you get a dialog box where you can choose to "Modify network"

   c. Choose "Modify network", scroll down and tap the "Show advanced options" checkbox. This gives you even more options if you scroll down further.

  d. Scroll down to "Proxy settings" and tap it, then select "Manual"
  e. You now get input fields where you can enter proxy server and proxy port. Enter the same things here as you did when setting up proxy for your desktop client.

  f. Tap "SAVE" to save your changes


IOS

Configuring the proxy server on an IOS device is even simpler:

   a. just go to the network (WiFi) settings
   b. tap on the WiFi network you're currently connected to, the proxy settings will be visible at the bottom of the page
   c. tap the "Manual" button to be able to set proxy settings manually



   d. enter the same proxy settings as you did for the desktop browser



6. Click the "Start recording" button on your desktop in the LoadImpact interface.
You should see confirmation that the proxy recorder has started, do not close this window. The proxy recorder sometimes can take a few moments to connect initialize, so please try again if it does not launch on the first try.


7. Use your mobile device to run your app, or whatever you want to test. Note that if you have apps running in the background these can generate traffic too. Make sure you're only running the app you want to test.

8. Click "Stop recording" in the LoadImpact interface on the desktop. This stops the recording and shows you the user scenario code generated from the transactions recorded from your mobile device.

You now have a user scenario containing the transactions made by your mobile device. Sometimes, such a recording can be used straight out of the box as a user scenario in a load test, and sometimes you will need to modify it, for example if your app requires user authentication and you want the simulated users in your load test to authenticate with different user credentials (username/password combinations) you would have to edit your user scenario code to accomplish that - see here for more info on how to make simulated clients login as different users.

The next step would be to add the user scenario to your test configuration, and select what type of client and what type of network emulation the load test should be using. This is done in the test configuration interface:


When selecting a network speed, these are the limits set for each:
4G - 10,240 kbps
3G - 3,072 kbps
EDGE - 240 kbps
DSL - 5120 kbps
Unlimited - As fast as AWS can provide, we set no limit on our end.


Note here that "Android" emulation means we emulate the standard Android browser - both the User-Agent string and how many concurrent connections it uses when fetching content. If you are testing an app that behaves differently, you can override these settings in the user scenario code using our load script API functions http.set_max_connections() and http.set_user_agent_string()
