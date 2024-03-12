---
title: "Route 53"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 5.1 </b> "
---

**Route 53** is AWS highly scalable and reliable Domain Name System (DNS) web service. It enables you to register domain names, route traffic to resources globally, and manage the DNS records for your domain.Here are some key features and functionalities of Route 53: Domain Registration, DNS Management, Traffic Routing, Health Checks, Integration with AWS Services

Whether registering a domain name with Route 53 or any other registrar, such as NameCheap, I currently use a domain from [NameCheap](https://www.namecheap.com/) . With this setup, I can connect the domain to Route 53 by configuring the nameservers.

Sets up a Route 53 hosted zone for the domain **yourdomain.com** in this case I am using (lenhattan.com) and creates a Route 53 record to route traffic to an **ALB**

```
resource "aws_route53_zone" "main" {
  name = "lenhattan.com"
}

resource "aws_route53_record" "alb" {
  zone_id = aws_route53_zone.main.zone_id
  name    = "lenhattan.com"
  type    = "A"

  alias {
    name                   = aws_lb.alb.dns_name
    zone_id                = aws_lb.alb.zone_id
    evaluate_target_health = false
  }
}
```

**Result on AWS console:**
![Route](../../images/5-Route53/result-53.png)
We will copy the **`nameserver`** values from the hosted zone and paste them into the domain registrar where the domain name is registered. I will paste them into my **Namecheap** domain settings
![Route](../../images/5-Route53/result-domain.png)

And then, we'll wait for a few minutes to allow **AWS Certificate Manager** (ACM) to complete the DNS validation process in next section.
