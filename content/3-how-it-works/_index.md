---
title : "About GuardDuty"
date : "`r Sys.Date()`"
weight : 3
chapter : false
pre : " <b> 3. </b> "
---

**Content**
- [Data sources](#data-sources)
- [Findings](#findings)

#### Data sources
Since being activated in an AWS Region, GuardDuty conducts analysis of all data coming from:
1. VPC Flow Logs
2. CloudTrail Logs
3. DNS Logs
   - DNS cached logs come from DNS resolvers (owned by AWS) for VPCs and are not directly accessible from the user side.
   - If the DNS resolver is configured independently by yourself or from a third party, GuardDuty will not be able to retrieve, process and identify threats from this data source.

GuardDuty can access all data sources (mentioned above) even if they have not been activated before.

GuardDuty is a **Regional** service, so to be able to track data in an AWS Region, you must enable it in that AWS Region.


No matter how small or large you have AWS resources (such as VPCs or IAM users), GuardDuty will not have any effect on any resources as the processing will only be performed internally. set inside the GuardDuty service.


GuardDuty's pricing will be based on
- Number of CloudTrail events analyzed
- Volume of VPC flow logs (in **GB**)
- Volume of DNS log (in **GB**)


#### Findings
GuardDuty will actively observe and monitor any abnormal signs
- From 3 data sources (mentioned above)
- From EC2 instances
- From AWS IAM resources

You will easily access the details of Findings detected by GuardDuty in the **Findings** bar. Each Finding will be broken down into multiple pieces of information in a format that allows us to easily read and deal with security risks.

```
ThreatPurpose : ResourceTypeAffected / ThreatFamilyName . ThreatFamilyVariant ! Artifact
```

For highly unpredictable cases and behaviors, in order to learn them and analyze anomalies, GuardDuty's Machine Learning engine will need a baseline period of 7 to 14 days.

**For example: **
1. An EC2 instance initiates communication with a remote server through an unusual port.
2. An IAM user starts changing Route Tables while not doing this before.

For the above cases, all searches will be based on Signatures, so they will be discovered within 10 minutes of the CloudFormation stack being completed. In the process of threat detection, the delay will be more or less based on the frequency of occurrence of the relevant information and the total time that GuardDuty can retrieve and analyze such information at the data sources. specific material.


When there are any changes at Findings, based on the settings coming from Amazon CloudWatch Events, GuardDuty will send you a notification immediately within 5 minutes. Any related changes from subsequent iterations will have the same ID as the original Finding, and notifications will be sent every 6 hours from the first submission, to deal with the massive influx of notifications within the same Finding. to the user.