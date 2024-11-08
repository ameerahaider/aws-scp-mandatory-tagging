# AWS Service Control Policy (SCP) - Enforcing Mandatory Tagging on Resource Creation

## Overview
This **Service Control Policy (SCP)** is designed for use in an **AWS Organization** to enforce consistent tagging of AWS resources during their creation. The policy **denies the creation of key resources** (such as VPCs, Subnets, Security Groups, EC2 Instances, and S3 Buckets) if they are not tagged with the required tag key `Environment`. This helps ensure that all resources are properly classified and managed, improving cost allocation, security, and resource organization.

---

## Policy Details

### Policy Name
`EnforceMandatoryEnvironmentTagOnResourceCreation`

### Description
This SCP restricts users from creating certain AWS resources unless they include a specific tag:  
**Tag Key**: `Environment`  
**Tag Value**: Any value is acceptable, but the key `Environment` must be present.

### Applicable Resources and Services
The following resources and services are restricted by this policy:

1. **VPC (Virtual Private Cloud)**
   - Action: `ec2:CreateVpc`
2. **Subnets**
   - Action: `ec2:CreateSubnet`
3. **Route Tables**
   - Action: `ec2:CreateRouteTable`
4. **Internet Gateways**
   - Action: `ec2:CreateInternetGateway`
5. **NAT Gateways**
   - Action: `ec2:CreateNatGateway`
6. **Security Groups**
   - Action: `ec2:CreateSecurityGroup`
7. **Elastic Load Balancers (ELB)**
   - Action: `elasticloadbalancing:CreateLoadBalancer`
8. **ELB Target Groups**
   - Action: `elasticloadbalancing:CreateTargetGroup`
9. **EC2 Instances**
   - Action: `ec2:RunInstances`
10. **S3 Buckets**
    - Action: `s3:CreateBucket`

### Policy Behavior
The policy denies the creation of the above resources if the required `Environment` tag is **not specified** at the time of creation.

---

## SCP JSON Definition

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "DenyUnTaggedVPC",
      "Effect": "Deny",
      "Action": ["ec2:CreateVpc"],
      "Resource": ["arn:aws:ec2:*:*:vpc/*"],
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true"
        }
      }
    },
    {
      "Sid": "DenyUnTaggedSubnet",
      "Effect": "Deny",
      "Action": ["ec2:CreateSubnet"],
      "Resource": ["arn:aws:ec2:*:*:subnet/*"],
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true"
        }
      }
    },
    {
      "Sid": "DenyUnTaggedRouteTable",
      "Effect": "Deny",
      "Action": ["ec2:CreateRouteTable"],
      "Resource": ["arn:aws:ec2:*:*:route-table/*"],
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true"
        }
      }
    },
    {
      "Sid": "DenyUnTaggedLoadBalancers",
      "Effect": "Deny",
      "Action": ["elasticloadbalancing:CreateLoadBalancer"],
      "Resource": ["arn:aws:elasticloadbalancing:*:*:loadbalancer/*"],
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true"
        }
      }
    },
    {
      "Sid": "DenyUnTaggedS3",
      "Effect": "Deny",
      "Action": ["s3:CreateBucket"],
      "Resource": ["arn:aws:s3:::*"],
      "Condition": {
        "Null": {
          "aws:RequestTag/Environment": "true"
        }
      }
    }
  ]
}
```

---

## How to Apply This SCP

1. Navigate to the **AWS Organizations** console.
2. Select the **Policies** section.
3. Create a new **Service Control Policy (SCP)**.
4. Copy and paste the JSON policy above into the SCP editor.
5. Attach this SCP to the root, organizational units (OUs), or specific accounts where you want to enforce mandatory tagging.

---

## Extending the Policy

### Adding New Resources
To enforce tagging on additional resources, add new `Statement` blocks.

#### Example: Enforce `Environment` Tag on SQS Queues
```json
{
  "Sid": "DenyUnTaggedSQSQueue",
  "Effect": "Deny",
  "Action": "sqs:CreateQueue",
  "Resource": "arn:aws:sqs:*:*:*",
  "Condition": {
    "Null": {
      "aws:RequestTag/Environment": "true"
    }
  }
}
```

### Enforcing Multiple Tags
To enforce multiple tags, adjust the `Condition` block:

```json
"Condition": {
  "Null": {
    "aws:RequestTag/Environment": "true",
    "aws:RequestTag/Project": "true"
  }
}
```
### Changing the Tag Key
To enforce a different tag, modify the key in the `Condition`:

```json
"Condition": {
  "Null": {
    "aws:RequestTag/CostCenter": "true"
  }
}
```

### Using Wildcards for More Flexibility
To enforce tagging on all EC2 actions:

```json
{
  "Sid": "DenyUnTaggedEC2Resources",
  "Effect": "Deny",
  "Action": "ec2:Create*",
  "Resource": "arn:aws:ec2:*:*:*",
  "Condition": {
    "Null": {
      "aws:RequestTag/Environment": "true"
    }
  }
}
```

---

## Benefits of Enforcing Tagging
- **Cost Management**: Simplifies cost allocation by categorizing resources.
- **Governance**: Enhances visibility and control over AWS resources.
- **Security**: Helps enforce security policies by identifying resources based on tags.
- **Automation**: Facilitates automation workflows by using tags as filters.

---
