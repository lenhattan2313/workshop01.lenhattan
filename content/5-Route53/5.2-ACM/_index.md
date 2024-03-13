---
title: "AWS Certificate Manager"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 5.2 </b> "
---

**AWS Certificate Manager** (ACM) is a service provided by AWS that simplifies the process of provisioning, managing, and deploying SSL/TLS certificates for use with AWS services and your internal resources. Here are some key aspects of ACM:

- Certificate Provisioning
- Integration with AWS Services
- Automatic Certificate Renewal
- Managed Certificate Lifecycle
- Security and Compliance

Sets up an SSL/TLS certificate for the domain "lenhattan.com" and performs DNS validation to verify ownership of the domain

```
resource "aws_acm_certificate" "cert" {
  domain_name       = "lenhattan.com"
  validation_method = "DNS"

  lifecycle {
    create_before_destroy = true
  }
}

resource "aws_route53_record" "records" {
  for_each = {
    for dvo in aws_acm_certificate.cert.domain_validation_options : dvo.domain_name => {
      name   = dvo.resource_record_name
      record = dvo.resource_record_value
      type   = dvo.resource_record_type
    }
  }
  allow_overwrite = true
  name            = each.value.name
  records         = [each.value.record]
  ttl             = 300
  type            = each.value.type
  zone_id         = aws_route53_zone.main.zone_id
}

resource "aws_acm_certificate_validation" "cert_validate" {
  certificate_arn         = aws_acm_certificate.cert.arn
  validation_record_fqdns = [for record in aws_route53_record.records : record.fqdn]
}
```

Validates the ACM certificate using DNS validation.

**certificate_arn** specifies the ARN (Amazon Resource Name) of the ACM certificate to be validated.

**validation_record_fqdns** lists the fully qualified domain names (FQDNs) of the Route 53 records created for DNS validation.

**Result on AWS console:**
![ACM](../../images/5-Route53/result-acm.png)

We will check this record in **ACM** have the same value in **Route53**
![ACM](../../images/5-Route53/result-record-acm.png)
![ACM](../../images/5-Route53/result-record-check.png)

After configuring all of resources, we execute the **`terraform apply --auto-approve`** command to automatically create the specified resources on AWS without requiring manual confirmation at each step

## RESULT

![RESULT](../../images/5-Route53/result1.png)
![RESULT](../../images/5-Route53/result2.png)
