![Imgur](https://imgur.com/NG4qYhV.jpg)

## Introduction

This project will show you how to create alerts that will generate incidents in your Microsoft Sentinel using KQL. KQL, or Kusto Query Language, is a query language developed by Microsoft used to analyze structured and semi-structured data. KQL is most useful when working with Microsoft Sentinel, as it can query for potential security incidents. We will explore using KQL in a few use cases and then create our own alerts that will trigger security incidents in Microsoft Sentinel. 


## Use Case #1: Detecting Brute Force On A Windows Machine
An attack technique that might be used to gain unauthorized or elevated privileges to a system would be brute force. Brute force is when attackers will continuously guess every possible combination of values within a password until they succeed. If that attacker can gain access to an organization's network, that can pose detrimental risks to a company, its data, its customer data, and so on. Let's generate a query within Microsoft Sentinel to see how we can detect this from a Windows Virtual Machine. 
<br>

Since we are ingesting logs from a Windows VM, we will filter our results using the SecurityEvent table where EventID is 4625.
<br>
![img](https://i.imgur.com/05ceyo5.png)

<h3>Results Returned: </h3>

![img](https://i.imgur.com/UhKJTbC.png)

This simple query generated over 30,000 results in a span of 24 hrs. Yikes! Pretty scary to know that attackers are active as we speak just trying to get into your test environment. Okay, but what if we wanted to limit the scope of our results? Let's create a query that limits the time when the event took place and only displays specific information such as the Attacker's IP address, the hostname destination, and the number of times the event was triggered. 

<h3>Limiting Our Results </h3>

Let's say instead of the past 24 hrs, we only want to query results that took place in the last 60 minutes. We would add to our query:
<br>
- | TimeGenerated > ago(60m)

Now we want to obtain information from the AttackerIP table that only filters out for the IP address, EventID, Activity, and hostname destination. Lastly, we want to know how many times a failed login occurred by using the FailureCount table. The final query should look something like this: 

![img](https://i.imgur.com/O75QuGH.png)

As a result of this query, we've been able to return 8 suspicious brute force incidents that happened in the last 60 minutes. Let's take a look at one of these logs. 
![img](https://i.imgur.com/GIfupVo.png)

According to our logs, the attack IP is 146.19.191.29. Our host, "windows-vm", was the attacker's destination and they attempted to brute force 700 times. (Wow... persistent aren't we?)

## Use Case #2: Detecting Malware On A Windows Machine
You get an alert within Sentinel that indicates possible malware. You have an idea of where this alert is being triggered from and why, but you aren't quiet sure. Let's use KQL to filter results that may lead to a cause. 

Just like in the first use case, we can filter our results using the SecurityEvent table for starters. Then within that table, we'll filter for the 'AlertType' where 'AntimalwareActionTaken' is the value. This query is obtained from the antimalware software installed on the vm. Next, we only want to filter through a specific host that we suspect might have the malware. To do this, we'll look through the 'CompromisedEntity' table. The entity in question will be the hostname. In our case, it's 'Windows-vm'. Lastly, we will filter out the 'RemediationSteps' where intervening may or may not be required because the AntiMalware software has already remediated the issue. In this case, we want to see if remediation is NOT required by using the '!has' expression. Lastly, let's limit the scope to about 2 days ago. 

<h3>Results Returned: </h3>

![img](https://i.imgur.com/vQ1iuJr.png)

According to the description, the anti-malware software did take the necessary steps to remediate the issue. But what was the malware that triggered this event? Let's dig further. 

![img](https://i.imgur.com/5JBocI3.png[/img])

Under 'Threat Information', where it says 'Value' we get this: ....DOS/EICAR_Test_File within the value.
As we can see, an EICAR_Test_File is mentioned. From this, we know that an EICAR file is just a test file used to test responses from anti-malware software. From the looks of this, we can classify this as a false positive. 

![img](https://i.imgur.com/hoCfIus.png)

## Use Case #3: Detecting Privalage Escaltion And Stolen Credentials Within Azure

You suspect that someone in your orginaztion may be trying to get tenent access within the azure portal. Let's use a KQL query that will help us investigate. 
First, let's define a variable. In this case our variable is 'CRITICAL_PASSWORD_NAME'. The value attached to this variable should have an input that equals 'Tenant-Global-Admin-Password'. Now that our variable has been set, we're going to filter our results starting with the AzureDiagnostics table and include the following columns in our query, the 'ResourceProvider', the 'OperationName' and the 'id_s'. We want the 'ResourceProvider' to equal "MICROSOFT.KEYVAULT" and the 'OperationName' to equal either "SecretGet" or "SecretSet". Lastly, the 'id_s' will need to contain the variable we set earlier, 'CRITICAL_PASSWORD_NAME'. The Query should look something like this: 

![img](https://i.imgur.com/vYfRxKg.png)

Looking at the logs, let's start with CallerIPAdress. This gives us insight as to where this privalage escalation originated from. If this was internal, the analyist would be able to a variety of sources such as network infractuture documentation, network monitering tools, ect, to see if this IP was local to the network.

![img](https://i.imgur.com/3mtciMW.png)

The Azure log also showcases the 'identity_claim_oid_g' columne. This column returns a unique ID assigned to users within Microsoft Entre ID. We can then use that ID to trace the user within the orginazation who may be responible for triggering this alert. 

![img](https://i.imgur.com/MhTO3an.png)

## Wrap-Up

In summary, the efficacy of KQL in incident response lies in its proficiency at promptly and efficiently identifying various security threats, including brute force attacks, malware infiltrations, and attempts to steal credentials. By presenting specific instances of KQL in action, we've highlighted its practicality within Microsoft Sentinel, illustrating how it empowers security analysts to meticulously and swiftly analyze large datasets. The showcased use cases are foundational, providing a launchpad for crafting personalized queries tailored to unique threat scenarios. The iterative process of refining and storing queries not only enhances operational efficiency but also ensures that incident responders are well-prepared to tackle emerging security challenges effectively. In the dynamic realm of cybersecurity, KQL's adaptability and extensibility position it as an indispensable tool for organizations committed to proactive incident detection and response.
