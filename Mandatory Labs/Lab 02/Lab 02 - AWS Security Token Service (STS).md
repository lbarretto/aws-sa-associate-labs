<div align="center">

# 🔐 AWS STS: Temporary Credentials with IAM Roles

**Hands-on lab: assuming roles and generating temporary credentials with AWS Security Token Service**

![AWS STS](https://img.shields.io/badge/AWS-STS-DD344C?style=flat-square&logo=amazonaws&logoColor=white) ![AWS IAM](https://img.shields.io/badge/AWS-IAM-DD344C?style=flat-square&logo=amazonaws&logoColor=white) ![Topic](https://img.shields.io/badge/Topic-Security%20%26%20Identity-2E7D32?style=flat-square) ![Status](https://img.shields.io/badge/Status-Completed-success?style=flat-square)

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
- [🧹 Resource Cleanup](#-resource-cleanup)
- [☁️ AWS Services Covered](#️-aws-services-covered)
- [👤 Author](#-author)

---

## 📖 About the Project

This project documents a hands-on lab completed during the **Escola da Nuvem** training program (**Class C10 OUT – DPCN 19**), part of my preparation for the **AWS Certified Solutions Architect – Associate** certification.

The lab demonstrates how to use **temporary credentials** on AWS through **AWS Security Token Service (STS)**: creating and assuming an **IAM role** with restricted permissions, generating temporary credentials with a Python script in **CloudShell**, and validating allowed and denied access.

> 💡 Instead of relying on long-lived access keys, STS issues credentials that **expire on their own** and carry only the permissions of the assumed role, which reduces the impact of a potential leak.

---

## 🎯 Objectives

- 🎭 Create a temporary IAM role with the `AmazonS3FullAccess` policy
- 🔄 Switch to the role in the console and via CLI
- 🔑 Generate temporary credentials through STS
- 🧪 Configure and test the credentials in the AWS CLI
- ⏳ Simulate credential expiration
- 🤝 Understand the impact of the role's trust policy
- 🗑️ Delete all the resources created

---

## 🧩 Scenario

Acting as an **IAM user** (not root) with permission to create and assume roles, the lab goes through hands-on steps in the console and CloudShell to observe how the permissions of a temporary role behave.

| Parameter | Value |
|---|---|
| 👤 Source identity | IAM user |
| 🎭 Assumed role | Role with the `AmazonS3FullAccess` policy |
| 🤝 Who can assume it | Defined by the role's **trust policy** |
| ⏱️ Maximum session duration | **1 hour** (3600 seconds, default value) |
| 💻 Execution environment | AWS CloudShell with Python 3 and Boto3 |

---

## 🏗️ Architecture

```mermaid
flowchart LR
    U["👤 IAM User"] -->|access| C["💻 CloudShell"]
    C -->|request credentials| S["🔐 AWS STS"]
    C -->|access permitted| B["🪣 Amazon S3"]
    C -->|access denied| L["⚡ AWS Lambda"]

    subgraph AWS["☁️ AWS Cloud"]
        C
        S
        B
        L
    end
```

The IAM user opens CloudShell, which requests temporary credentials from STS. With them, **S3 access is permitted** (the role has `AmazonS3FullAccess`) and **Lambda access is denied** (the role has no permissions for that service).

---

## ✅ Prerequisites

- ☁️ Access to the AWS Management Console with the account provided by Escola da Nuvem
- 👤 IAM user (not root) allowed to create and manage roles, use CloudShell, and assume roles
- 💻 Basic familiarity with the Linux command line and the AWS CLI

> ⚠️ In the training environment, permissions are limited to what each lab requires. Errors may appear when accessing services or resources that are not enabled for the account.

---

## 📊 Result

### 🪪 Identity verified in CloudShell

Output of the `aws sts get-caller-identity` command run in CloudShell, which returns the identity active in the session (user ID, account, and ARN).

<div align="center">

![Output of aws sts get-caller-identity in CloudShell](docs/images/sts-get-caller-identity.png)

*Figure 1: `aws sts get-caller-identity` in CloudShell, showing the IAM user ARN before assuming the temporary role.*

</div>

> 💡 After the temporary credentials are configured, the same command returns an ARN in the format `assumed-role/<role-name>/<session-name>`, as described in the validation section below.

---

## 🔍 Validation

| ✔️ | Test | Expected result |
|---|---|---|
| ☑️ | Console after switching to the role | **S3** access works and other services return **access denied** |
| ☑️ | `aws sts get-caller-identity` as the original user | ARN with `user/<your-user>` |
| ☑️ | Requesting a duration above the role maximum (e.g., 7200 s) | Error, since the default limit is 3600 s |
| ☑️ | `aws s3 ls` without the *session token* | Invalid credential error |
| ☑️ | `aws s3 ls` with all three credentials configured | Lists the S3 buckets |
| ☑️ | `aws sts get-caller-identity` with the temporary credentials | ARN with `assumed-role/<role-name>/<session-name>` |
| ☑️ | `aws lambda list-functions` | `AccessDeniedException` |
| ☑️ | `aws s3 ls` after the session ends | Expired token error (`ExpiredToken`) |
| ☑️ | Re-running the script after changing the trust policy | `AccessDenied` on the `AssumeRole` operation |

---

## 💡 Key Takeaways

- ⏱️ **Credentials with an expiry date:** STS credentials expire on their own, limiting the window of use if they leak.
- 🔑 **Three pieces, not two:** the access key and secret key alone do not work; the **session token** is also required.
- 🎭 **Role permissions define the reach:** with the role assumed, S3 worked and Lambda was denied, even though the source user was the same.
- 🤝 **Trust policy ≠ permissions policy:** one defines **who can assume** the role, the other defines **what the role can do**.
- 🚫 **Changing trust blocks access:** after replacing the principal in the trust policy, the original user could no longer assume the role.
- 🔐 **Best practices:** prefer roles over long-lived keys and apply least privilege. In this lab, `AmazonS3FullAccess` was used for demonstration only.
- 🧠 **Exam relevance:** `AssumeRole`, STS, roles, and trust policies are recurring exam topics.

---

## 🧹 Resource Cleanup

At the end, every resource created was removed:

- 🗑️ **CloudShell** environment deleted
- 🗑️ Lab **IAM role** deleted
- 🧽 Temporary credentials removed or commented out in the AWS CLI configuration files

---

## ☁️ AWS Services Covered

![AWS STS](https://img.shields.io/badge/AWS-STS-DD344C?style=flat-square&logo=amazonaws&logoColor=white) ![AWS IAM](https://img.shields.io/badge/AWS-IAM-DD344C?style=flat-square&logo=amazonaws&logoColor=white) ![AWS CloudShell](https://img.shields.io/badge/AWS-CloudShell-232F3E?style=flat-square&logo=amazonaws&logoColor=white) ![Amazon S3](https://img.shields.io/badge/Amazon-S3-569A31?style=flat-square&logo=amazons3&logoColor=white) ![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-FF9900?style=flat-square&logo=awslambda&logoColor=white)

| Service | Role in the lab |
|---|---|
| 🔐 **AWS STS** | Issues the temporary credentials (access key, secret key, and *session token*) |
| 🎭 **AWS IAM** | Role creation, trust policy, and permissions policy attachment |
| 💻 **AWS CloudShell** | Terminal to run the Python script and AWS CLI commands |
| 🪣 **Amazon S3** | Service with **permitted** access for the role |
| ⚡ **AWS Lambda** | Service with **denied** access for the role |

---

## 👤 Author

**Lucas Barretto**

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0A66C2?style=flat-square&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/lucaspimentabarretto)
[![GitHub](https://img.shields.io/badge/GitHub-Follow-181717?style=flat-square&logo=github&logoColor=white)](https://github.com/lbarretto)

---

<div align="center">

☁️ Lab completed as part of the **Escola da Nuvem** program, Class **C10 OUT – DPCN 19** 🚀

</div>