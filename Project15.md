# Documentation for Project 15: AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY

## SET UP A VIRTUAL PRIVATE NETWORK (VPC)

- Create a VPC:

[Create VPC](./images/create-vpc.PNG)

- Enable HostNames

- Create an Internet Gateway and attach it to the VPC:

[Create Internet Gateway](./images/create-internetgateW.PNG)

[Attach Internet Gateway and VPC](./images/attach-internetGW-vpc.PNG)

- Create subnets as shown in the architecture:

- Create 2 public subnets:

[Create Public Subnet](./images/create-public-subnet.PNG)

- Create 4 private subnets:

[Create Private Subnet](./images/create-private-subnet.PNG)

- Create a route table and associate it with public subnets:

[Create Public Route Table](./images/create-public-routeT.PNG)

- Create a route table and associate it with private subnets

[Create Private Route Table](./images/create-private-routeT.PNG)

- To associate Route Tables to subnets:

- Click on the Route Table

- Click on subnet associations

Attach the subnet and save the associations

[Connect Route Table and Subnets](./images/subnet-routeT-associate.PNG)

- Edit the Routes Tables:

[Edit Public Route Table](./images/edit-public-routeT.PNG)

[Public Route Table Output](./images/public-routeT-success.PNG)

- Create Elastic IP:

[Public Route Table Output](./images/elastic-ip-allocate.PNG)

- Create NAT Gateway: (should be create in a public subnet)

[Nat Gateway Configuration](./images/natgateW-configure.PNG)

[Nat Gateway Output](./images/natgateW-success.PNG)

[Edit Private Route Table](./images/edit-private-routeT.PNG)

[Private Route Table Output](./images/private-routeT-success.PNG)

- Create a Security Group for:

- Nginx Servers: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder.

- Bastion Servers: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers. Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type curl www.canhazip.com
Application Load Balancer: ALB will be available from the Internet.

- Webservers: Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.

- Data Layer: Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have access to EFS Mountpoint.

[Security Group Configuration](./images/create-securitygrp-cfg.PNG)

[Security Group Output](./images/securgrp-success.PNG)

[Bastion Security Group Configuration](./images/create-securitygrpbastion-cfg.PNG)

[Bastion Security Group Output](./images/securgrpbastion-success.PNG)

[Nginx Reverse Proxy Security Group Configuration](./images/securgrpnginx-reverseP-configure.PNG)

[Nginx Reverse Proxy Security Group Output](./images/securgrpnginx-reverseP-success.PNG)

[Internal ALB Security Group Configuration](./images/securgrpinalb-configure.PNG)

[Internal ALB Security Group Output](./images/securgrpinalb-success.PNG)

[Webserver Security Group Configuration](./images/securgrpwebS-configure.PNG)

[Webserver Security Group Output](./images/securgrpwebS-success.PNG)

[Datalayer Security Group Configuration](./images/securgrpdatalyer-configure.PNG)

[Datalayer Security Group Output](./images/securgrpdatalyer-success.PNG)

[AWS Certificate Output](./images/aws-cert-create.PNG)

- Create Elastic File System:

[Elastic File System Create](./images/create-elas-filesys.PNG)

[Elastic File System Output](./images/create-elas-success.PNG)

- Create Access Point for wordpress:

[WordPress Access Point 1](./images/wordpress-ap-1.PNG)

[WordPress Access Point 2](./images/wordpress-ap-2.PNG)

[WordPress Access Success](./images/wordpress-ap-success.PNG)

[Tooling Access Point 1](./images/tooling-ap-1.PNG)

[Tooling Access Point 2](./images/tooling-ap-2.PNG)

[Tooling Access Success](./images/tooling-ap-success.PNG)

- Create a KMS Key:

[KMS Key](./images/rds-key-users.PNG)

[KMS Key](./images/review-key-configure.PNG)

[KMS Key](./images/kms-success.PNG)

- Create subnet group:

[Rds Subnet Creation](./images/rds-subnet-config.PNG)

[Rds Subnet Output](./images/rds-subnet-success.PNG)

- Create DB:

[Create DB Configure](./images/create-rds-1.PNG)

[Create DB Configure](./images/create-rds-2.PNG)

[Create DB Configure](./images/create-rds-3.PNG)

[Create DB Configure](./images/create-rds-4.PNG)

[Create DB Configure](./images/create-rds-5.PNG)

[Create DB Configure](./images/create-rds-6.PNG)

[Create DB Configure](./images/create-rds-7.PNG)

[Create DB Output](./images/create-rds-success.PNG)

Set Up Compute Resources for Bastion:

Provision the EC2 Instances for Bastion
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ where you created Nginx server
Ensure that it has the following software installed

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
Associate an Elastic IP with each of the Bastion EC2 Instances

Create an AMI out of the EC2 instance

Prepare Launch Template For Bastion (One per subnet)

Make use of the AMI to set up a launch template

Ensure the Instances are launched into a public subnet

Assign appropriate security group

Configure Userdata to update yum package repository and install Ansible and git

Configure Target Groups

Select Instances as the target type

Ensure the protocol is TCP on port 22

Register Bastion Instances as targets

Ensure that health check passes for the target group

Configure Autoscaling For Bastion

Select the right launch template

Select the VPC

Select both public subnets

Enable Application Load Balancer for the AutoScalingGroup (ASG)

Select the target group you created before

Ensure that you have health checks for both EC2 and ALB

The desired capacity is 2

Minimum capacity is 2

Maximum capacity is 4

Set scale out if CPU utilization reaches 90%

Ensure there is an SNS topic to send scaling notifications

- Change to super user

`sudo su -`

Run the commands below:

`yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`yum install wget vim python3 telnet htop git mysql net-tools chrony -y`

`systemctl start chronyd`

`systemctl enable chronyd`

Set Up Compute Resources for Nginx:

Provision EC2 Instances for Nginx
Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region (it is recommended to use the Region that is closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)

Ensure that it has the following software installed:

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
Create an AMI out of the EC2 instance

Prepare Launch Template For Nginx (One Per Subnet)

Make use of the AMI to set up a launch template

Ensure the Instances are launched into a public subnet

Assign appropriate security group

Configure Userdata to update yum package repository and install nginx

Configure Target Groups

Select Instances as the target type

Ensure the protocol HTTPS on secure TLS port 443

Ensure that the health check path is /healthstatus

Register Nginx Instances as targets

Ensure that health check passes for the target group

Configure Autoscaling For Nginx

Select the right launch template

Select the VPC

Select both public subnets

Enable Application Load Balancer for the AutoScalingGroup (ASG)

Select the target group you created before

Ensure that you have health checks for both EC2 and ALB

The desired capacity is 2

Minimum capacity is 2

Maximum capacity is 4

Set scale out if CPU utilization reaches 90%

Ensure there is an SNS topic to send scaling notifications

- Change to super user

`sudo su -`

Run the commands below:

`yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`yum install wget vim python3 telnet htop git mysql net-tools chrony -y`

`systemctl start chronyd`

`systemctl enable chronyd`

configure selinux policies for the webservers and nginx servers:

`setsebool -P httpd_can_network_connect=1`

`setsebool -P httpd_can_network_connect_db=1`

`setsebool -P httpd_execmem=1`

`setsebool -P httpd_use_nfs 1`

- this section will instll amazon efs utils for mounting the target on the Elastic file system:

`git clone https://github.com/aws/efs-utils`

`cd efs-utils`

`yum install -y make`

`yum install -y rpm-build`

`make rpm`

`yum install -y  ./build/amazon-efs-utils*rpm`

- Setting up self-signed certificate for the nginx instance:

`cd`

`sudo mkdir /etc/ssl/private`

`sudo chmod 700 /etc/ssl/private`

`openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ACS.key -out /etc/ssl/certs/ACS.crt`

[Openssl Configure](./images/openssl-output.PNG)

`sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048`

[Openssl Dhparam](./images/openssl-dhparam.PNG)

- Cert check

`ls -l /etc/ssl/certs/`

[Cert Check](./images/cert-check.PNG)

`ls -l /etc/ssl/private/`

[Private Cert Check](./images/private-cert-check.PNG)

Set Up Compute Resources for Webserver:

Provision the EC2 Instances for Webservers

Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

Ensure that it has the following software installed

python
ntp
net-tools
vim
wget
telnet
epel-release
htop
php
Create an AMI out of the EC2 instance

- Change to super user

`yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`yum install wget vim python3 telnet htop git mysql net-tools chrony -y`

`systemctl start chronyd`

`systemctl enable chronyd`

configure selinux policies for the webservers and nginx servers:

`setsebool -P httpd_can_network_connect=1`

`setsebool -P httpd_can_network_connect_db=1`

`setsebool -P httpd_execmem=1`

`setsebool -P httpd_use_nfs 1`

[Setsebool Success](./images/setsebool-output.PNG)

- this section will instll amazon efs utils for mounting the target on the Elastic file system:

`git clone https://github.com/aws/efs-utils`

`cd efs-utils`

`yum install -y make`

`yum install -y rpm-build`

`make rpm`

`yum install -y  ./build/amazon-efs-utils*rpm`

- Setting up self-signed certificate for the apache webserver instance:

`yum install -y mod_ssl`

`openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/ACS.key -x509 -days 365 -out /etc/pki/tls/certs/ACS.crt`

`vi /etc/httpd/conf.d/ssl.conf`

[Update the etc file](./images/vi-etc-httpd.PNG)

TLS Certificates From Amazon Certificate Manager (ACM)

You will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB).

Navigate to AWS ACM

Request a public wildcard certificate for the domain name you registered in Freenom

Use DNS to validate the domain name

Tag the resource

- Setting up self-signed certificate for the apache nginx instance:

`vi /etc/httpd/conf.d/ssl.conf`

Creating AMI for the Instances:

Webserver:

[Webserver AMI](./images/webserver-ami-config.PNG)

[Webserver AMI](./images/webserver-ami-success.PNG)

Bastion:

[Bastion AMI](./images/bastion-ami-config.PNG)

[Bastion AMI](./images/bastion-ami-success.PNG)

Nginx:

[Nginx AMI](./images/nginx-ami-config.PNG)

[Nginx AMI](./images/nginx-ami-success.PNG)

- Webserver:

[Webserver AMI](./images/webserver-ami-config.PNG)

[Webserver AMI](./images/webserver-ami-success.PNG)

- Create Target Group for Nginx and Webserver:

[Nginx Target Group](./images/nginx-targetgrp-create.PNG)

[Nginx Target Group](./images/nginx-targetgrp-create2.PNG)

[Nginx Target Group](./images/nginx-targetgrp-success.PNG)

[Wordpress Target Group](./images/wordpress-targetgrp-create.PNG)

[Wordpress Target Group](./images/wordpress-targetgrp-create2.PNG)

[Wordpress Target Group](./images/wordpress-targetgrp-success.PNG)

### CONFIGURE APPLICATION LOAD BALANCER (ALB)

- Application Load Balancer To Route Traffic To NGINX

Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. No request should go directly to Nginx servers. With this kind of setup, we will benefit from intelligent routing of requests from the ALB to Nginx servers across the 2 Availability Zones. We will also be able to offload SSL/TLS certificates on the ALB instead of Nginx. Therefore, Nginx will be able to perform faster since it will not require extra compute resources to valifate certificates for every request.

Create an Internet facing ALB

Ensure that it listens on HTTPS protocol (TCP port 443)

Ensure the ALB is created within the appropriate VPC | AZ | Subnets

Choose the Certificate from ACM

Select Security Group

Select Nginx Instances as the target group

- Since the webservers are configured for auto-scaling, there is going to be a problem if servers get dynamically scalled out or in. Nginx will not know about the new IP addresses, or the ones that get removed. Hence, Nginx will not know where to direct the traffic.

To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer. Not Internet facing since the webservers are within a private subnet, and we do not want direct access to them.

Create an Internal ALB

Ensure that it listens on HTTPS protocol (TCP port 443)

Ensure the ALB is created within the appropriate VPC | AZ | Subnets

Choose the Certificate from ACM

Select Security Group

Select webserver Instances as the target group

Ensure that health check passes for the target group

NOTE: This process must be repeated for both WordPress and Tooling websites.

- Create LoadBalancers:

[Load Balancer](./images/loadbalancer-create1.PNG)

[Load Balancer](./images/loadbalancer-create2.PNG)

[Load Balancer](./images/loadbalancer-create3.PNG)

[Load Balancer Int](./images/loadbalancer-int-create1.PNG)

[Load Balancer Int](./images/loadbalancer-int-create2.PNG)

[Load Balancer Int](./images/loadbalancer-int-create3.PNG)

[Load Balancer Int](./images/loadbalancer-int-success.PNG)

[Load Balancer Succcess](./images/loadbalancer-success.PNG)

- Bastion Launch Template:

[Bastion launch Template](./images/bast-launc-temp-create1.PNG)

[Bastion launch Template](./images/bast-launc-temp-create2.PNG)

[Bastion launch Template](./images/bast-launc-temp-create3.PNG)

[Bastion launch Template](./images/bast-launc-temp-create4.PNG)

[Bastion launch Template Succcess](./images/bast-launc-temp-success.PNG)

- Nginx Launch Template:

[Nginx launch Template](./images/nginx-launc-temp-create1.PNG)

[Nginx launch Template](./images/nginx-launc-temp-create2.PNG)

[Nginx launch Template](./images/nginx-launc-temp-create3.PNG)

[Nginx launch Template](./images/nginx-launc-temp-create4.PNG)

[Nginx launch Template Succcess](./images/nginx-launc-temp-success.PNG)

- Tooling Launch Template:

[Tooling launch Template](./images/tool-launc-temp-create1.PNG)

[Tooling launch Template](./images/tool-launc-temp-create2.PNG)

[Tooling launch Template](./images/tool-launc-temp-create3.PNG)

[Tooling launch Template Succcess](./images/tool-launc-temp-success.PNG)

- Wordpress Launch Template:

[Wordpress launch Template](./images/wordp-launc-temp-create1.PNG)

[Wordpress launch Template](./images/wordp-launc-temp-create2.PNG)

[Wordpress launch Template](./images/wordp-launc-temp-create3.PNG)

[Wordpress launch Template Succcess](./images/wordp-launc-temp-success.PNG)

- Create a Relational database:

[Relational Database Create](./images/create-rds-1.PNG)

[Relational Database Create](./images/create-rds-2.PNG)

[Relational Database Create](./images/create-rds-3.PNG)

[Relational Database Create](./images/create-rds-4.PNG)

[Relational Database Create](./images/create-rds-5.PNG)

[Relational Database Create](./images/create-rds-6.PNG)

[Relational Database Create](./images/create-rds-7.PNG)

[Relational Database Create Success](./images/create-rds-success.PNG)

#### Setup EFS

Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic Network File System (NFS) for use with AWS Cloud services and on-premises resources. In this project, we will utulize EFS service and mount filesystems on both Nginx and Webservers to store data.

Create an EFS filesystem

Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer

Associate the Security groups created earlier for data layer.

Create an EFS access point. (Give it a name and leave all other settings as default)

[Create Elastic File System](./images/create-elas-filesys.PNG)

[Create Elastic File System Success](./images/create-elas-success.PNG)

##### Setup RDS

Pre-requisite: Create a KMS key from Key Management Service (KMS) to be used to encrypt the database instance.

Amazon Relational Database Service (Amazon RDS) is a managed distributed relational database service by Amazon Web Services. This web service running in the cloud designed to simplify setup, operations, maintenans & scaling of relational databases. Without RDS, Database Administrators (DBA) have more work to do, due to RDS, some DBAs have become jobless

To ensure that yout databases are highly available and also have failover support in case one availability zone fails, we will configure a multi-AZ set up of RDS MySQL database instance. In our case, since we are only using 2 AZs, we can only failover to one, but the same concept applies to 3 Availability Zones. We will not consider possible failure of the whole Region, but for this AWS also has a solution – this is a more advanced concept that will be discussed in following projects.

To configure RDS, follow steps below:

Create a subnet group and add 2 private subnets (data Layer)

Create an RDS Instance for mysql 8.*.*

To satisfy our architectural diagram, you will need to select either Dev/Test or Production Sample Template. But to minimize AWS cost, you can select the Do not create a standby instance option under Availability & durability sample template (The production template will enable Multi-AZ deployment)

Configure other settings accordingly (For test purposes, most of the default settings are good to go). In the real world, you will need to size the database appropriately. You will need to get some information about the usage. If it is a highly transactional database that grows at 10GB weekly, you must bear that in mind while configuring the initial storage allocation, storage autoscaling, and maximum storage threshold.

Configure VPC and security (ensure the database is not available from the Internet)

Configure backups and retention

Encrypt the database using the KMS key created earlier

Enable CloudWatch monitoring and export Error and Slow Query logs (for production, also include Audit)

Note This service is an expensinve one. Ensure to review the monthly cost before creating. (DO NOT LEAVE ANY SERVICE RUNNING FOR LONG)

[KMS](./images/rds-key-users.PNG)

[KMS](./images/rds-subnet-config.PNG)

[KMS](./images/rds-subnet-success.PNG)

[KMS](./images/review-key-configure.PNG)

[Vpc Create](./images/create-vpc.PNG)

- Create tooling and wordpress databases:

[Tooling and WordPress Databases](./images/tool-wordp-dbs.PNG)

`mysql -h acs-database.cilcnl2h4q0u.us-east-2.rds.amazonaws.com -u ACSadmin -p`

- Enter Password:

- Configuring DNS with Route53

Earlier in this project you registered a free domain with Freenom and configured a hosted zone in Route53. But that is not all that needs to be done as far as DNS configuration is concerned.

You need to ensure that the main domain for the WordPress website can be reached, and the subdomain for Tooling website can also be reached using a browser.

Create other records such as CNAME, alias and A records.

NOTE: You can use either CNAME or alias records to achieve the same thing. But alias record has better functionality because it is a faster to resolve DNS record, and can coexist with other records on that name. Read here to get to know more about the differences.

Create an alias record for the root domain and direct its traffic to the ALB DNS name.

Create an alias record for tooling.<yourdomain>.com and direct its traffic to the ALB DNS name

[Success](./images/url-success.PNG)

[Tooling Url Success](./images/tooling-r-success.PNG)