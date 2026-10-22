# AWS VPC Peering: Private Connectivity Between Two VPCs

**Project type:** AWS networking / cloud infrastructure  
**Services:** Amazon VPC, VPC Peering, EC2, Route Tables, Internet Gateway, Security Groups  
**Status:** Project blueprint and documentation prepared; AWS deployment and test evidence must be completed in your own AWS account.

## Overview
This project demonstrates private IPv4 connectivity between two non-overlapping Amazon VPCs using a VPC peering connection. A public bastion host in VPC A provides administrative SSH access to two private EC2 application instances. The private instances do not receive public IPv4 addresses.

## Architecture and IP plan

| Resource | CIDR / private IP |
|---|---|
| VPC A (`ewb-vpc-a`) | `10.10.0.0/16` |
| Public subnet A (`ewb-a-public`) | `10.10.1.0/24` |
| Private subnet A (`ewb-a-private`) | `10.10.2.0/24` |
| Bastion (`ewb-bastion`) | `10.10.1.10` + public IPv4 |
| App-A (`ewb-app-a`) | `10.10.2.10`, no public IPv4 |
| VPC B (`ewb-vpc-b`) | `10.20.0.0/16` |
| Private subnet B (`ewb-b-private`) | `10.20.1.0/24` |
| App-B (`ewb-app-b`) | `10.20.1.10`, no public IPv4 |

## Route table requirements

- `ewb-rt-a-public`: local route, `0.0.0.0/0` to the VPC A Internet Gateway, and `10.20.0.0/16` to the peering connection.
- `ewb-rt-a-private`: local route and `10.20.0.0/16` to the peering connection.
- `ewb-rt-b-private`: local route and `10.10.0.0/16` to the peering connection.

Use the actual peering connection ID (`pcx-...`) as the route target. The two VPC CIDR ranges must not overlap.

## Security notes
- Allow bastion SSH (TCP 22) only from your current public IPv4 `/32`.
- Allow SSH to the private instances only from the bastion private IP `10.10.1.10/32`.
- Allow ICMP Echo Request between the specific private instance IPs for the connectivity test.
- Do not expose SSH or ICMP to `0.0.0.0/0`.
- Never commit a `.pem` private key, credentials, access tokens, or account identifiers to GitHub.

## Validation plan
Run and capture your own output for these checks:

1. Before peering/routes: from App-A, `ping -c 4 10.20.1.10` should fail.
2. After peering and both-side routes: from App-A, `ping -c 4 10.20.1.10` should succeed.
3. Reverse path: from App-B, `ping -c 4 10.10.2.10` should succeed.
4. From your laptop, `ssh ewb-app-b` should connect through the bastion using SSH `ProxyJump`.
5. Confirm App-A and App-B have no public IPv4 addresses.
6. Remove only App-B's inbound ICMP rule from App-A, repeat the ping to show controlled failure, restore the rule, and verify recovery.

**Important:** These are expected acceptance criteria, not claimed results. Add screenshots and actual output only after you run the tests.

## Deployment outline
1. Create both VPCs and the subnets listed above.
2. Attach an Internet Gateway to VPC A and configure the public route table.
3. Create private route tables and explicitly associate each subnet.
4. Create security groups with the restricted inbound rules above.
5. Launch three Amazon Linux 2023 EC2 instances; only the bastion receives a public IPv4 address.
6. Create and accept the VPC peering request.
7. Add the peering routes to all three route tables as listed above.
8. Configure local SSH `ProxyJump`, run the tests, and save evidence.
9. Complete cleanup to avoid ongoing AWS charges.

## Cost and cleanup
AWS resources can incur charges depending on region, instance configuration, public IPv4 usage, EBS, and account credits. Check current AWS pricing before launching. When finished, save your evidence, terminate the three EC2 instances, remove lab volumes and any Elastic IPs, delete the peering connection, then remove routes, security groups, subnets, route tables, Internet Gateway, and both VPCs.

## Evidence checklist
- [ ] Architecture and resource inventory with actual IDs
- [ ] Route tables and subnet associations
- [ ] Active peering connection
- [ ] Restricted security-group inbound rules
- [ ] Baseline ping failure
- [ ] Forward and reverse ping success
- [ ] SSH to App-B through bastion
- [ ] Controlled failure and recovery
- [ ] Cleanup evidence

## Suggested resume entry
**AWS VPC Peering – Private Network Connectivity (Hands-on Project)**  
Designed a two-VPC AWS network with non-overlapping CIDRs, configured VPC peering and bidirectional routes, and restricted administrative access using security groups and a bastion host. Validated private connectivity with ICMP and SSH tests.  
*Use this entry only after completing and validating the implementation yourself.*

## Project report
See [`AWS_VPC_Peering_Project_Report.pdf`](AWS_VPC_Peering_Project_Report.pdf).

## License
For learning and portfolio demonstration. Adapt the project and document your own implementation and results.
