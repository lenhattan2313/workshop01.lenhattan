---
title: "Application Load Balancer"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 4.2 </b> "
---

An **Application Load Balancer** (ALB) in AWS is a powerful networking solution that efficiently distributes incoming application traffic across multiple targets, such as EC2 instances, containers, or IP addresses, within one or more availability zones. ALBs operate at the application layer (Layer 7) of the OSI model, enabling them to route requests based on content such as HTTP headers, cookies, or paths, thus allowing for more sophisticated routing decisions compared to traditional load balancers. With features like content-based routing, TLS termination, and native integration with other AWS services like Auto Scaling and AWS Certificate Manager, ALBs provide enhanced scalability, reliability, and security for modern web applications deployed on AWS infrastructure.

- We also create **security groups** for ALB

```
resource "aws_security_group" "alb_sg" {
  name        = "alb_sg"
  description = "Allow http, https inbound traffic and all outbound traffic"
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
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

**Target group** route incoming traffic to EC2 instances based on the defined criteria (port 80, HTTP protocol). The attachments ensure that the EC2 instances are registered with the target group, allowing the ALB to distribute traffic to them effectively.

Sets up **Target group** and attaches **EC2** instance to it

```
resource "aws_lb_target_group" "alb_target_group" {
  name        = "alb-target-group"
  target_type = "instance"
  port        = 80
  protocol    = "HTTP"
  vpc_id      = aws_vpc.vpc.id
}

resource "aws_lb_target_group_attachment" "alb_target_group_attachment" {
  count            = length(aws_instance.public_ec2)
  target_group_arn = aws_lb_target_group.alb_target_group.arn
  target_id        = aws_instance.public_ec2[count.index].id
}
```

**Result on AWS console:**
![ALB](../../images/4-Compute/result-tg.png)

Sets up an **ALB** along with its associated listeners for handling HTTP and HTTPS traffic. The HTTPS listener ensures secure communication with clients using SSL/TLS encryption, while the HTTP listener redirects requests to HTTPS for improved security.

```
resource "aws_lb" "alb" {
  name               = "alb"
  internal           = false
  load_balancer_type = "application"
  security_groups    = [aws_security_group.alb_sg.id]
  subnets            = [for subnet in aws_subnet.public_subnet : subnet.id]

  tags = {
    Environment = "production"
  }
}

resource "aws_lb_listener" "alb_listener" {
  load_balancer_arn = aws_lb.alb.arn
  port              = "443"
  protocol          = "HTTPS"
  ssl_policy        = "ELBSecurityPolicy-2016-08"
  certificate_arn   = aws_acm_certificate_validation.cert_validate.certificate_arn

  default_action {
    type             = "forward"
    target_group_arn = aws_lb_target_group.alb_target_group.arn
  }
}
resource "aws_lb_listener" "alb_listener_http" {
  load_balancer_arn = aws_lb.alb.arn
  port              = "80"
  protocol          = "HTTP"

  default_action {
    type = "redirect"
    redirect {
      port        = "443"
      protocol    = "HTTPS"
      status_code = "HTTP_301"
    }
  }
}
```

**Result on AWS console:**
![ALB](../../images/4-Compute/result-alb.png)
