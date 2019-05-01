---
layout: classic-docs
title: LoadImpact's Open Source Server Monitoring Agents
description: How to install LoadImpact's open source Server Monitoring Agents to add more data and context to your load tests.
categories: [monitoring-agents]
order: 1
redirect_from:
  - /knowledgebase/topics/114939-monitoring
  - /knowledgebase/articles/265482-server-monitoring-formerly-server-metric-agents
  - /knowledgebase/articles/265482-server-metrics-tutorial
---

***

Server Monitoring helps you to measure performance metrics from your own servers and integrates these metrics with the graphs generated from your LoadImpact test.

LoadImpact offers three options to monitor a system under test(SUT).

You can use:

- Our open source monitoring agents (described in this article)
- [An Integration with New Relic ]({{ site.baseurl }}/3.0/monitoring-agents/new-relic-integration/)
- [Sending metrics from another APM via our Server Monitoring Agents (advanced)]({{ site.baseurl }}/3.0/monitoring-agents/other-monitoring-tools/)


These instructions, plus source code, are available on our [GitHub Repo](https://github.com/loadimpact/loadimpact-server-metrics)

In all cases, monitoring agents will provide you with additional context to monitor the system under test during a test. This allows you to make powerful correlations helping you identify performnace problems, faster.

If you wish to collect more specialized metrics than just the standard physical server metrics (i.e. CPU usage, memory, Disk I/O and Network I/O), our Server Monitoring Agents are also compatible with the Nagios plugin system. A tutorial on custom metrics with [Server Monitoring agents is available here]({{ site.baseurl }}/3.0/monitoring-agents/configuring-custom-monitoring-agent-metrics/)

***


### Step 1: Download and install Server Monitoring Agent

The first step is to download the agent installer for your server platform. Download links can be found below.

**DEPENDENCIES:**

The server monitoring agent requires Python 2.6+ and the module `psutil`. Both Python and `psutil` are bundled in the Windows installer, but on Ubuntu you may need to `apt-get install python-psutil`


Packages/Installers:  The latest installers/packages of our Server Monitoring Agents are found within our app here: [Monitoring](https://app.loadimpact.com/monitoring) or by navigating to the integrations menu -> Server Agent (Login required).

![Navigation to install agent]({{ site.baseurl }}/assets/img/v3/monitoring-agents/load-impacts-open-source-monitoring-agents/monitoring-agent-install-navigation.png)

### Step 2: Generate a Server Monitoring Token

Below the packages, you can also generate your token:

![Token generation]({{ site.baseurl }}/assets/img/v3/monitoring-agents/load-impacts-open-source-monitoring-agents/monitoring-agent-token-generation.png)


You will use the **same token** for all LoadImpact Monitoring Agents. You can re-generate a new token at any time if you believe it has been compromised or distributed to someone outside your company. Upon re-generation, the old token is no longer valid.

### Step 3: Name your agents and give them access

During the installation of the server monitoring agent you will be asked to provide a name for the agent and the token. You should give it a contextual name to make it easier to identify it later on.

If you are using the python files to install your agent, please read [README.md](https://github.com/loadimpact/loadimpact-server-metrics/blob/master/README.md). You will need to set the name and token within the config file. Using our provided template, this can be found on lines 1-8 of li_metrics_agent.conf:
```
# LoadImpact Server Metrics sample configuration file

[General]
# agent_name - hostname or any other identifier for this server, choose wisely
agent_name = myagent

# server_metrics_token - assigned to your account on loadimpact.com
server_metrics_token = SERVER_METRICS_API_TOKEN
```

_To ensure that the agents can connect, please allow outgoing port 443 against api.loadimpact.com in your firewall._

### Step 4: Wait for check-in

Once installed, click "Check installation" within LoadImpact. We will see if your agent has started communication with us.

![Check Install]({{ site.baseurl }}/assets/img/v3/monitoring-agents/load-impacts-open-source-monitoring-agents/check-install.png)


### Step 5: Configure your test to include the agents

Once the installation is complete, the server monitoring agent will start automatically and you will be able to select it on the test configuration page. You must specifically add the Server Monitoring Agent to any test you wish to collect these metrics for.

![Check Install]({{ site.baseurl }}/assets/img/v3/monitoring-agents/load-impacts-open-source-monitoring-agents/test-configuration.png)

Refer to: [Test Configuration]({{ site.baseurl }}/3.0/test-configuration/what-is-a-test-configuration/) for more details

### Step 6: Review your Server Monitoring results

Once your test has started, you will be able to see your Server Monitoring results in real time. Scroll down below the main graph and "Add Visualization Widget" -> Select your monitoring agent. You can now plot your metrics on the main graph.

***


### FAQs

#### How do the Server Monitoring Agents communicate with LoadImpact?

LoadImpact servers never contact the server monitoring agents. It is the other way around - the agents contact LoadImpact regularly. Since most firewalls allow outgoing HTTP connections, this generally ensures that the data can be sent to our servers. If you have outgoing firewall rules, please allow outgoing **port 443 against api.loadimpact.com** in your firewall.

The agents contact LoadImpact every few minutes. If our system sees you are logged in to your LoadImpact account we will tell the agents to start contacting us a little more frequently. This is to minimize the waiting time for tests to start with monitoring agents.

When a test is started with an agent configured, we will wait for that agent to contact us as described above. When it does - by sending us an HTTP request - we will respond to the agent to begin collecting data and sending it to us. This is considered active mode - collecting data and transmitting it to us continuously throughout the load test (using HTTP). When the test is complete, we will tell the agent to stop collecting data. When you logout from loadimpact.com, we will also tell the agent to contact us less frequently.

**Note:** On Windows Server you must make sure that the proper Truested Root CA Certificate ("GeoTrust Global CA") is present in the Trusted Root Certificate Authorities. If you don't have this root CA certificate you'll see CERTIFICATE_VERIFY_FAILED errors in the Server Monitoring Agent log file. The needed root CA certificate can be downloaded from [GeoTrust](https://www.geotrust.com/resources/root_certificates/certificates/GeoTrust_Global_CA.pem)
(from this page: https://www.geotrust.com/resources/root-certificates/), and installed by following these steps:


1. Open Microsoft Management Console (Start -> Run -> mmc.exe)
2. Choose File -> Add/Remove Snap-in
3. In the Standalone tab, choose Add
4. Choose the Certificates snap-in, and click Add
5. In the wizard, choose the Computer Account, and then choose Local Computer. Press Finish to end the wizard
6. Close the Add/Remove Snap-in dialog
7. Navigate to Certificates (Local Computer)
8. Right-click the Trusted Root Certification Authorities store and choose All Tasks -> Import
9. Follow the wizard and provide the certificate file that you downloaded from GeoTrust


_In a future version the Server Monitoring Agent will make an attempt to trigger an "Automatic Root Certificate Update" to download the needed root CA certificate, if the group policy hasn't been turned off (Computer Settings / Administrative Templates / System / Internet Communication Management / Turn off Automatic Root Certificate Update), but in some environments this might not be possible due to firewall restrictions etc. In those cases the manual root CA certificate installation process described above will be necessary._


#### What are the default values returned for the metrics and can they be changed?

The default values reported back are as follows:
- Network - bps
- Disk- bps
- CPU - % of system-wide utilization
- Memory - % of utilization
- Our agents are also compatible with the Nagios plugin system if you wanted to send other metrics back during a test.

If you would like to configure custom metrics with our Monitoring Agents, please see [this tutorial]({{ site.baseurl }}/3.0/monitoring-agents/configuring-custom-monitoring-agent-metrics/).


You can report back on different things that may be interesting for you. For example, packets/s for network performance or an individual CPU. You can change the defaults by opening the `li_metrics_agent.conf` file in the install directory and making the necessary changes

#### My agent is not working! Help!

In the event that an agent takes too long to check-in, or just fails to report back information we recommend the following steps to troubleshoot:

1. Ensure port 443 is open against api.loadimpact.com
2. Restart the agent (because turning it off and on again works far too often with computers)
3. Check the log file in the install folder (and send it to us if you need help.)
