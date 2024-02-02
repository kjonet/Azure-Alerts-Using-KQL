![Imgur](https://imgur.com/NG4qYhV.jpg)

## Introduction

This project will show you how to create alerts that will generate incidents in your Microsoft Sentinel using KQL. KQL, or Kusto Query Language, is a query language developed by Microsoft used to analyze structured and semi-structured data. KQL is most useful when working with Microsoft Sentinel, as it can query for potential security incidents. We will explore using KQL in a few use cases and then create our own alerts that will trigger security incidents in Microsoft Sentinel. 


## Use Case #1: Detecting Brute Force
An attack technique that might be used to gain unauthorized or elevated privileges to a system would be brute force. Brute force is when attackers will continuously guess every possible combination of values within a password until they succeed. If that attacker can gain access to an organization's network, that can pose detrimental risks to a company, its data, its customer data, and so on. Let's generate a query within Microsoft Sentinel to see how we can detect this from a Windows Virtual Machine. 
<br>

Since we are ingesting logs from a Windows VM, we will filter our results using the SecurityEvent table where EventID is 4625.
<br>
![img](https://i.imgur.com/05ceyo5.png)

<h3>Results returned: </h3>

![img](https://i.imgur.com/UhKJTbC.png)

This simple query generated over 30,000 results in a span of 24 hrs. Yikes! Pretty scary to know that attackers are active as we speak just trying to get into your test environment. Okay, but what if we wanted to limit the scope of our results? Let's create a query that limits the time when the event took place and only displays specific information such as the Attacker's IP address, the hostname destination, and the number of times the event was triggered. 

<h3>Limiting our results </h3>

Let's say instead of the past 24 hrs, we only want to query results that took place in the last 60 minutes. We would add to our query:
<br>
- | TimeGenerated > ago(60m)

Now we want to obtain information from the AttackerIP table that only filters out for the IP address, EventID, Activity, and hostname destination. Lastly, we want to know how many times a failed login occurred by using the FailureCount table. The final query should look something like this: 

![img](https://i.imgur.com/O75QuGH.png)

As a result of this query, we've been able to return 8 suspicious brute force incidents that happened in the last 60 minutes. Let's take a look at one of these logs. 
![img](https://i.imgur.com/GIfupVo.png)

According to our logs, the attack IP is 146.19.191.29. Our host, "windows-vm", was the attacker's destination and they attempted to brute force 700 times. (Wow... persistent aren't we?)




