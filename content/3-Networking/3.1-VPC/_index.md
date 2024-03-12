---
title: "VPC"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1 </b> "
---

Now, we will proceed to create a Virtual Private Cloud (VPC) configuration that includes 2 public subnets and an Internet Gateway. This setup will establish the foundational networking infrastructure required to enable connectivity to resources within the VPC and to the internet.

Create VPC and IGW:

```
resource "aws_vpc" "vpc" {
  cidr_block = var.vpc_cidr_block
  tags = {
    Name = "VPC HCM"
  }
}
resource "aws_internet_gateway" "igw" {
  vpc_id = aws_vpc.vpc.id
  tags = {
    Name = "IGW"
  }
}
```

In this configuration, we utilize the **`var.vpc_cidr_block`** variable to store the CIDR block value for the VPC. This approach allows for flexibility and modularity in defining the VPC's address range, facilitating easier management and customization of the VPC configuration as needed.

You can check on **`variables.tf`** file

```
variable "availability_zones" {
  default = ["ap-southeast-1a", "ap-southeast-1b"]
}

variable "public_subnet_cidr_blocks" {
  type    = list(string)
  default = ["10.10.1.0/24", "10.10.2.0/24"]
}

variable "vpc_cidr_block" {
  default = "10.10.0.0/16"
}

```

Now we will create 2 public subnets and associate with route table

```
# create 2 public subnets in ap-southeast-1a,1b
resource "aws_subnet" "public_subnet" {
  count             = length(var.public_subnet_cidr_blocks)
  vpc_id            = aws_vpc.vpc.id
  cidr_block        = var.public_subnet_cidr_blocks[count.index]
  availability_zone = var.availability_zones[count.index % length(var.availability_zones)]

  tags = {
    Name = "Public Subnet ${count.index + 1}"
  }
}

# create route table
resource "aws_route_table" "public_route_table" {
  vpc_id = aws_vpc.vpc.id
  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.igw.id
  }

  tags = {
    Name = "Public route table"
  }
}

# associate public subnet with public route table
resource "aws_route_table_association" "public_associate" {
  count          = length(aws_subnet.public_subnet)
  subnet_id      = aws_subnet.public_subnet[count.index].id
  route_table_id = aws_route_table.public_route_table.id
}

```

In this Terraform configuration snippet, **`count`** is a meta-argument used to create multiple instances of a resource based on the length of a specified list or array.

**Result on AWS console:**
![VPC](../../images/3-Networking/result.png)
