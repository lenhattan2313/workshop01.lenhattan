---
title: "AWS CLI"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 2.2 </b> "
---

## Authenticate with IAM user credentials

We will configure basic settings that the AWS CLI uses to interact with AWS, we create a new profile **WorkshopUser** by **Terminal** and use that profile to handle create resources in **Terraform**.

We can check how many profiles we have:

```
$ aws configure list-profiles
```

Now create **WorkshopUser** profile:

```
$ aws configure --profile WorkshopUser

AWS Access Key ID [None]: **************UHKK
AWS Secret Access Key [None]: *************io+dFg5
Default region name [None]: ap-southeast-1
Default output format [None]:
```

Check **WorkshopUser** profile

```
$ aws sts get-caller-identity --profile WorkshopUser

{
    "UserId": "AIDAQPRGOCWCFBU7GPMIE",
    "Account": "YOUR_ACCOUNT_ID",
    "Arn": "arn:aws:iam::YOUR_ACCOUNT_ID:user/WorkshopUser"
}
```
