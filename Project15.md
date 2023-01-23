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

- To setup RDS:

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

- Change to super user

`sudo su -`

Run the commands below:

`yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm`

`yum install -y dnf-utils http://rpms.remirepo.net/enterprise/remi-release-8.rpm`

`yum install wget vim python3 telnet htop git mysql net-tools chrony -y`

`systemctl start chronyd`

`systemctl enable chronyd`

Set Up Compute Resources for Nginx:

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

Creating AMI for the Instances:

Webserver:

Bastion:

Nginx:

- Create Target Group:


- Create LoadBalancers:

- Bastion Launch Template:

- Nginx Launch Template:

