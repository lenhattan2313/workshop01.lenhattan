---
title: "EC2"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 4.1 </b> "
---

Before creating an EC2 instance, it's essential to set up a security group that defines the inbound and outbound traffic rules to control access to the instance. Specifically, enabling SSH, HTTP, and HTTPS access ensures that you can securely connect to and interact with the instance. Here's how you can set up the security group:

```
resource "aws_security_group" "public_sg" {
  name        = "public_sg"
  description = "Allow 22, http, https inbound traffic and all outbound traffic"
  vpc_id      = aws_vpc.vpc.id

  tags = {
    Name = "public_sg"
  }

  ingress {
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = 22
    to_port     = 22
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  ingress {
    from_port   = -1
    to_port     = -1
    protocol    = "icmp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

To create two EC2 instances using the count argument and ensure that a key pair named **`vpc-keypair`** ([How to create a key pairs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/create-key-pairs.html)) exists before creating the instances, along with installing Nginx on the instances to run a web server, you can use the following Terraform configuration:

```
resource "aws_instance" "public_ec2" {
  count                       = length(aws_subnet.public_subnet)
  ami                         = var.ami
  instance_type               = var.instance_type
  key_name                    = "vpc-keypair"
  subnet_id                   = aws_subnet.public_subnet[count.index].id
  vpc_security_group_ids      = [aws_security_group.public_sg.id]
  associate_public_ip_address = true # Ensure instance gets a public IP
  tags = {
    Name = "EC2_Public ${count.index + 1}"
  }
  user_data = <<-EOF
              #!/bin/bash
              sudo su
              yum update -y
              yum install -y nginx
              systemctl start nginx
              systemctl enable nginx

              chmod 2775 /usr/share/nqinx/html
              echo "<h1>Web Server - ${count.index + 1}</h1>" > /usr/share/nginx/html/index.html
              EOF

}

variable "ami" {
  default = "ami-07a6e3b1c102cdba8"

}

variable "instance_type" {
  default = "t2.micro"

}
```

We have used the **`user_data`** attribute to supply a script that installs and runs the nginx service.

**Result on AWS console:**
![EC2](../../images/4-Compute/result-ec2-1.png)
![EC2](../../images/4-Compute/result-ec2-2.png)

**Testing EC2 server**
![EC2](../../images/4-Compute/web1.png)
![EC2](../../images/4-Compute/web2.png)
