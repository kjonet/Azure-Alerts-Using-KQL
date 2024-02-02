![Imgur](https://imgur.com/NG4qYhV.jpg)

## Introduction

This project will show you how to create alerts that will generate incidents in your Microsoft Sentinel using KQL. KQL, or Kusto Query Language, is a query language developed by Microsoft used to analyze structured and semi-structured data. KQL is most useful when working with Microsoft Sentinel, as it can query for potential security incidents. We will explore using KQL in a few use cases and then create our own alerts that will trigger security incidents in Microsoft Sentinel. 

- SecurityEvent (Windows Event Logs)
- Syslog (Linux Event Logs)
- SecurityAlert (Log Analytics Alerts Triggered)
- SecurityIncident (Incidents created by Sentinel)
- AzureNetworkAnalytics_CL (Malicious Flows allowed into our honeynet)
