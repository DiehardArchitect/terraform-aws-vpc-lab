# Production AWS VPC — Multi-AZ Infrastructure

A production-grade AWS VPC built entirely with Terraform, 
designed with security and high availability as first-class 
requirements — not afterthoughts.

## What This Builds

### Network Architecture
- Custom VPC (10.0.0.0/16) across two Availability Zones
- Isolated public and private subnets per AZ
- Internet Gateway for public subnet egress
- NAT Gateway for private subnet outbound traffic
- Route tables scoped per subnet type

### Security Controls
- Zero open inbound ports — SSH replaced entirely with 
  AWS Systems Manager Session Manager
- DynamoDB VPC Endpoint — eliminates data traversal 
  over the public internet
- VPC Flow Logs enabled with CloudWatch integration 
  for network traffic visibility and anomaly detection
- IAM policies scoped to least-privilege — no wildcard 
  actions or resource ARNs

### Observability
- VPC Flow Logs → CloudWatch Log Group
- Network traffic baseline for anomaly detection
- Full Session Manager audit trail — every session logged

## Architecture Diagram
```
                    Internet Gateway
                          |
              ┌───────────┴───────────┐
              │                       │
         AZ-1 Public            AZ-2 Public
         Subnet                 Subnet
              │                       │
         NAT Gateway            NAT Gateway
              │                       │
         AZ-1 Private           AZ-2 Private
         Subnet                 Subnet
              │                       │
              └───────────┬───────────┘
                    DynamoDB VPC
                     Endpoint
```

## Security Design Decisions

| Decision | Reason |
|---|---|
| No SSH / port 22 | Eliminates credential-based access; Session Manager provides full audit trail |
| Private subnets for workloads | Removes direct internet exposure from application layer |
| DynamoDB VPC Endpoint | Data never traverses public internet — eliminates exfiltration vector |
| VPC Flow Logs | Provides network visibility for threat detection and incident response |
| NAT Gateway (not NAT instance) | Managed service — no patching, no single point of failure |

## Tools Used

- Terraform
- AWS CLI
- AWS Systems Manager Session Manager

## How to Deploy
```bash
terraform init
terraform plan
terraform apply
```

## Key Outcomes

- Zero open inbound ports across entire VPC
- Full network traffic visibility via CloudWatch
- Private workloads with controlled outbound access only
- Infrastructure fully reproducible via Terraform
