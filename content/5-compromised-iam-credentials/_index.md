---
title : "Compromised IAM credentials"
date : "`r Sys.Date()`"
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

**Scenario 2: Compromised IAM credentials**

You have completed the first attack simulator and are back with your cup of coffee. However, you continue to receive new notifications about Findings related to AWS IAM services. The body of the first message indicates that using **IAM credentials**, some **API calls** were made from the IP address that was added to the **Threat List** (in this post). before).

**Content**
- [](#)
    - [Review question](#review-question)

#### Architecture Overview
![architecture-overview](/images/5-architecture-overview.png?featherlight=false&width=60pc)

1. This *EC2 malicious instance* makes **API calls**, the EIP of this instance has been added to **Threat List**. The contents of **API calls** are logged in CloudTrail's logs.
2. GuardDuty observes the logs of **CloudTrail Logs** along with **VPC Flow Logs** and **DNS Logs**, thereby assessing the situation on certain bases.
3. GuardDuty generates corresponding Findings and simultaneously sends details to GuardDuty Console and EventBridge Events.
4. **EventBridge Event Rule** activates **SNS Topic**.
5. **SNS Topic** sends out E-mail notifications with related information.

#### Investigation process

---

**Go to GuardDuty Console**

To conduct a review of the Findings:
1. Access GuardDuty Console at **us-west-2**
2. We will see the Findings in the following format.
   1. `Recon:IAMUser`.
   2. `UnauthorizedAccess:IAMUser`.

![guardduty-finding-recon-iamuser](/images/5-guardduty-findings.png?featherlight=false&width=90pc)

3. If there isn't any Finding, proceed to press the Refresh button and wait.
4. From Finding - `Recon:IAMUser/MaliciousIPCaller.Custom`, we can easily retrieve the following information:
   1. What happened?
   2. What AWS resources are affected?
   3. When did this event happen?

5. Under the **Resource Affected** section, you will find the `User Name` related to this Finding.

![guardduty-finding-recon-iamuser-affected-resources](/images/5-guardduty-finding-recon-iamuser-affected-resources.png?featherlight=false&width=90pc)

> Based on the format reviewed in detail in the previous section, what security incident can you pinpoint through the Finding style?

This finding indicates that the **IAM credential** of the above `User Name` could have been exposed by **API calls** derived from an IP address that was previously added to the **Threat List**.

> What actions were taken by this IAM User?

In the **Action** section, we see that the `DescribeParameters` action has been performed.

![guardduty-finding-recon-iamuser-action](/images/5-guardduty-finding-recon-iamuser-action.png?featherlight=false&width=90pc)

> How can we see all the remaining actions, taken by this IAM User, within the last 1 hour or 1 day ago?

GuardDuty is capable of analyzing large amounts of data to pinpoint the hazards present in your environment. However, during the investigation and remediation steps, we also need to combine many different data sources to have the most correlated view possible.

In this case, the analyst can use insights that can be found in user behavior logs through **CloudTrail**.

![cloudtrail-event-history](/images/5-cloudtrail-event-history.png?featherlight=false&width=90pc)
---

**Check EventBridge Event Rule**

1. Access the CloudWatch Console at **us-west-2**.
2. In the left-hand navigation bar, under **Events**, select **Rules**. You will see 3 rules have been set up (by CloudFormation Template), starting with a prefix of the following from `GuardDuty-Event.`.
3. Proceed to select the rule named `GuardDuty-Event-IAMUser-MaliciousIPCaller`.

![eventbridge-event-iam-malicious-ip-caller](/images/5-eventbridge-event-iam-malicious-ip-caller.png?featherlight=false&width=90pc)

4. You will easily notice that there is only one target in the **Targets** area, which is **SNS Topic**.

![eventbridge-event-iam-malicious-ip-caller-targets](/images/5-eventbridge-event-iam-malicious-ip-caller-targets.png?featherlight=false&width=90pc)

As it turned out, Alice had never set up a Lambda Function to perform the Remediation process because the Security team decided that they would proceed manually with this Finding.

> The combination of **GuardDuty and EventBridge Events** provides flexibility that makes it easy to create an automated Remediation process. Lambda functions or solutions from AWS partners are the top choices.

For certain Findings, we may end up configuring notifications and resolving issues manually, rather than automatically. Because when designing the automation process, we will have to pay attention to and evaluate the results that the Remediation process brings, including advantages and disadvantages.

> In addition, we can set **Target** to some other AWS resources like **SSM Run Commands** or **Step Function State Machine**.

---

**Resolve the situation**

Since Alice has never set up the Remediation process for this Finding, we need to do it manually. While the Security team is analyzing the behavior of this IAM user to better define the scope of the vulnerability, we need to take some steps to disable **Access Key** to prevent programming. the next actions.
1. Access the IAM Console.
2. In the left-hand navigation bar, select **Users**.

![iam-users](/images/5-iam-users.png?width=90pc)

3. Based on GuardDuty Finding and E-mail message, we can easily choose IAM user - `GuardDuty-Example-Compromised-Simulated`.
4. In the user `GuardDuty-Example-Compromised-Simulated`, we select the **Security Credentials** bar.

![iam-users-compromised-simulated-credential](/images/5-iam-users-compromised-simulated-credential.png?featherlight=false&width=90pc)

5. In the **Access Keys** section, based on the **Access Key ID** information from Finding, we proceed to press the `Make Inactive` button.

![iam-users-compromised-simulated-credential-deactivate](/images/5-iam-users-compromised-simulated-credential-deactivate.png?featherlight=false&width=90pc)

#### Review question
1. What data source was used by GuardDuty to identify this threat?
2. What Permissions are IAM users allowed to use?
3. Why did the Security team decide to go against the automated Remediation process?