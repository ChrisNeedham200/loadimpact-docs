---
layout: classic-docs
title: Installing Server Monitoring Agents on Ubuntu
description: Brief install guide to get Server Monitoring Agents installed on a server running Ubuntu
categories: [monitoring-agents]
order: 4
redirect_from: /knowledgebase/articles/1093015-how-to-install-server-monitoring-agents-ubuntu
---

***

LoadImpact distributes .deb and .rpm packages for Linux systems and a Windows installation package. Linux packages are distributed by the [packagecloud.io service](https://packagecloud.io/loadimpact/server-metrics-agent). Linux installation requires Python v2.6 or v2.7 installed on your server.
Ubuntu

Install the **psutil** dependency
```
sudo apt-get install python-psutil
```
Packagecloud.io provides a setup script that manages .deb package installation including https-transport setup, setting PGP verification keys and adding a system .list file. You can see details [here](https://packagecloud.io/loadimpact/server-metrics-agent/install). A quick way to get started is to download and run this script. Alternatively you can run the commands manually by following the instructions in the "manual" tab.
```
$ wget https://packagecloud.io/install/repositories/loadimpact/server-metrics-agent/script.deb.sh
$ sudo bash script.deb.sh
```
Install last version of the package
```
$ sudo apt-get install li-metrics-agent
```
Run the configuration tool. You will be asked to give the agent a name and your server metrics token. The name is used for identification in the LoadImpact application, so it is recommended to choose a short readable name. The name and token will be written to the config file. You can read about advanced configuration of [Nagios and custom metrics plugins here]({{ site.baseurl }}/3.0/monitoring-agents/configuring-custom-monitoring-agent-metrics/).
```
$ sudo li-metrics-agent-config
```
Output like li_metrics_agent start/running, process XXXX means that the agent has properly installed as a service and started correctly. It will be automatically restarted after a crash or server reboot.

You can also click the "Check installation" button on the monitoring tab within LoadImpact. If a new entry appears in the list then the agent has been successfully installed.



If something goes wrong it's recommended you check the `.log` file:
```
$ tail /var/log/li_metrics_agent.log
```
You can manage the agent as a regular Linux service
```
# Upstart systems (Ubuntu 12.04, Ubuntu 14.04)
$ sudo initctl status|stop|start|restart li_metrics_agent

# Systemd systems (Ubuntu 16.04)
$ sudo systemctl status|stop|start|restart li_metrics_agent.service
```
