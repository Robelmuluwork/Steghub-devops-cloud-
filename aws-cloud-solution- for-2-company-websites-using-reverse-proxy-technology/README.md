## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY
  
## Starting Off Your AWS Cloud Project
  
There are few requirements that must be met before you begin:

1. Properly configure your AWS account and Organization Unit [Watch How To Do This Here](https://youtu.be/9PQYCc_20-Q)

- Create an AWS Master account. (Also known as Root Account) 
- Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)
1.1.  In the AWS Management Console, navigate to AWS Organizations.
![Screenshot 2024-12-14 203720](https://github.com/user-attachments/assets/3b1d5f4e-ef5f-45f0-a37b-b10abe3f9acd)


1.2. If you haven't already created an organization, click "Create an organization" and select "Enable All Features".
1.3. Click "Add account".
1.4. Select "Create account".
1.5. Fill in the required details:
 - Account name: DevOps
 - Email address: Provide a new email address (different from the root account).
1.6. Click "Create". AWS will send a verification email to the provided email address.
![image](https://github.com/user-attachments/assets/768ac497-35a3-4536-bba6-5109bc8d5b0a)


1.7. Verify the email address by following the instructions in the email sent by AWS.
![image](https://github.com/user-attachments/assets/52b4dfc2-6adb-43b2-b8e1-244806653619)



- Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there)
  
  **Create an Organizational Unit (OU) Named Dev**  > **In the AWS Organizations dashboard, click on "Organize accounts"** > **Click on "Create organizational unit"** > **Enter Dev as the name for the new OU** > **Click "Create organizational unit"**
![image](https://github.com/user-attachments/assets/bec6149b-3573-428c-8274-9ae5842e1248)


- Move the DevOps account into the Dev OU.
![image](https://github.com/user-attachments/assets/be883c0c-6735-4d5d-82a6-17c09d815113)


- Login to the newly created AWS account using the new email address.
   - Go to the AWS Management Console login page.
   - Enter the email address used for the DevOps account and click Next.


![image](https://github.com/user-attachments/assets/51088d31-a9ee-4679-9c49-d0dd402faa5d)



2. Create a free domain name for your fictitious company.you can use [cloudns](https://www.cloudns.net/) for free

![image](https://github.com/user-attachments/assets/beab4332-6bea-4eaf-9e15-40057f4c1b0d)



3. Create a hosted zone in AWS, and map it to your free domain.

 In the search bar, type `Route 53` and select `Route 53` from the results. >  In the Route 53 dashboard, click on `Hosted zones` in the left-hand navigation pane >
 Click `Create hosted zone`
Fill in the following details:
   -  Domain name: Enter your Freenom domain name (e.g., example.tk).
   -  Comment: Optional.
   -  Type: Select Public hosted zone.
   -  VPC: Leave this blank for a public hosted zone.
   -  Click Create hosted zone

![image](https://github.com/user-attachments/assets/a7ac479f-be63-4745-9b44-5b04131fb2a0)


 Go to the domain name provider and edit the nameservers

Transfer the (4 server address) in the hostname zone in the (NS column) to the domain provider (nameservers).

![image](https://github.com/user-attachments/assets/a0fd72ec-ff2a-446c-9c1c-37e4cb699184)


The next step is to get a certificate from AWS Certificate Manager. The reason we are creating a certificate first is because when creating `ALB` we need to select a certificate.

- Click on request a Cert > Request public cert > Next

![image](https://github.com/user-attachments/assets/3dbe434b-857e-46d2-8f8d-71007a1c842a)

![image](https://github.com/user-attachments/assets/c6674e36-2dba-44ed-8a6e-efa25d6cf191)



In the domain name, we are going to use a wild card i.e(*.). should in case we want to have another `name` or `subdomain`, the `WILDCARD` will make sure that any name before the domain name is attached to the `certificate`. 
e.g robelM.fictitiouscompany.ip-ddns.com

![image](https://github.com/user-attachments/assets/363a1d70-1e8d-4895-bab0-ec7660771162)



NOTE: Because we are using DNS verification is going to automatically write to the Rout53 to confirm

![image](assets/project15_14_certificate_details.JPG)

> NOTE : As you proceed with configuration, ensure that all resources are appropriately tagged, for example:

Project: Give your project a name `Project-15`

Environment: `dev`

Automated:`No` (If you create a recource using an automation tool, it would be `Yes`)


## SET UP A VIRTUAL PRIVATE NETWORK (VPC)

Set Up a Virtual Private Network (VPC)
Always make reference to the architectural diagram and ensure that your configuration is aligned with it.

1. Create a [VPC](https://docs.aws.amazon.com/vpc/latest/userguide/what-is-amazon-vpc.html)


![image](https://github.com/user-attachments/assets/7dab27f9-427b-4ed7-ae12-04d2ef7ba68b)


**Enable DNS hosting**

![image](https://github.com/user-attachments/assets/b414d56c-1dc8-48c2-893c-76f3c05b270c)




2. Create subnets as shown in the architecture  On the left panel menu of the VPC UI, click on Subnet > Create Subnet.

VPC: 10.0.0.0/16

- Public Subnet 1: 10.0.1.0/24  in Zone A
- Public Subnet 2: 10.0.2.0/24  in Zone B
- Private Subnet 1: 10.0.3.0/24  in Zone A
- Private Subnet 2: 10.0.4.0/24  in Zone B
- Private Subnet 3: 10.0.5.0/24  in Zone A
- Private Subnet 4: 10.0.6.0/24   in Zone B

![image](https://github.com/user-attachments/assets/ececae8f-d81c-4601-9a28-e433eef35eea)


![image](https://github.com/user-attachments/assets/c2f9767e-f83e-43af-b957-9a24671f2082)



3. Create a route table and associate it with public subnets

**In the left-hand navigation pane, click on Route Tables** >  **Create route table button**

![image](https://github.com/user-attachments/assets/e8206092-f099-4ccc-842c-6de34ad0e1c5)

 
 **Enter the following details:**

   - Name tag: Provide a name for your route table  Public Route Table
   - VPC: Select the VPC you created earlier.
   - Click Create route table.

![image](https://github.com/user-attachments/assets/32ffe511-9e00-42a9-bfb0-9bd008c7b444)



**Associate Route Table with Public Subnets**
- Select the Public Route Table from the list.
- Click on the Subnet associations tab.
- Click Edit subnet associations.
- In the Select subnets section, check the boxes for Public Subnet 1 and Public Subnet 2.
- Click Save associations.

4. Create a Private route table and associate it with private subnets

![image](https://github.com/user-attachments/assets/40b76fd2-3cd1-4615-99c4-9a40b02d9bae)



5. Create an [Internet Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Internet_Gateway.html)

Click on Internet Gateways on the left-hand side >  Create internet gateway > Enter a name for your internet gateway > Create internet gateway

![image](https://github.com/user-attachments/assets/961f9b79-a182-4748-b09e-f90cf0ac930a)



**Attach Internet Gateway to VPC**: Select the internet gateway you just created > Click the Actions dropdown, then select Attach to VPC >Select the VPC you created earlier >Click Attach internet gateway.

![image](https://github.com/user-attachments/assets/b39258a6-3127-4aee-8070-bbdfd97b6d30)


6. Edit a route in public route table, and associate it with the Internet Gateway. (This is what allows a public subnet to be accisble 
from the Internet)

Go back to Route Tables and select the Public Route Table  >  Click on the Routes tab > Click Edit routes.
Click Add route > Destination: 0.0.0.0/0 > Target: Select the internet gateway you created > Click Save routes

![image](https://github.com/user-attachments/assets/19c019ff-5ebf-42ba-97c1-d851a6739ce7)



7. Create 3 [Elastic IPs](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/elastic-ip-addresses-eip.html) one for `Nat getway` and the other `2 will be used by Bastion hosts`

Create Elastic IP to configured with the NAT gateway. The NAT gateway enables connection from the public subnet to private subnet and it needs a static ip to make this happen. VPC > Elastic IP addresses > Allocate Elastic IP address - add a name tag and click on allocate

![image](https://github.com/user-attachments/assets/8225ddf6-668e-441c-b77f-5bb752084f26)


![image](https://github.com/user-attachments/assets/ea6a7e13-c924-4e9d-bdb6-47739e13465b)


8. Create a Nat Gateway and assign one of the Elastic IPs the other 2 will be used by [Bastion hosts](https://aws.amazon.com/solutions/implementations/linux-bastion/))

Create a Nat Gateway and assign the Elastic IPs Click on VPC > NAT gateways > Create NAT gateway

Select a Public Subnet
Connection Type: Public
Allocate Elastic IP

![image](https://github.com/user-attachments/assets/550e96a8-2f04-4652-a0c4-5a72be736bea)


Update the Private route table - add allow anywhere ip and associate it the NAT gateway

![image](https://github.com/user-attachments/assets/3dd9e713-afec-4aba-aa0a-ab9721b354e4)


9. Create a Security Group for:


- **Nginx Servers**: Access to Nginx should only be allowed from a Application Load balancer (ALB). At this point, we have not created a 
load balancer, therefore we will update the rules later. For now, just create it and put some dummy records as a place holder.

- **Bastion Servers**: Access to the Bastion servers should be allowed only from workstations that need to SSH into the bastion servers.
Hence, you can use your workstation public IP address. To get this information, simply go to your terminal and type 
curl www.canhazip.com

- **Application Load Balancer**: ALB will be available from the Internet give it both HTTPS and HTTP from anywhere 0.0.0.0/0

- **Webservers** : Access to Webservers should only be allowed from the Nginx servers. Since we do not have the servers created yet, just put some dummy records as a place holder, we will update it later.

- **Data Layer** : Access to the Data layer, which is comprised of Amazon Relational Database Service (RDS) and Amazon Elastic File 
System (EFS) must be carefully desinged – only webservers should be able to connect to RDS, while Nginx and Webservers will have 
access to EFS Mountpoint.

![image](https://github.com/user-attachments/assets/5d9e3cd3-4732-41d2-8e5f-7d0fe9109d79)


Create others like above

![image](https://github.com/user-attachments/assets/51851e0e-808d-45a1-b7ab-47ce62e3b001)


**Proceed With Compute Resources**
You will need to set up and configure compute resources inside your VPC. The recources related to compute are:

- EC2 Instances
- Launch Templates
- Target Groups
- Autoscaling Groups
- TLS Certificates
- Application Load Balancers (ALB)

## Set Up Compute Resources for Nginx  Provision EC2 Instances for Nginx

1. Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) in any 2 Availability Zones (AZ) in any AWS Region 
(it is recommended to use the Region that is closest to your customers). Use EC2 instance of T2 family (e.g. t2.micro or similar)


2. Ensure that it has the following software installed:

- python
- ntp
- net-tools
- vim
- wget
- telnet
- epel-release
- htop

**Nginx ami installation**
```
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm

yum install wget vim python3 telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd
```
**Configure selinux policies for the webservers and nginx servers**
```
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1
```
**This section will instll amazon efs utils for mounting the target on the Elastic file system**
```
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm
```
**Seting up self-signed certificate for the nginx instance**
```
sudo mkdir /etc/ssl/private

sudo chmod 700 /etc/ssl/private

openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/ACS.key -out /etc/ssl/certs/ACS.crt

sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048
```

3. Create an AMI out of the EC2 instance
Select the EC2 instance you want to create an AMI from  > Click on Actions > Image and templates > Create image.
- Provide a name and description for the image.
- Configure any additional options like reboot behavior.
- Click Create image
  
Prepare Launch Template For Nginx (One Per Subnet)
1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet
3. Assign appropriate security group
4. Configure Userdata to update yum package repository and install nginx

![image](https://github.com/user-attachments/assets/8903ac25-e112-4a02-bb6f-ede2975ebbaa)


## Configure Target Groups

1. Select Instances as the target type
2. Ensure the protocol HTTPS on secure TLS port 443
3. Ensure that the health check path is /healthstatus
4. Register Nginx Instances as targets
5. Ensure that health check passes for the target group

![image](https://github.com/user-attachments/assets/f2200835-e829-4d69-8a29-6e47a47bb7e3)


## CONFIGURE AUTOSCALING FOR NGINX

1. Select the right launch template
2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG)
5. Select the target group you created before
6. Ensure that you have health checks for both EC2 and ALB
7. The desired capacity is 2
8. Minimum capacity is 2
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%
11. Ensure there is an SNS topic to send scaling notifications

![image](https://github.com/user-attachments/assets/4277789f-e3b4-49b6-ae40-a9aef8dfbb66)

![image](https://github.com/user-attachments/assets/b3892931-9943-4419-a441-195287302b78)


## Set Up Compute Resources for Bastion
Provision the EC2 Instances for Bastion

1. Create an EC2 Instance based on CentOS Amazon Machine Image (AMI) per each Availability Zone in the same Region and same AZ 
where you created Nginx server
2. Ensure that it has the following software installed

- python
- ntp
- net-tools
- vim
- wget
- telnet
- epel-release
- htop

We will use instance to create an ami for launching instances 

```
sudu su -
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm
yum install wget vim python3 telnet htop git mysql net-tools chrony -y 
systemctl start chronyd 
systemctl enable chronyd
```
3. Associate an Elastic IP with each of the Bastion EC2 Instances

4. Create an AMI out of the EC2 instance

 Here is sample for the one available zone

![image](https://github.com/user-attachments/assets/fa6f5cc7-57d2-4145-a4fe-4d9046766f49)


Prepare Launch Template For Bastion (One per subnet)

1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet
3. Assign appropriate security group
4. Configure Userdata to update yum package repository and install Ansible and git
 
 Here is sample for the one available zone

![image](https://github.com/user-attachments/assets/239da23d-6927-40c4-b787-fc8caaefe6d8)


## Configure Target Groups

1. Select Instances as the target type
2. Ensure the protocol is TCP on port 22
3. Register Bastion Instances as targets
4. Ensure that health check passes for the target group

![image](https://github.com/user-attachments/assets/eb0eabcc-f756-41fa-a2d5-1b56c8a3e82e)


## CONFIGURE AUTOSCALING FOR BASTION

1. Select the right launch template
2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG)
5. Select the target group you created before
6. Ensure that you have health checks for both EC2 and ALB
7. The desired capacity is 2
8. Minimum capacity is 2
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%
11. Ensure there is an SNS topic to send scaling notifications

![image](https://github.com/user-attachments/assets/fed1aba3-f99a-4f9a-8ab1-c8acfb90a999)


## Set Up Compute Resources for Webservers

Provision the EC2 Instances for Webservers
Now, you will need to create 2 separate launch templates for both the WordPress and Tooling websites

1. Create an EC2 Instance (Centos) each for WordPress and Tooling websites per Availability Zone (in the same Region).

2. Ensure that it has the following software installed

- python
- ntp
- net-tools
- vim
- wget
- telnet
- epel-release
- htop
- php

```
sudo yum install -y https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
sudo yum install -y https://rpms.remirepo.net/enterprise/remi-release-9.rpm

yum install wget vim python3  php telnet htop git mysql net-tools chrony -y

systemctl start chronyd

systemctl enable chronyd
```

3. Create an AMI out of the EC2 instance select the EC2 instance you want to create an AMI from  > Click on Actions > Image and templates > Create image.
- Provide a name and description for the image.
- Configure any additional options like reboot behavior.
- Click Create image

**configure selinux policies for the webservers and nginx servers**
```
setsebool -P httpd_can_network_connect=1
setsebool -P httpd_can_network_connect_db=1
setsebool -P httpd_execmem=1
setsebool -P httpd_use_nfs 1
```
**instll amazon efs utils for mounting the target on the Elastic file system**
```
git clone https://github.com/aws/efs-utils

cd efs-utils

yum install -y make

yum install -y rpm-build

make rpm 

yum install -y  ./build/amazon-efs-utils*rpm
```
Setting up self-signed certificate for the apache webserver instance
```
yum install -y mod_ssl

openssl req -newkey rsa:2048 -nodes -keyout /etc/pki/tls/private/ACS.key -x509 -days 365 -out /etc/pki/tls/certs/ACS.crt

vi /etc/httpd/conf.d/ssl.conf
```

Prepare Launch Template For Webservers (One per subnet)

1. Make use of the AMI to set up a launch template
2. Ensure the Instances are launched into a public subnet
3. Assign appropriate security group
4. Configure Userdata to update yum package repository and install wordpress (Only required on the WordPress launch template)

![image](https://github.com/user-attachments/assets/b02c892d-de93-4087-a57d-0542f9f43fa0)



   **Instances are listed**
![image](https://github.com/user-attachments/assets/fed356a2-e3d2-4028-a7e7-fc1b309d8ab1)




**Creating AMI from the instances**
![image](https://github.com/user-attachments/assets/06d134a7-b2ab-4ec0-9578-03bcda0369b3)


**Creating Target Groups**
![image](https://github.com/user-attachments/assets/a41f8551-bdd4-4d91-bd87-ddd10ba3fe7c)

**Create Launch Templates**
The lunch templates requires AMIs (Amazon Machine Images) - Create AMIs from the instances and terminate them.From the created custom AMIs, create Launch templates for each of the instances

![image](https://github.com/user-attachments/assets/c2b214d9-7ba1-4a20-8a68-63ea44c5f3b9)


![image](https://github.com/user-attachments/assets/8e437bec-9743-45f9-9e78-9f1fc9e62461)

![image](https://github.com/user-attachments/assets/a7c8a7c0-7b6d-42a0-8942-215953363d58)


![image](https://github.com/user-attachments/assets/c06c978e-0db7-453a-860b-a5b18b3cff00)

![image](https://github.com/user-attachments/assets/cee4e850-7d8c-464d-87bf-f9d991865355)


**Configure Autoscaling For Bastion**

![image](https://github.com/user-attachments/assets/733dab38-91e5-4a68-a1d3-a2d33390c026)


1. Select the right launch template
![image](https://github.com/user-attachments/assets/5ce7db10-5b70-4888-af0a-9e4daa6f831c)


![image](https://github.com/user-attachments/assets/473b084d-ad24-4512-b22d-6401f7648c82)


2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG)

5. Select the target group you created before
6. Ensure that you have health checks for both EC2 and ALB
7. The desired capacity is 2
8. Minimum capacity is 2
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%


11. Ensure there is an SNS topic to send scaling notifications


![image](https://github.com/user-attachments/assets/0347edb7-a950-4caa-892e-8b12e4455ab4)

![image](https://github.com/user-attachments/assets/a35cc2e9-3c98-440b-acad-bf8eaec56fcd)

![image](https://github.com/user-attachments/assets/3d869027-b3f9-4698-aa2c-8cf7437a71d4)

## Configure Autoscaling For Nginx repate same thing for this also

1. Select the right launch template
2. Select the VPC
3. Select both public subnets
4. Enable Application Load Balancer for the AutoScalingGroup (ASG)
5. Select the target group you created before
6. Ensure that you have health checks for both EC2 and ALB
7. The desired capacity is 2
8. Minimum capacity is 2
9. Maximum capacity is 4
10. Set scale out if CPU utilization reaches 90%

**TLS Certificates From Amazon Certificate Manager (ACM) you will need TLS certificates to handle secured connectivity to your Application Load Balancers (ALB)**

1. Navigate to AWS ACM
2. Request a public wildcard certificate for the domain name you registered in Freenom
3. Use DNS to validate the domain name
4. Tag the resource

![image](https://github.com/user-attachments/assets/0770e3df-a03f-46d8-a600-ab9ff553b53f)



![image](https://github.com/user-attachments/assets/cc3a1715-7fbc-4b78-a263-78ae065f0205)


![image](https://github.com/user-attachments/assets/2b84adca-2f8d-46b7-8c2b-8cafa8e306cb)


![image](https://github.com/user-attachments/assets/a890b832-43d3-47f5-b6f2-37e252fc3e30)


## CONFIGURE APPLICATION LOAD BALANCER (ALB) Application Load Balancer To Route Traffic To NGINX
Nginx EC2 Instances will have configurations that accepts incoming traffic only from Load Balancers. No request should go directly
to Nginx servers. With this kind of setup, we will benefit from intelligent routing of requests from the `ALB` to Nginx servers 
across the 2 `Availability Zones`. We will also be able to `offload SSL/TLS certificates` on the ALB instead of Nginx. Therefore, 
Nginx will be able to perform faster since it will not require extra compute resources to validate certificates for every request.

1. Create an Internet facing ALB

Open the Amazon EC2 console > Navigate to Load Balancers under the Load Balancing section > Click Create Load Balancer and select Application Load Balancer >  Configure the ALB > 

- Name: Enter a name for your ALB
- Scheme: Choose Internet-facing
- IP address type: Choose ipv4
- Listeners: Add a listener for HTTPS (port 443).
- Availability Zones: Select the appropriate VPC and Availability Zones (AZs). Ensure that the ALB is created within subnets that have internet access.

![image](https://github.com/user-attachments/assets/d8e4ce7c-e283-4894-9d6c-35adfed71fcc)



![image](https://github.com/user-attachments/assets/0df46d37-f466-42b2-8850-95db1f119969)


2. Ensure that it listens on HTTPS protocol (TCP port 443)
![image](https://github.com/user-attachments/assets/cfe96c69-eaa6-45ee-93df-655a00ae5176)


3. Ensure the ALB is created within the appropriate VPC | AZ | Subnets
![image](https://github.com/user-attachments/assets/1d65e135-d351-409b-81e5-77434bde23b7)



4. Choose the Certificate from ACM
![image](https://github.com/user-attachments/assets/b839e7f6-3b2e-4849-b232-8063d155783a)


5. Select Security Group

![image](https://github.com/user-attachments/assets/9b1759c2-2962-44a9-a096-17d917394ce8)



6. Select Nginx Instances as the target group

![image](https://github.com/user-attachments/assets/3f6377ea-5b51-440a-bd1c-1376c4bc4360)


## Application Load Balancer To Route Traffic To Web Server
Since the webservers are configured for auto-scaling, there is going to be a problem if servers get dynamically scalled out or in. 
Nginx will not know about the new IP addresses, or the ones that get removed. Hence, Nginx will not know where to direct the traffic.

To solve this problem, we must use a load balancer. But this time, it will be an internal load balancer. Not Internet facing since
the webservers are within a private subnet, and we do not want direct access to them.


1. Create an Internal ALB
![image](https://github.com/user-attachments/assets/99fe473c-b6e0-440a-8500-4f1ae342963e)


2. Ensure that it listens on HTTPS protocol (TCP port 443)
![image](https://github.com/user-attachments/assets/35d07b79-cb4e-406c-85d5-5d990b4b0c2b)


3. Ensure the ALB is created within the appropriate VPC | AZ | Subnets
![image](https://github.com/user-attachments/assets/3ee5865f-994f-4197-8f11-b43275bf480b)


4. Choose the Certificate from ACM

![image](https://github.com/user-attachments/assets/8a435797-2dca-4582-9ac4-15ca5b179273)

5. Select Security Group
![image](https://github.com/user-attachments/assets/162bc856-9ce3-458b-88c2-f46e1eb8fe45)


6. Select webserver Instances as the target group
7. Ensure that health check passes for the target group

![image](https://github.com/user-attachments/assets/a0b6cbfe-2980-4211-9886-ff2681cbf2a5)



> NOTE: This process must be repeated for both WordPress and Tooling websites.

### Setup EFS
Amazon Elastic File System (Amazon EFS) provides a simple, scalable, fully managed elastic Network File System (NFS) for use with
AWS Cloud services and on-premises resources. In this project, we will utulize EFS service and mount filesystems on both Nginx 
and Webservers to store data.

1. Create an EFS filesystem

![image](https://github.com/user-attachments/assets/6d558f42-f8b9-42bb-aeb4-d3650b9bf17d)


![image](https://github.com/user-attachments/assets/c66ec938-e11d-4374-aca8-b2d3d87c4089)



2. Create an EFS mount target per AZ in the VPC, associate it with both subnets dedicated for data layer
3. Associate the Security groups created earlier for data layer.
![image](https://github.com/user-attachments/assets/6dae99bc-41b7-42b4-bc41-d9341a957ee0)


4. Create an EFS access point. Create Access Points mount the applications wordpress and tooling

![image](https://github.com/user-attachments/assets/b5f7bdef-3cf9-4c84-bb36-cbd5807183c5)


![image](https://github.com/user-attachments/assets/de1b6a7e-4120-44f8-b09f-64efde57f834)


![image](https://github.com/user-attachments/assets/bd00319d-692a-40b0-9426-750473bd8ed6)




### Setup RDS
Pre-requisite: Create a KMS key from Key Management Service (KMS) to be used to encrypt the database instance. Create KMSa Key Management Service to encrypt the Database

Open the AWS Management Console > Navigate to the AWS Key Management Service (KMS) > Click Create Key >
- Key Type: Choose Symmetric.
- Key Usage: Select Encrypt and decrypt.
![image](https://github.com/user-attachments/assets/e781713c-ceb0-45a8-95fa-16c9e549b6ab)


**Configure Key:**
- Key Alias: Enter an alias for your key  `project15_key_RDS`
- Key Description: Optionally add a description.

![image](https://github.com/user-attachments/assets/fa19db63-049e-4b4c-b175-cd028e90deb8)



Define Key Administrative Permissions: Add IAM roles or users who can manage this key.
![image](https://github.com/user-attachments/assets/96e5ee70-ed66-4570-812c-934960678682)


Define Key Usage Permissions:
![image](https://github.com/user-attachments/assets/4fd58344-3ac4-4001-9179-dddfeb09314c)



Review and Create:
![image](https://github.com/user-attachments/assets/16dbca33-9790-4419-81ea-7abac13a1522)


Review the settings and click Create Key.

![image](https://github.com/user-attachments/assets/dc5fd205-2ac8-491d-ae8d-a9cb0d91ad68)


Amazon Relational Database Service (Amazon RDS) is a managed distributed relational database service by Amazon Web Services. 
This web service running in the cloud designed to simplify setup, operations, maintenans & scaling of relational databases. 
Without RDS, Database Administrators (DBA) have more work to do, due to RDS, some DBAs have become jobless

To ensure that your databases are highly available and also have failover support in case one availability zone fails, we will 
configure a multi-AZ set up of RDS MySQL database instance. In our case, since we are only using 2 AZs, we can only failover to
one, but the same concept applies to 3 Availability Zones. We will not consider possible failure of the whole Region, but for 
this AWS also has a solution – this is a more advanced concept that will be discussed in following projects.

To configure RDS, follow steps below:

1. Create a subnet group and add 2 private subnets (data Layer)
![image](https://github.com/user-attachments/assets/fa80e979-0ff1-4821-9e8b-ac828a65155a)

![image](https://github.com/user-attachments/assets/0b5fbf84-018e-4df9-a8f5-d9b18232db7b)


![image](https://github.com/user-attachments/assets/525271e4-8533-46c6-8be9-80be97ecc034)



2. Create an RDS Instance for mysql 8.*.*
![image](https://github.com/user-attachments/assets/16e55d01-81c7-4b6a-94de-1a970e0e37fe)

![image](https://github.com/user-attachments/assets/7b120a46-18da-4293-be81-e85c1e61a2b1)


3. To satisfy our architectural diagram, you will need to select either Dev/Test or Production Sample Template. But to minimize AWS 
cost, you can select the Do not create a standby instance option under Availability & durability sample template (The production 
template will enable Multi-AZ deployment)


![image](https://github.com/user-attachments/assets/b3c9db1f-94c3-47fb-866d-218a6706cbc5)


4. Configure other settings accordingly (For test purposes, most of the default settings are good to go). In the real world, you will
need to size the database appropriately. You will need to get some information about the usage. If it is a highly transactional 
database that grows at 10GB weekly, you must bear that in mind while configuring the initial storage allocation, storage autoscaling, 
and maximum storage threshold.

![image](https://github.com/user-attachments/assets/c72e1a6c-a817-4a3b-9a3d-91ed1d7a3696)


![image](https://github.com/user-attachments/assets/1665802b-387e-43c3-8ff8-8e3325b4664c)



5. Configure VPC and security (ensure the database is not available from the Internet)
![image](https://github.com/user-attachments/assets/6ac9b4df-1679-4323-b92a-be7af32c9992)



6. Configure backups and retention
![image](https://github.com/user-attachments/assets/4fff5e58-b588-4676-8ccb-dc2fae56b175)


7. Encrypt the database using the KMS key created earlier
S![image](https://github.com/user-attachments/assets/2c1cf718-cc9c-414f-ac8a-90fdc6db7e59)



8.Enable CloudWatch monitoring and export Error and Slow Query logs (for production, also include Audit)

![image](https://github.com/user-attachments/assets/7dff26c8-2678-46f4-b8c5-96020a326875)



![image](assets/project15_95_created_db_008.JPG)

> Note This service is an expensinve one. Ensure to review the monthly cost before creating. **(DO NOT LEAVE ANY SERVICE RUNNING FOR LONG)Create other records such as CNAME, alias and A records**

**NOTE:** You can use either CNAME or alias records to achieve the same thing. But alias record has better functionality because it is 
a faster to resolve DNS record, and can coexist with other records on that name. Read here to get to know more about the differences.


- Create an alias record for the root domain and direct its traffic to the ALB DNS name.
- Create an alias record for tooling.<yourdomain>.com and direct its traffic to the ALB DNS name.

You need to ensure that the main domain for the WordPress website can be reached, and the subdomain for Tooling website can also be 
reached using a browser.


![image](https://github.com/user-attachments/assets/c9a0d39e-b4f6-413e-ad1b-f84f4bcc5c2c)


 Worpress website

![image](https://github.com/user-attachments/assets/835a85c3-3a13-4c52-bc14-1eb47a057ef3)

![image](https://github.com/user-attachments/assets/5a6c2eeb-fdaf-4abe-b005-4f26de2ddfcf)


