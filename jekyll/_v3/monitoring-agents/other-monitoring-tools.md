---
layout: classic-docs
title: Integrating other Monitoring products into LoadImpact
description: While we do not have official integrations with other monitoring tools, it is possible, with some effort, to pull them into LoadImpact for analysis. We provide this example, using AppDynamics
categories: [monitoring-agents]
order: 3
redirect_from: /knowledgebase/articles/801591-how-to-get-your-appdynamics-metrics-into-load-impa
---

***

While the only official integration we currently have is with New Relic, it is possible to pull metrics from other tools, via our open source agents, into LoadImpact for analysis.

How to get your AppDynamics metrics into LoadImpact test results



This example shows how to get a single CPU metric into LoadImpact test results using the LoadImpact Server Metrics Agent.



Assumption: You have your AppDynamics agents set up, configured and running.



1. Get your LoadImpact Server Monitoring Agent and install it on your server.
  - See this Knowledge Base Article if this is not already set up



2. Go into your AppDynamics panel and find the REST API URL for the particular metric you want to collect.
  -Example here: http://community.appdynamics.com/t5/AppDynamics-Discussions/Rest-APIs/td-p/18856
  -For our example collecting our CPU metric it’s `https://loadimpact.saas.appdynamics.com/controller/rest/applications/GSSTO002/metric-data?metric-path=Application%20Infrastructure%20Performance%7CMachine%20Agent%7CHardware%20Resources%7CCPU%7C%25Busy&time-range-type=BEFORE_NOW&duration-in-mins=1`


3. Assuming you have installed your LoadImpact Server Metrics Agent on a windows box, create a powershell script containing this (named appdynamics_cpu.ps1):


        $user = "myppadynamicsuser@myappdynamicsaccount"
        $password = "<myappdynamicspassword>"
        $uri = https://loadimpact.saas.appdynamics.com/controller/rest/applications/GSSTO002/metric-data?metric-path=Application%20Infrastructure%20Performance%7CMachine%20Agent%7CHardware%20Resources%7CCPU%7C%25Busy&time-range-type=BEFORE_NOW&duration-in-mins=1
        $auth = 'Basic ' + [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($user+":"+$password ))
        $req = New-Object System.Net.WebClient
        $req.Headers.Add('Authorization', $auth)
        [xml]$resp = $req.DownloadString($uri)
        $cpu =  $resp."metric-datas"."metric-data".metricValues."metric-value".value
        "AppDynamics CPU | 'AppDynCPU'=$cpu"


4. 	And to be able to run the powershell script we are somewhat lazy (and skip setting up proper authorization and authentication on our windows box, do not do this in your production environment and create a cmd file containing this (named appdynamics.cmd):

        @ECHO OFF
        powershell -noprofile -ExecutionPolicy Bypass -f "c:\Server Metrics Agent\appdynamics_cpu.ps1"
        EXIT /B 0

5. Now to the interesting part, how to get that into LoadImpact!
  - Open the li_metrics_agent.conf file located in your server metrics agent install folder. On a windows box that typically is c:\server metrics agent\.
  - Find the section that starts with:
```
        # An external script
        #
        # The script needs to output data in the Nagios performance data format, see:
        # http://nagiosplug.sourceforge.net/developer-guidelines.html#AEN201
```
  - This section is where you add any and all extra metrics to collect during the test run. Add the following:
```
        # CPU usage from AppDynamics
        [test5]
        command = "c:/Server Metrics Agent/appdynamics.cmd"
```
  - Save, and recycle your server metrics agent service (it’s aptly named LoadImpact Server Metrics Agent Service) to pick up the configuration changes.



6. The next time you run your test and include the server metrics agent in the test config (see #1 for usage) you will get the AppDynamics metric included so you can plot like this:



 Image: https://lh6.googleusercontent.com/HGytbhRVYtU-oWwJplN6Asy2YYWQJNchzuu-uGm7KoFxBlRpqrLMggJjfpp1uJQJye3yNXP11ULjjLU8HiFriRwW_bXyZRRuvAw7un52VBEIDcz4d8rbswTKvWGWPvXsDT1PEeYf



And for those of you not deploying the LoadImpact Server Metrics Agent on windows but rather on a *nix this shell script (named appdynamics.sh in this example) will output the same thing as the cmdfile/powershell combo on windows (it uses curl to call the REST API, get here if you don’t have it http://curl.haxx.se/):



```
        #!/bin/sh
        echo "AppDynamics CPU|'AppDynCpu='"$(curl -s -G -u myappdynamicsuser@myappdynamicsaccount:myappdynamicspassword "https://loadimpact.saas.appdynamics.com/controller/rest/applications/GSSTO002/metric-data?metric-path=Application%20Infrastructure%20Performance%7CMachine%20Agent%7CHardware%20Resources%7CCPU%7C%25Busy&time-range-type=BEFORE_NOW&duration-in-mins=1" | grep -oPm1 "(?<=<value>)[^<]+")
```


And just for clarity you would add this line to your li_metrics_agent.conf


```
        # CPU usage from AppDynamics
        [test5]
        command = "appdynamics.sh"

```

Now that you know how to do it, go find your analytics metrics in AppDynamics, include them in your test runs and enjoy correlating analytics and results at the speed of test!
