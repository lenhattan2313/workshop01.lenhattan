---
title: "Introduction"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 1. </b> "
---

## Introduction to Creating an Application Load Balancer with Terraform in AWS

Welcome to this hands-on lab where we will walk you through the process of deploying an **Application Load Balancer** (ALB) using **Terraform** in Amazon Web Services (AWS). Load balancing is a crucial component of modern web applications, ensuring high availability and distributing incoming traffic across multiple targets such as EC2 instances, containers, or Lambda functions.

In this lab, you will know how to automate the creation of an ALB infrastructure using Terraform, an infrastructure as code tool. By leveraging Terraform, you can define your infrastructure in declarative configuration files, allowing for reproducibility, versioning, and easy management of your AWS resources.

## Objectives:

- Learn how to use Terraform to define and provision AWS infrastructure.
- Create an ALB along with its associated components such as target groups, listeners, and security groups.
- Test the ALB by routing traffic to backend services running on EC2 instances.

**Link github**: [workshop01](https://github.com/lenhattan2313/workshop01)

This is the architecture we will make in this lab:
![VPC](images/1-Introduce/architecture.png)
