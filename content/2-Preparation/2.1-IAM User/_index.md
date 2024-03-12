---
title: "IAM User"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 2.1 </b> "
---

## Create IAM user

You will create IAM User for this workshop through AWS console home page.

1. Go to **Users** section in IAM

   - Click **Create user**
     ![User](../../images/2-Preparation/1.png)
   - User name: **`WorkshopUser`**
   - Select **Provide user access to the AWS Management Console**
   - Select **I want to create an IAM user**
   - Select **Custom password**
   - Password: Enter your password
   - Deselect **Users must create a new password at next sign-in**
   - Click **Next**
     ![User](../../images/2-Preparation/2.png)
     ![User](../../images/2-Preparation/3.png)
   - Select **Attach policies directly**
     ![User](../../images/2-Preparation/4.1.png)

Now we will provide some permissions for this IAM user:

- **`AmazonEC2FullAccess`**: Handle with EC2
- **`AmazonRoute53FullAccess`**: Handle with Route53
- **`AWSCertificateManagerFullAccess`**: Handle with ACM
- **`CloudWatchLogsFullAccess`**: Handle with Cloudwatch
- **`IAMFullAccess`**: Provide permissions when creating VPC flowLog

Check permissions and Click **Create user**
![User](../../images/2-Preparation/4.2.png)

2.  Create **Access key**
    ![User](../../images/2-Preparation/4.png)
    ![User](../../images/2-Preparation/5.png)
    ![User](../../images/2-Preparation/6.png)
    ![User](../../images/2-Preparation/7.png)
    ![User](../../images/2-Preparation/8.png)
    After we created access key, we will **Download** .csv file and **Done**
    ![User](../../images/2-Preparation/9.png)
