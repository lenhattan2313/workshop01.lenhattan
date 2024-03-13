---
title: "Terraform"
date: "`r Sys.Date()`"
weight: 3
chapter: false
pre: " <b> 2.3 </b> "
---

## Terraform backend

In modern cloud infrastructure management, automating the provisioning and configuration of resources is paramount for scalability, reliability, and efficiency. **Terraform**, an infrastructure as code (IaC) tool, has emerged as a leader in this domain, enabling engineers to define their infrastructure in code and provision it across various cloud providers.

When utilizing Terraform, one critical consideration is the choice of backend. The backend is responsible for storing Terraform state files, which contain the current state of the managed infrastructure. While Terraform supports various backend options, including local storage, remote storage solutions like Amazon S3, Azure Blob Storage, or HashiCorp's Terraform Cloud offer numerous advantages over local storage.

**Terraform backends** are a native Terraform feature, which saves the state file in a remote location, rather than a local file. Using a Terraform backend means we don’t have to worry about sharing the state between team members.

## Terraform Cheat Sheet

| CLI                                 | Description                                                               |
| ----------------------------------- | ------------------------------------------------------------------------- |
| terraform –version                  | Shows terraform version installed                                         |
| **terraform init**                  | Ask for input if necessary                                                |
| **terraform plan**                  | Creates an execution plan (dry run)                                       |
| terraform apply                     | Executes changes to the actual environment                                |
| **terraform apply –auto-approve**   | Apply changes without being prompted to enter ”yes”                       |
| terraform apply -var ‘foo=bar’      | Set a variable in the Terraform configuration, can be used multiple times |
| terraform apply -target             | Only apply/deploy changes to the targeted resource                        |
| **terraform destroy –auto-approve** | Destroy/cleanup without being prompted to enter ”yes”                     |
| **terraform fmt**                   | Format code as per HCL canonical standard                                 |

## Create Terraform backend

We will create S3 bucket and dynamoDB in AWS console to store LockID of this terraform.tfstate.

1.  Create S3 bucket

- Go to **S3**
- Select **Bucket**
- Click **Create Bucket**
  ![Bucket](../../images/2-Preparation/Terraform/1.png)
- Select Region: **ap-southeast-1**
- Enter bucket name: **`terraform-workshop-01`**
  ![Bucket](../../images/2-Preparation/Terraform/2.png)
- Scroll down and click **Create bucket**

2. Create dynamoDB table

- Go to **DynamoDB**
- Select **Tables** tab
- Click **Create table**
  ![Bucket](../../images/2-Preparation/Terraform/3.png)
- Enter table name: **`terraform-workshop-01-locking`**
- Enter Partition key: **`LockID`**
  ![Bucket](../../images/2-Preparation/Terraform/4.png)
- Scroll down and click **Create table**

3. Setup main.tf

- Block **provider**: This block declares the AWS provider and specifies its configuration settings.
- Using **ap-southeast-1** (Singapore Region)
- Your profile: **WorkshopUser**

```
provider "aws" {
    region  = "ap-southeast-1"
    profile = "WorkshopUser"
}
```

- **backend "s3"**: This block specifies the backend configuration to use Amazon S3 for storing Terraform state files.
- **bucket**: Name of bucket
- **key**: Specifies the path within the S3 bucket where the state file will be stored.
- **dynamodb_table**: Name of DynamoDB table

```
terraform {
  backend "s3" {
    bucket         = "terraform-workshop-01"
    key            = "tanle/terraform/remote/s3/terraform.tfstate"
    region         = "ap-southeast-1"
    dynamodb_table = "terraform-workshop-01-locking"
  }
}

```

After configuring the provider and setting up the S3 backend, we execute the **`terraform init`** command to initialize Terraform and prepare the working directory for further operations.

```
terraform init
```
