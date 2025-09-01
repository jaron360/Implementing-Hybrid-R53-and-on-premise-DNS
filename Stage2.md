## STAGE 2A - CREATE A VPC PEER

1. Move to the VPC console and then Peering Connections https://console.aws.amazon.com/vpc/home?region=us-east-1#PeeringConnections:sort=vpcPeeringConnectionId
2. Click Create Peering Connection For Peering Connection name Tag enter AWS-ONPREM
3. Click VPC (Requester) dropdown and select a4l-aws
4. Scroll down Click VPC (Accepter) select a4l-onprem
5. Click Create Peering Connection

This creates the peering connection & logical gateway object between the two VPCs 

## STAGE 2B - ACCEPT THE VPC PEER

Now you need to accept the VPC Peering connection - creating a VPC peer is an INVITE and ACCEPT architecture
1. Select the PEERING CONNECTION which shows as Pending Acceptance
2. Click the Actions Dropdown and select Accept Request
3. Click Yes, Accept
4. Click Close

Thats the peering connection created between the AWS and Simulated ON-PREMISES VPC


<img width="1565" height="435" alt="VPC peering connection" src="https://github.com/user-attachments/assets/9b97136b-d828-4bfd-abe4-aed4409e06a7" />

## STAGE 2B - ADD ROUTING - AWS

1. Move to the Route Tables area of the VPC Console https://console.aws.amazon.com/vpc/home?region=us-east-1#RouteTables:sort=tag:Name
2. Select the A4L-AWS-RT , click Routes Tab Click Edit Routes Click Add Route You're adding a route for the simulated on-premises environment
3. Type 192.168.10.0/24 into the destination (on-premises CIDR) Click the Target Dropdown, Select Peering Connection and select AWS-ONPREM
4. Click Save Routes
5. Click Close

<img width="1179" height="244" alt="Add peering in RTB aws" src="https://github.com/user-attachments/assets/3b0f81c2-3752-4d5b-b5c6-843f36df9cfa" />


Thats the route FROM AWS TO ON-PREMISES
## STAGE 2C - ADD ROUTING - ONPREM

1. Move to the Route Tables area of the VPC Console https://console.aws.amazon.com/vpc/home?region=us-east-1#RouteTables:sort=tag:Name
2. Select the A4L-ONPREM-RT , click Routes Tab
3. Click Edit Routes
4. Click Add Route You're adding a route for the AWS environment
5. Type 10.16.0.0/16 into the destination (AWS VPC CIDR)
6. Click the Target Dropdown, Select Peering Connection and select AWS-ONPREM
7. Click Save Routes
8. Click Close

<img width="910" height="301" alt="ON prem RTB" src="https://github.com/user-attachments/assets/960eb53e-2468-4b7d-8ee8-87adc125d8aa" />
 
## STAGE 2D - TEST
1. Move to the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1
2. Click Running Instances You will need the IP address of one side of the peering relationship
3. Select A4L-ONPREM-APP copy down the Private IP for this instance
4. Select the A4L-AWS-EC2-A, right click, Click connect, choose Session Manager , Click Connect
5. Type ping IPYOUJUSTCOPIED
6. This verifies that the AWS environment can ping a resource in the on-premises environment
7. But DNS still doesn't work. If you type ping app.corp.animals4life.org you should recieve the message ping: app.corp.animals4life.org: Name or Service not known. This is expected as there is no DNS integration yet.

<img width="562" height="240" alt="ping success from on prem to aws" src="https://github.com/user-attachments/assets/a1594adf-6897-49ba-a723-d8035c4fe841" />


## STAGE 2 - FINISH
This is the end of STAGE2 of this advanced demo. You have created a VPC Peering connection between AWS and ON-PREMISES which provides IP connectivity between the two environments.
