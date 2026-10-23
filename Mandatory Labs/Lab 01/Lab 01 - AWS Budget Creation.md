<div align="center">

# 💰 AWS Budgets: Cost Control with Email Alerts

**Hands-on lab: creating a cost budget and automatic alerts on AWS**

![AWS](https://img.shields.io/badge/AWS-Budgets-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Amazon SNS](https://img.shields.io/badge/Amazon-SNS-E7157B?style=flat-square&logo=amazonaws&logoColor=white) ![FinOps](https://img.shields.io/badge/Topic-Cost%20Management-2E7D32?style=flat-square) ![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

![Certification](https://img.shields.io/badge/Goal-AWS%20Certified%20Solutions%20Architect%20Associate-232F3E?style=flat-square&logo=amazonaws&logoColor=white) ![Bootcamp](https://img.shields.io/badge/Escola%20da%20Nuvem-Class%20C10%20OUT%20--%20DPCN%2019-0A66C2?style=flat-square) ![Language](https://img.shields.io/badge/Lang-English-blue?style=flat-square)

</div>

---

## 📑 Table of Contents

- [📖 About the Project](#-about-the-project)
- [🎯 Objectives](#-objectives)
- [🧩 Scenario](#-scenario)
- [🏗️ Architecture](#️-architecture)
- [✅ Prerequisites](#-prerequisites)
- [📊 Result](#-result)
- [🔍 Validation](#-validation)
- [💡 Key Takeaways](#-key-takeaways)
- [🧹 Cleanup and Cost Notes](#-cleanup-and-cost-notes)
- [☁️ AWS Services Covered](#️-aws-services-covered)
- [👤 Author](#-author)

---

## 📖 About the Project

This project documents a hands-on lab completed during the **Escola da Nuvem** training program (**Class C10 OUT – DPCN 19**), part of my preparation for the **AWS Certified Solutions Architect – Associate** certification.

The lab walks through setting up a spending budget with **AWS Budgets**, including an **email alert** triggered by a usage threshold, and then verifying the resulting configuration.

> 💡 Cost visibility is a core part of well-architected workloads. A budget with alerts turns cost management from **reactive** (finding out at the end of the month) into **proactive** (acting while spending happens).

---

## 🎯 Objectives

- 🔎 Access **AWS Budgets** through the AWS Management Console
- 🛠️ Create a budget with a custom limit
- 📧 Configure email alerts based on usage percentage
- ✅ Validate the budget creation and the notification setup

---

## 🧩 Scenario

As a student using AWS for learning and experimentation, the goal is to avoid billing surprises and keep spending under control.

| Parameter | Value |
|---|---|
| 💵 Budget amount | **US$ 10.00** |
| 🔔 Alert threshold | **10%** of the budgeted amount (**US$ 1.00**) |
| 📊 Trigger type | **Actual** cost |
| 📬 Notification | Email |

---

## 🏗️ Architecture

```mermaid
flowchart LR
    A["📊 AWS Budgets"] -->|Notification| B["📣 Billing alert<br/>SNS topic"]
    B -->|Email| C["👥 Users"]

    subgraph AWS["☁️ AWS Cloud"]
        A
        B
    end
```

When actual cost exceeds the configured threshold, AWS Budgets sends a notification through an SNS topic, which delivers the email to the registered recipient.

---

## ✅ Prerequisites

- ☁️ AWS account provided by Escola da Nuvem, with first access already configured
- 📧 A valid email address to receive alerts
- 🔐 Console login with the correct account (always check the account shown at the top right of the console)

> ⚠️ In the training environment, permissions are limited to what each lab requires. Errors may appear when accessing services or resources that are not enabled for the account.

---

## 📊 Result

### 💵 Budget created

Budget list in the **AWS Budgets** console, showing the lab budget, the defined amount, and the threshold status.

<div align="center">

![Budget created in AWS Budgets](docs/images/budget_created.png)

*Figure 1: budget listed in the AWS Budgets console, with a US$ 10.00 limit.*

</div>

---

## 🔍 Validation

In the budgets list, check that:

| ✔️ | Item | Expected |
|---|---|---|
| ☑️ | **Name** | `my-budget-<first-name-last-name>` |
| ☑️ | **Budget** | **US$ 10.00** |
| ☑️ | **Amount used** | Matches current spend (typically close to US$ 0.00 in a fresh account) |
| ☑️ | **Thresholds** | Status shown as **OK** while under the limit |

---

## 💡 Key Takeaways

- 🔔 **Alerts before overruns:** thresholds give you time to react before costs exceed your limit.
- 🕵️ **Investigate costs early:** a notification is the cue to find where spending comes from.
- 🧯 **Corrective action:** shut down unused resources and optimize services in time.
- 📉 **Avoid surprises:** monitor spending throughout the billing cycle, not only at its end.
- 🧠 **Exam relevance:** cost management and monitoring tools such as AWS Budgets are part of designing cost-optimized architectures.

---

## 🧹 Cleanup and Cost Notes

- ✅ Creating a budget in this lab is part of the course setup, and **the budget is kept** to monitor resources throughout the training.
- 🔌 When an alert arrives, check for **resources left running** to avoid unnecessary charges.
- 💲 Always review current AWS pricing and Free Tier terms for your account.

---

## ☁️ AWS Services Covered

![AWS Budgets](https://img.shields.io/badge/AWS-Budgets-FF9900?style=flat-square&logo=amazonaws&logoColor=white) ![Amazon SNS](https://img.shields.io/badge/Amazon-SNS-E7157B?style=flat-square&logo=amazonaws&logoColor=white) ![AWS Management Console](https://img.shields.io/badge/AWS-Management%20Console-232F3E?style=flat-square&logo=amazonaws&logoColor=white)

| Service | Role in the lab |
|---|---|
| 📊 **AWS Budgets** | Creates the US$ 10.00 budget and sets the 10% alert threshold |
| 📣 **Amazon SNS** | Billing alert topic that delivers the email notification |
| 🖥️ **AWS Management Console** | Interface used to access and configure the services |

---

## 👤 Author

**Lucas Barretto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucaspimentabarretto)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/lbarretto)

---

<div align="center">

☁️ Lab completed as part of the **Escola da Nuvem** program, Class **C10 OUT – DPCN 19** 🚀

</div>