## Configure inbound R53 Endpoints allowing the on-premises environment to resolve AWS

Welcome to STAGE3 of this Advanced Demo where you will gain practical experience using a Hybrid ON-PREMISES & AWS DNS Environment You will perform the following tasks:-

## STAGE 3A - INBOUND ENDPOINTS

1. Move to the Route53 Console https://console.aws.amazon.com/route53/home?region=us-east-1#
2. Click Inbound endpoints under Resolver. Click Configure Endpoints
3. Select Inbound Only
4. Click Next

5. Type A4LINBOUND under Endpoint Name
6. Choose the a4l-aws VPC in the VPC Dropdown
7. Click the Security Group Dropdown and select AWSSecurityGroup

Inbound Endpoints for R53 consist of 2 ENI's which are allocted with 1 IP address each.
Next you need to pick the subnets these will be placed in.
8. Scroll Down and find IP address #1 Click Availability Zone dropdown and select us-east-1a
9 Click the Subnet dropdown and pick sn-private-A
10. Leave the IP assignment as Use an IP address that is selected automatically

11. Scroll Down and find IP address #2 Click Availability Zone dropdown and select us-east-1b
12. Click the Subnet dropdown and pick sn-private-B
13. Leave the IP assignment as Use an IP address that is selected automatically

14. Scroll to the bottom and click Next
15. Scroll all the way to the bottom and click Submit

16. Click on Inbound endpoints under Resolver and wait for the status to be Operational

17. Click on the endpoint
18. Scroll Down
Note down the two IP addresses ... one in us-east-1a and one in us-east-1b


<img width="1382" height="463" alt="image" src="https://github.com/user-attachments/assets/576bd249-d6b9-47fa-8366-f75b47388f68" />


This is the AWS side configuration completed ... these endpoints can be accessed from outside the VPC .. provising access to any AWS R53 private hosted zones.

Next you need to configure the on-premises Self-Managed DNS to use these addresses
## STAGE 3B - CONFIGURE ON-PREMISES DNSA

Open a new tab, and load the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1
Click Running Instances
Select A4L-ONPREM-DNSA, right click, select Connect, choose session manager click Connect

Download this file https://learn-cantrill-labs.s3.amazonaws.com/aws-hybrid-dns/awszone.forward This is a template for a DNS Zone file ... you will configure each of the on-premises DNS servers to point at the AWS R53 endpoints using a customised version of this file
This file, creates a zone for aws.animals4life.org and configures the DNS server to point at the R53 inbound endpoints for those queries.

Edit that file ... and replace INBOUND_ENDPOINT_IP1 and INBOUND_ENDPOINT_IP2 with the each of the IP addresses you copied in the previous step for the the Route53 inbound endpoint.

Copy the entire updated text into your clipboard.

My file
```
zone "aws.animals4life.org" {
  type forward;
  forward only;
  forwarders { 10.16.107.8; 10.16.47.137; };
};
```

In the session manager session for A4L-ONPREM-DNSA
type cd /etc
type sudo nano /etc/named.conf press enter Scroll all the way to the bottom of that file Paste in the text you copied above Correct the formatting so lines are split by the ; use the corp.animals4life.org as a guide
Press ctrl+o to save the file
Press ctrl+x to exit the editor
Type sudo service named restart
That will load that configuration file

Full etc/named.conf

```
  GNU nano 8.3                                                                                                   /etc/named.conf
options {
  directory     "/var/named";
  dump-file     "/var/named/data/cache_dump.db";
  statistics-file "/var/named/data/named_stats.txt";
  memstatistics-file "/var/named/data/named_mem_stats.txt";
  allow-query { any; };
  allow-transfer     { localhost; 192.168.10.146; };
  recursion yes;
  forward first;
  forwarders {
    192.168.10.2;
  };
  dnssec-validation yes;
  /* Path to ISC DLV key */
  bindkeys-file "/etc/named.iscdlv.key";
  managed-keys-directory "/var/named/dynamic";
};
zone "corp.animals4life.org" IN {
    type master;
    file "corp.animals4life.org.zone";
    allow-update { none; };
};
zone "aws.animals4life.org" {
  type forward;
  forward only;
  forwarders { 10.16.107.8; 10.16.47.137; };
};
```

To test, type nslookup press enter
type server 127.0.0.1 press enter
type web.aws.animals4life.org press enter If everything is working you will get an answer .. showing an ip starting with 10.16.
Architecturally ... you have just queried the local DNS server on this instance, which is configured to use the route53 inbound endpoints
Because the integration is configured, the query works and a result is returned
## STAGE 3C - CONFIGURE ON-PREMISES DNSB

Open a new tab, and load the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1
Click Running Instances
Select A4L-ONPREM-DNSB, right click, select Connect, choose session manager click Connect

type cd /etc
type sudo nano /etc/named.conf press enter Scroll all the way to the bottom of that file Paste in the text you copied above Correct the formatting so lines are split by the ; use the corp.animals4life.org as a guide
Press ctrl+o to save the file
Press ctrl+x to exit the editor
Type sudo service named restart
That will load that configuration file

To test, type nslookup press enter
type server 127.0.0.1 press enter
type web.aws.animals4life.org press enter If everything is working you will get an answer .. showing an ip starting with 10.16.
Architecturally ... you have just queried the local DNS server on this instance, which is configured to use the route53 inbound endpoints
Because the integration is configured, the query works and a result is returned
STAGE 3D - REVIEW THE AWS DNS CONFIGURATION

Move to the AWS console Move to Route53 Click Hosted Zones Locate aws.animals4life.org and open it .. this is a private hosted zone, thats only available by default from the AWS VPC ...
By using R53 inbound endpoints, you have configured this one-way integration
STAGE 3E - Update the App server DNS

Load the EC2 Console https://console.aws.amazon.com/ec2/v2/home?region=us-east-1
Click Running Instances
Select A4L-ONPREM-DNSA and note down its PrivateIP
Select A4L-ONPREM-DNSB and note down its PrivateIP
Select A4L-ONPREM-APP, right click, select Connect, choose session manager click Connect

Type sudo nano /etc/systemd/resolved.conf press enter
Scroll to the bottom of this file
Add the following

DNS=
Domains=~.

Onto the end of DNS= add THE_PRIVATE_IP_OF_ONPREM_DNS_A and then a space and then THE_PRIVATE_IP_OF_ONPREM_DNS_B (be sure to use the actual IPs not these place holders)
Press ctrl+o to save
Press ctrl+x to exit
type sudo reboot press enter
Select A4L-ONPREM-APP, right click, select Connect, choose session manager click Connect
Type ping web.aws.animals4life.org press enter
Architecturally this is using DNSA or DNSB as a resolver ... both of which are configured to forward those queries to the R53 inbound endpoints
This is why you can now resolve AWS

## STAGE 3 - FINISH

You have configured inbound integration
The on-premises environment can now resolve the AWS DNS
Any services which are configured to user the DNSA and DNSB servers as their DNS can now resolve the AWS DNS Infrastructure
