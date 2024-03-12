---
title: "Flow logs and CloudWatch"
date: "`r Sys.Date()`"
weight: 2
chapter: false
pre: " <b> 3.2 </b> "
---

Flow Logs provide network traffic visibility while CloudWatch offers centralized log storage and monitoring in AWS. Together, they enable proactive monitoring, troubleshooting, and security analysis of your infrastructure.

- Now we configures a Flow Log for our VPC, capturing all traffic types.
- Specifies CloudWatch Logs as the destination for Flow Logs.
- Associates an IAM role (created later) for permissions to publish logs to CloudWatch.

```
resource "aws_flow_log" "vpc_flow_log" {
  iam_role_arn         = aws_iam_role.iam_role.arn
  log_destination_type = "cloud-watch-logs"
  log_destination      = aws_cloudwatch_log_group.cloudwatch_log_group.arn
  traffic_type         = "ALL"
  vpc_id               = aws_vpc.vpc.id
}

resource "aws_cloudwatch_log_group" "cloudwatch_log_group" {
  name              = "VPC-FlowLogs-Group"
  retention_in_days = 30
}
```

- The data block in Terraform is used to fetch and reference data from various sources that are external to your infrastructure. Data blocks below are used to retrieve IAM policy documents, which are then used to define IAM policies for the IAM role associated with Flow Logs.

```
data "aws_iam_policy_document" "assume_role" {
  statement {
    effect = "Allow"

    principals {
      type        = "Service"
      identifiers = ["vpc-flow-logs.amazonaws.com"]
    }

    actions = ["sts:AssumeRole"]
  }
}

data "aws_iam_policy_document" "policy_document" {
  statement {
    effect = "Allow"

    actions = [
      "logs:CreateLogGroup",
      "logs:CreateLogStream",
      "logs:PutLogEvents",
      "logs:DescribeLogGroups",
      "logs:DescribeLogStreams",
    ]

    resources = ["*"]
  }
}
```

- Defines an IAM role with a policy allowing the Flow Logs service to assume this role.

```
resource "aws_iam_role" "iam_role" {
  name               = "iam_role_flow_log"
  assume_role_policy = data.aws_iam_policy_document.assume_role.json
}

resource "aws_iam_role_policy" "iam_policy" {
  name   = "iam_policy_flow_log"
  role   = aws_iam_role.iam_role.id
  policy = data.aws_iam_policy_document.policy_document.json
}
```

**Result on AWS console:**
![CloudWatch](../../images/3-Networking/result-cloudwatch.png)
