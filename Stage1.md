# Advanced Hybrid DNS Demo
Welcome to STAGE1 of this Advanced Demo where you will gain practical experience using a Hybrid ONPREMISES & AWS DNS Environment You will perform the following tasks:-

- Provision the environments <== THIS STAGE
- Verify no IP & DNS Connectivity <== THIS STAGE
- Configure a VPC Peer between the environments
- Configure inbound R53 Endpoints allowing the on-premises environment to resolve AWS
- Configure outbound R53 Endpoints allowing the AWS environment to resolve on-premises

## STAGE 1A - Login to an AWS Account

Login to an AWS account and select the N. Virginia // us-east-1 region
## STAGE 1B - APPLY CloudFormation (CFN) Stack

Click https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/quickcreate?templateURL=https://learn-cantrill-labs.s3.amazonaws.com/aws-hybrid-dns/HybridDNS.yaml&stackName=HYBRIDDNS

Check the box for capabilities: [AWS::IAM::Role] Click Create Stack

The stack will take 5-10 minutes to apply and will need to be in a CREATE_COMPLETE state before you can continue.

<img width="844" height="383" alt="image" src="https://github.com/user-attachments/assets/4264c66c-8b2a-4136-8638-7373ee1e2089" />

STAGE 1C - VERIFY THERE IS NO CONNECTIVITY BETWEEN ENVIRONMENTS

Move to the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1
Click Running Instances
Select A4L-AWS-EC2-A right click and connect, select session manager click connect

1. type ping app.corp.animals4life.org
2. Verify that you get a message ping: app.corp.animals4life.org: Name or Service not known
3. This proves that the AWS environment cannot resolve any DNS from the on-premises (corp) environment

<img width="490" height="81" alt="image" src="https://github.com/user-attachments/assets/dab97423-94f2-4d03-9e28-b349b0c8ab18" />

5. Move back to the EC2 Console
6. Select A4L-ONPREM-APP and copy its Private IP into your clipboard
7. Move back to the session manager connection to A4L-AWS-EC2-A
8. Type ping THEIPADDRESSYOUJUSTCOPIED


<img width="523" height="69" alt="image" src="https://github.com/user-attachments/assets/19623579-587e-44a4-aa1f-643c4d9569e2" />

10. Verify you DON'T Receive a ping response ... proving that there is no networking connectivity between the AWS and ON-PREMISES Environments
11. Close down the session manager tab to A4L-AWS-EC2-A
12. Select A4L-ONPREM-APP right click, select connect, choose session manager, click connect Type ping web.aws.animals4life.org
13. Verify you get the error ping: web.aws.animals4life.org: Name or Service not known
14. This proves that there is no resolution from the on-premises environment to AWS
15. Move back to the EC2 Console
16. Select A4L-AWS-EC2-A and copy its Private IP into your clipboard
17. Move back to the session manager connection to A4L-ONPREM-APP
18. Type ping THEIPADDRESSYOUJUSTCOPIED
19. Verify you DON'T Receive a ping response ... proving that there is no networking connectivity between the ON-PREMISES and AWS Environments

## STAGE 1 - FINISH
This is the end of STAGE1 of this advanced demo. You have created the AWS and ON-PREMISES environment and verified that no DNS or IP connectivity exist between the two environments.
