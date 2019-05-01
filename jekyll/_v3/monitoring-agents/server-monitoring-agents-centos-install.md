---
layout: classic-docs
title: Installing Server Monitoring Agents on CentOS
description: Brief install guide to get Server Monitoring Agents installed on a server running CentOS
categories: [monitoring-agents]
order: 5
redirect_from: /knowledgebase/articles/1094101-how-to-install-server-monitoring-agents-centos
---

***

LoadImpact distributes .deb and .rpm packages for Linux systems and a Windows installation package. Linux packages are distributed by the [packagecloud.io service](https://packagecloud.io/loadimpact/server-metrics-agent). Linux installation requires Python v2.6 or v2.7 installed on your server.

Install the **psutil** dependency
```
$ sudo yum install epel-release
$ sudo yum install python-psutil
```
Packagecloud.io provides a setup script that manages .deb package installation including https-transport setup, setting PGP verification keys and adding a system .list file. You can see details [here](https://packagecloud.io/loadimpact/server-metrics-agent/install). So quick way is to download and run this script. Alternatively you can run the commands manually by following the instructions in the "manual" tab.
```
wget https://packagecloud.io/install/repositories/loadimpact/server-metrics-agent/script.rpm.sh
sudo bash script.rpm.sh
```
Install last version of the package
```
sudo yum install li-metrics-agent
```
Run the configuration tool. You will be asked to give the agent a name and your server metrics token. The name is used for identification in the LoadImpact application so it is recommended to choose a short readable name. The name and token will be written to the `config-file`. You can read about advanced configuration of Nagios and custom metrics plugins here.
```
$ sudo li-metrics-agent-config
```
You can also click the "Check installation" button on the monitoring tab within LoadImpact. If a new entry appears in the list then the agent has been successfully installed.



If something goes wrong it's recommended you check the .log file:
```
$ tail /var/log/li_metrics_agent.log
```
You can manage the agent as a regular Linux service
```
# Upstart systems (Centos 6)
sudo initctl status|stop|start|restart li_metrics_agent

# Systemd systems (Centos 7)
sudo systemctl status|stop|start|restart li_metrics_agent.service
```
