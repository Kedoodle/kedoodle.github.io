---
layout: post
title: "Learning the Elastic Stack"
date: 
permalink: /notes/linkedin-learning/learning-the-elastic-stack
---

# Overview

The Elastic Stack, formerly known as the ELK Stack, refers to the core products offered by Elastic - Elasticsearch, Kibana, Beats, and Logstash. Together, these open source products reliably and securely collect and process data (Logstash and Beats), store, index, and analyse it (Elasticsearch), and visualise it (Kibana).

Elastic offers it's own hosted Elastic Stack by way of Elastic Cloud. There are similar services provided by AWS (Amazon Elasticsearch Service) and Google Cloud Platform (Elasticsearch). You can also roll your own in Kubernetes.


# Why Elastic?

Elastic ingests data (e.g. access logs, fields, application logs) from multiple sources (e.g. clients, servers, applications) and stores it centrally. Once the data is added, you can answer questions by analysing and correlating the data. These answers and data can then be visualised however you like.

Further to the core stack, Elastic also offers an additional bundle called X-Pack. Included are Security, Alerting, Monitoring, Reporting, Graph, and Machine Learning features which integrate with the Elastic Stack.


# Installing the stack

[Installation](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-elastic-stack.html) can be done in a number of ways. In addition to traditional downloads, Elastic maintains package manager support (including a [Homebrew repository](https://www.elastic.co/guide/en/elasticsearch/reference/current/brew.html) `brew tap elastic/tap`) and [Docker images](https://www.elastic.co/guide/en/elastic-stack-get-started/current/get-started-docker.html) for the stack.


I chose the Docker option for the simplest set up for this course.


# Elasticsearch

The `Discover` tab of Kibana allows you to search through all of the data. Think of it as a search engine of your data. We can see the available data over time, search it, and filter it. 

<img src="/assets/kibana-discover.png" alt="kibana-discover.png" title="Navigating to Discover in Kibana"/>

Here's an example search using the `kibana_sample_data_flights` index pattern. We search for `Melbourne` and select the `FlightNum`, `OriginCityName`, and `DestCityName`. This reveals all of the flights coming in and going out of Melbourne in the last day.

<img src="/assets/kibana-discover-melbourne.png" alt="kibana-discover-melbourne.png" title="Example use of Kibana Discover to search for Melbourne flights"/>


# Logstash

Logstash ingests data from many different sources and sends it to a stash such as Elasticsearch. It can connect to servers, applications, networking devices, firewalls etc. Logstash works in three steps: inputs to connect to sources, parse and transform the data, and outputs to route data to the desired stashes.


# Beats

Beats are single-purpose data shippers which gather specific pieces of data. They then feed the gathered data either directly into Elasticsearch (when no changes are needed) or Logstash (where parsing and transforms are needed).

Elastic provides some core Beats, but there are also community made Beats and an opportunity to build your own custom Beat.
- **Filebeat**: Logs
- **Metricbeat**: System metric data
- **Packetbeat**: Network data
- **Winlogbeat**: Windows event logs
- **Auditbeat**: Audit and file integrity data 
- **Heartbeat**: Uptime monitoring data
- **Functionbeat**: Serverless cloud data


# Log management

The Elastic Stack can be used as a best in class log management tool. It can gather different kinds of logs from multiple systems, index them in Elasticsearch, and explore the data in Kibana. It's highly scalable and can support almost any system. Some easy examples include Windows, Mac, servers, Docker, and databases.

Ultimately, good log management can lead to better decisions and realtime alerting.


# System metrics

You can monitor system metrics like CPU, RAM, and uptime with the help of Metricbeat. The system can be a laptop, desktop, server, virtual machine or even Docker container.