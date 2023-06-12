## Cloud Training

![image](https://user-images.githubusercontent.com/27693622/236635528-6813145c-189e-4d72-a754-f766a58a3213.png)

![image](https://user-images.githubusercontent.com/27693622/236635819-41df1f06-5d51-4833-8402-1881b5b2b44c.png)

![image](https://user-images.githubusercontent.com/27693622/236636869-e8cfc928-2223-4a92-814d-c8a2a611d15e.png)

![image](https://user-images.githubusercontent.com/27693622/236637912-0cb63609-6f48-442b-9f0f-6c69da5f4857.png)

### AWS Certified Solutions Architect - Associate (SAA - CO3)

https://d1.awsstatic.com/training-and-certification/docs-sa-assoc/AWS-Certified-Solutions-Architect-Associate_Exam-Guide_C03.pdf

| Domain                                         | % of exam |
|------------------------------------------------|-----------|
| Domain 1: Design Secure Architectures          | 30%       |
| Domain 2: Design Resilient Architectures       | 26%       |
| Domain 3: Design High-Performing Architectures | 24%       |
| Domain 4: Design Cost-Optimized Architectures  | 20%       |
| Total                                          | 100%      |

This diagram shows how AWS IAM works:
![aws_management](https://user-images.githubusercontent.com/27693622/236675900-3a20dd08-67e0-421a-a8aa-24d12f5031f2.jpg)

## Course Overview

This link is useful for code relating to AWS Certified Solutions Architect:
https://github.com/nealdct/aws-csaa-code

Here, we will learn everything you need to know to pass your AWS Certified Solutions Architect Associate exam.
What we will learn:

- how to design and build multi-tier web architectures with services such as Amazon EC2 Auto Scaling, Amazon Elastic
  Load Balancing (ELB), AWS Route 53, AWS Lambda, Amazon API Gateway and Amazon Elastic File System (EFS)
- how to create Docker container clusters on Amazon Elastic Container Services (ECS), set up serverless event-driven AWS
  Lambda Functions with Amazon API Gateway and Amazon Kinesis integrations, and geographically redundant database
  services with Amazon Relational Database Service (RDS)
- how to configure Amazon Virtual Private Clouds (VPC), subnets, and route tables and setup best practice Security Group
  configurations
- how to build repeatably and securely with AWS CloudFormation, set up a PaaS with AWS Elastic Beanstalk, configure
  Amazon S3 bucket policies and share data between multiple AWS accounts
- how to use application integration services including AWS Step Functions, Amazon MQ, SNS, SWF and SQS

### AWS Identity and Access Management

![iam_access (3)](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/0a518c9f-a587-4e6c-8712-406395416f1d)

The identity and access principals are used to check authentication. The principals can then create resources across AWS
regions according
to the authorization of the principal.

![iam_management](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/fcd41e70-25e3-4ed5-ab77-411014ba5ef4)

#### Users, Groups, Roles and Policies

A group is a way of organizing users. We then attach the policy to the user group. The user gains the policies added to
the group.
Identity based policies define the permissions applied to the group through the policy. Roles are used for delegation
and are assumed.
Policies define the permissions for the identity or the resources they are associated with. The root user has full
permissions. It is a best
practice to avoid using the root user account + enable MFA. You can create up to 5000 individual user accounts in IAM.
Users have no permissions
by default. Each user will have a friendly name and an Amazon Resource Name (arn):

```bash
arn:aws:iam:6453234333532:user/Andrea
```

Access keys or username/password can be used for authentication. We collect users in a group and then apply the
permissions to users using policies.
IAM roles are identities in IAM that have specific permissions. Roles are assumed by users, applications and services:

```bash
sts:AssumeRole
```

Once assumed, the identity becomes the role and gains the role's permissions. IAM policies are documents that define
permisssions:

```yaml
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

Bucket policy example:

```yaml
{
  "Version": "2012-10-17",
  "Id": "Policy1237456175634",
  "Statement": [
    "Sid": "Stmt1237456175634",
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam:1237456175634:user/Paul"
    },
    "Action": "s3:*",
    "Resource": "arn:aws:s3::dctcompany",
    "Condition": {
      "StringLike": {
        "s3:prefix": "Confidential/*"
      }
    }
  ]
}
```

You can use Username and Password to log into the AWS Management Console. To log in through the CLI or API we use Access
key ID and secret.
Multifactor authentication:
Something you know -> Something you have -> Something you are

### AWS Security Token Service (STS)

In order for an EC2 instance to access an application we can add an Instance Profile. In the profile we have a trust
policy and a permissions policy.
The Trust policy can look like this:

```yaml
{
  "Effect": "Allow",
  "Principal": {
    "Service": "ec2.amazonaws.com"
  },
  "Action": "sts:AssumeRole"
}
```

Trust policies control who can access the role.

#### Identity based IAM Policies

Identity based policies are JSON permissions policy documents that control waht actions an identity can perform, on
which resources, and under what conditions.
We can create inline policies for specific users and roles. We can also have managed policies which can be managed by
AWS or the account owner. The managed policies
can be attached to users, groups or policies.

#### Resource Based Policies

Resource based policies are JSON policy documents that we can attach to a resource such as an AWS S3 bucket:

```yaml
{
  "Version": "2012-10-17",
  "Id": "Policy1237456175634",
  "Statement": [
    "Sid": "Stmt1237456175634",
    "Effect": "Allow",
    "Principal": {
      "AWS": "arn:aws:iam:1237456175634:user/Paul"
    },
    "Action": "s3:*",
    "Resource": "arn:aws:s3::dctcompany",
    "Condition": {
      "StringLike": {
        "s3:prefix": "Confidential/*"
      }
    }
  ]
}
```

Resource based policies grant the specified principal permission to perform specific actions on the resource. For
instance this policy allows the user
to perform any action on s3. We can also attach resource based policies to the IAM role. A trust policy is also an
example of a resource based policy.

#### Permissions boundaries

The permissions boundary sets the maximum permissions that the entity can have:

```yaml
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:*",
        "cloudwatch:*",
        "ec2:*"
      ],
      "Resource": "*"
    }

  ]
}
```

Here the permissions boundary means that although the user has full control of S3, CloudWatch, EC2 and IAM the
permissions boundary means that she does not have permissions to
allow an application to use S3 for instance. You don't get granted permissions through a permissions boundary but
permissions boundary controls the privilege escalation.

This is a useful overview of the logic for permissions:
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/4fcf03d6-06a9-4874-924e-dc400e90c7df)

#### Authorizing Requests to AWS

Request context includes:

- Actions - the actions or operations the principal wants to perform
- Resources - the AWS resource object upon which actions are performed
- Pincipal - The user, role, federated user, or application that sent the request
- Environment data - Information about the IP address, user agent, SSL status, or time of day
- Resource data - Data related to the resource that is being requested

Types of policy:

- Identity-based policies - attached to users, groups or roles
- Resource based policies - attached to a resource and define permissions for a principal accessing the resource
- IAM permissions boundaries - set the maximum permissions an identity-based policy can grant an IAM entity
- AWS Organizations service control policies (SCP) - specify the maximum permissions for an organization

#### Determination rules

1. By default, all requests are implicitly denied (though the root user has full access)
2. An explicit allow in an identity based or resource based policy overrides this default
3. If a permissions boundary, Organizations SCP, or session policy is present, it might override the
   allow with an implicit deny.
4. An explicit deny in any policy overrides any allows

#### IAM Policy Structure

```yaml
    "Statement": [
  {
    "Effect": "effect",
    "Action": "action",
    "Resource": "arn",
    "Condition": {
      "condition": {
        "key": "value"
      }
    }
  }
]
```

This is an example of a policy:

```yaml
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "*",
      "Resource": "*"
    }
  ]
}
```

This is a more complex example:

```yaml
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [ "ec2:TerminateInstances" ],
      "Resource": [ "*" ]
    },
    {
      "Effect": "Deny",
      "Action": [ "ec2:TerminateInstances" ],
      "Condition": {
        "NotIpAddress": [
          "aws:SourceIp": [
            "192.0.2.0/24",
            "203.0.113.0/24"
          ]
        ]
      },
      "Resource": [ "*" ]
    }
  ]

}
```

Here the specific API action is defined. It is permitted for all resources. There is also a conditional deny if the IP
address is not in the
specified range. Only people in an office can terminate the resource.

The IAM policy simulator is useful for checking and creating user permissions:
https://policysim.aws.amazon.com/home/index.jsp?#

### AWS IAM Best Practices

- Require human users to use federation with an identity provider to access AWS using temporary credentials
- Require workloads to use temporary credentials with IAM roles to access AWS
- Require multifactor authentication (MFA)
- Rotate access keys regularly for use cases that require long-term credentials
- Safeguard your root user credentials and don't use them for everyday tasks
- Apply least-privilege permissions
- Get started with AWS managed policies and move toward least-privilege permissions
- Use IAM Analyzer to generate least-privilege policies based on access activity
- Regularly review and remove unused users, roles, permissions, policies and credentials
- Use conditions in IAM policies to further restrict access
- Establish permissions guardrails across multiple accounts
- Use permissions boundaries to delegate permissions management within an account

### AWS IAM architecture patterns

| Requirement                                                                                                      | Solution                                                                                                      |
|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------|
| A select group of users only should be allowed to change their IAM passwords                                     | Create a group for the users and apply a permissions policy that grants the iam:ChangePassword API permission |
| An Amazon EC2 instance must be delegated with permissions to an Amazon DynaoDB table                             | Create a role and assign a permissions policy to the role that grants access to the database service          |
| A company has created their first AWS account. They need to assign permissions to users based on job function    | Use AWS managed policies that are aligned with common job functions                                           |
| A solutions architect needs to restrict access to an AWS service based on the source IP address of the requestor | Create an IAM permisssions policy and use the Condition element to control access based on source IP address  |
| A developer needs to make programmatic API calls from the AWS CLI                                                | Instruct the developer to create a set of access keys and use those for programmatic access                   |
| A group of users require full access to all Amazon EC2 API actions                                               | Create a permissions policy that uses a wildcard for the Action element relating to EC2 (ec2:*)               |

### Exam Cram (IAM)

- IAM is used to securely control individual and group access to AWS resources
- IAM makes it easy to provide multiple users secure access to AWS resources
- IAM can be used to manage:
  - Users
  - Groups
  - Access policies
  - Roles
  - User credentials
  - User password policies
  - Multifactor authentication (MFA)
  - Generate API keys for programmatic access
- By default, new users are created with NO access to any AWS services - they can only log into the AWS console
- Permission must be explicitly granted to allow a user to access an AWS service
- IAM users are individuals who have been granted access to an AWS account
- IAM is universal (global) and does not apply to regions
- IAM is eventually consistent
- Authentication methods:
  - Console password - use to login to the AWS management console
  - Access keys - used for programmatic access
  - Server certificates - use SSL / TLS access
- IAM users represent a person or service
- Root user credentials are email address to create the account
- root account has full administrative permissions
- IAM users can be created to represent applications and are known as service accounts
- We can have upto 5000 users per AWS account
- IAM groups:
  - groups are collections of users and have policies attached to them
  - A group is not an identity and cannot be identified as a principal in an IAM policy
  - Use groups to assign permissions
- IAM roles:
  - created and assumed by "trusted" entities
  - delegate permissions
  - Security Token Service (STS) allows us to obtain temporary security credentials
- IAM policies:
  - documents can be applied to users, groups and roles
  - The most restrictive policy is applied
- Types of IAM policy:
  - Identity based policies
  - Resource based policies
  - IAM permissions boundaries - set the maximum permissions an identity based policy can grant to an IAM entity
  - Organization service control policies specify maximum permissions for an organization
- IAM best practices:
  - lock root access
  - create users
  - use groups
  - grant least privilege
  - use customer managed policies not inline policies


### Networking
The computer finds servers by IP address. Computers use HTTP protocol. There is also a Port which is the door into the server.
HTTP port is 80. HTTPS port is port 443. Depending on the service the protocol would change. A microsoft server would use SMB/CIFS on port 445.
SMTP for email would use port 25. Servers also speak to other servers such as databases. MySQL uses 3306. Client to server or server to server uses
ports and protocols and networking. 

#### Open Systems Interconnection (OSI) Model
This link is quite good for the OSI model:
https://www.geeksforgeeks.org/layers-of-osi-model/

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/ad5d8e58-6f59-4dc8-9912-75f964497943)

Layer 1 is the physical layer. Layer 2 uses Media Access Control Addresses so that computers can communicate with each other.
Layer 3 is the Network Layer. The Router allows computers on a network to communicate with each other. The routers find the best
path to the destination. Layer 4 is the transport layer. TCP uses a Syn, Syn-Ack, Ack handshake. UDP has no standard connection.
At level 1 we have bits. At level 2 we have frames. At level 3 we have packets. Transport layer uses segments. 
All People Seem to Need Data Processing:
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/c95afbde-c17e-4c41-8df2-232d66ff725d)

From the top to the bottom:
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/077627ce-e166-4061-8b63-f385a5d7f47d)

In the cloud we do deal with network and transport layer for IP addresses and ports we want open. Tools like Wireshark
can be useful for getting information about requests and responses.

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/c4d98814-ba6e-456a-b436-96f9aa235f03)


![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/c7d2add9-b3c9-45b9-93a6-d378b6586e73)

Useful network commands:
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/f2ce504d-aa20-4901-b71d-324e96a009d7)

Addresses that can be used for private ranges:
```text
Class A: 10.0. 0.0 to 10.255. 255.255.
Class B: 172.16. 0.0 to 172.31. 255.255.
Class C: 192.168. 0.0 to 192.168. 255.255.
```

# Lab 2 - Network Commands

## List of Windows commands

ipconfig /all
ping
arp -a
route print
netstat -ano
tracert

## List of Linux / Mac commands

To install net tools:
sudo apt install net-tools

ifconfig
ip a
ping
arp
route -v
netstat -apn

# Lab 3 - Install SSH and FTP Server

## Install OpenSSH Server on Ubuntu
sudo apt install openssh-server

## Connect to OpenSSH Server from Windows
ssh <USER-NAME>@<IP-ADDRESS>

## Install FTP server on Ubuntu
sudo apt-get install vsftpd

## Open configuration file for editing
sudo nano /etc/vsftpd.conf

## Edit configuration file entries as follows
anonymous_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
chown_uploads=YES
chown_username=ftp
anon_upload_enable=YES
anon_mkdir_write_enable=YES
anon_other_write_enable=YES

## Restart the vsftpd service
sudo systemctl restart vsftpd

## Create a file in the FTP directory
sudo touch /srv/ftp/my-ubuntu-file.txt

## FTP to the Ubuntu server from Windows
- open <IP-ADDRESS>
- User name is: anonymous
- Password is blank

## Download the file
get my-ubuntu-file.txt


### IAM Policies

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/bdb4dd42-6fe7-47aa-a16a-0290a2945824)

Here we define access to the bucket and access to the objects within the bucket.

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/203923f6-6c8b-4c00-b9d0-8699113f7b75)


![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/182ee31d-823d-4994-8729-00fce3d05b84)


![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/54c4ae8f-4c49-4c4c-a706-2fbd55bfc03e)

#### AWS digital cloud training - 25 May 

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/0c3aac14-a100-4621-93a1-0204294d42b3)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/ce58e079-f616-4cfc-b645-49d1757966bb)

With horizontal scaling each instance is identical to the AMI it is linked to.

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/018eeccb-7784-4cea-9560-93a1e774c8e6)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/44892a58-509b-40ca-9e17-429c49836590)


### DNS server
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/278a7e26-dd88-4bc6-9a70-f737c5051c43)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/dec85c1a-a56c-4043-8326-132ce2def084)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/20b9b8a3-dece-4773-91b3-421802147299)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/2c44ad17-4f35-4533-b551-6e761fb21f7c)

us-east-1a:
http://44.213.104.159/
us-east-1b:
http://34.239.133.144/

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/e2b9598b-1d2d-41df-ab2e-cbe7b6a021d4)


#### VPC and Networking + Infrastructure as Code
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/5ef0919c-e45d-471f-8b18-b23429881c5f)

So the CIDR blocks simply defines which part of the address space defines the network and which part is for hosts.  We can manipulate how many bits are available for hosts, to a degree.  There is a LOT more detail to this.

https://www.subnet-calculator.com

### Create a custom VPC

#### Security Groups vs Network ACLs

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/40fc22b7-d47c-4404-b449-7765f0c5bfa5)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/7bc8ff15-b99c-4fc5-a64e-c5a109a0844f)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/8cfb4e81-4b51-48ba-b906-140db3cab861)

### Nat gateway with Private EC2 instance
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/753b83db-5290-45c3-912b-aae9319cc2ff)

### Using VPC endpoints

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/aac9a44e-45be-4866-b7f3-a540c8da71b6)

### VPC Interface Endpoints
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/72810dd4-8e5c-4f12-aa71-e7504f71fc22)

### Gateway endpoints
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/dd74e349-76eb-4b22-876d-7db380ced7d3)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/d6bc1394-af25-4890-89db-9dec6d84c7c2)

#### Cloud Formation
https://aws.amazon.com/cloudformation/resources/templates/

![image](https://github.com/TomSpencerLondon/myPythonDockerRepo/assets/27693622/7d3cb624-3314-4e8b-b3e4-50c8b8cd67b9)


Note:
You could do something like this to add it to the user data:
#!/bin/bash
yum install -y httpd
systemctl start httpd
systemctl enable httpd
cat << EOF > /var/www/html/index.html
<!DOCTYPE html>
<html>
<head>
    <title>Static Website</title>
</head>
<body>
    <h1>S3 Website</h1>
    <p>This static website is running on Amazon S3</p>
</body>
</html>
EOF


### Overview of AWS services

#### Analytics
- Amazon Athena (query data in S3 using SQL)
- Amazon CloudSearch (managed search service)
- Amazon Kinesis (analyse real time video and data streams)
- Amazon OpenSearch Service (search, visualize and analyse petabytes of text and data)
- Redshift (data warehousing)

#### Cloud Financial Management
- AWS Budgets (set custom cost and usage budgets)
- AWS Cost and Usage Report (access comprehensive cost and usage information)
- AWS Cost Explorer (analyse cost and usage)
- Reserved instance reporting
- Savings plans

#### Compute
- Amazon EC2 (virtual servers in the cloud)
- Amazon EC2 auto scaling (scale compute capacity to meet demand)
- Amazon Elastic Container Service (scalable containers)
- Elastic Kubernetes Service (Kubernetes)
- Amazon Lightsail (launch private virtual servers)

#### Containers
- Elastic Container Registry (store containers)
- ECS 
- EKS
- AWS copilot - easiest way to launch and manage containerized application on AWS
- AWS Fargate - Serverless compute for containers

#### Database
- Amazon Aurora - high performance managed relational database
- Amazon DynamoDB - managed NoSQL database
- Amazon ElastiCache (in memory caching service)
- Amazon RDS (managed relational database service for MySQL, PSQL, Oracle, SQL Server, MariaDB)
- Amazon Redshift (data warehousing)

#### Frontend web and mobile
- Amazon API Gateway (manage APIs)
- AWS AppSync (accelerate app dev with GraphQL APIs)
- AWS Device Farm (test Android, IOS and web apps on real devices in the AWS cloud)
- Amazon Location Service (location data for applications)
- AWS Amplify (build, deploy, host and manage web and mobile apps)

#### Internet of Things
- AWS IoT Core (connect devices to the cloud)
- AWS IoT FleetWise (collect transform and transfer vehicle data to the cloud in real time)
- AWS IoT SiteWise (IoT data collector and interpreter)
- AWS IoT TwinMaker (optimize operations by creating digital twins of real-world systems)
- AWS IoT Greengrass (local compute, messaging and sync for devices)

#### Machine Learning
- Amazon Bedrock - foundation models
- Amazon Comprehend - insights and relationships in text
- Amazon polly - text into lif-like speech
- Amazon Rekognition - image and video analysis
- Amazon SageMAker - build, train and deploy machine learning models at scale

#### Networking and Content Delivery
- Amazon API gateway - build, deploy and manage APIs
- Amazon CloudFront - global content delivery network
- Amazon Route 53 - scalable domain name system
- Amazon VPC - isolated cloud resources
- Elastic Load Balancing (ELB) - distribute incoming traffic across multiple targets

#### Security, Identity and Compliance
- Amazon Cognito - Identity management for apps
- Amazon GuardDuty - managed threat detection service
- AWS Identity and Access Management - secure access management for services and resources
- AWS Key Management Service (KMS) Managed creation and control of encryption keys
- AWS WAF - filter malicious web traffic

#### Serverless
- Amazon S3 - object storage built to retrieve any amount of data from anywhere
- Amazon DynamoDB - managed NoSQL database
- AWS Lambda - Run code without thinking about servers
- AWS Fargate - serverless compute for containers
- Amazon API Gateway - build, deploy and manage APIs

#### Storage
- Amazon Elastic Block Store - EC2 block storage volumes
- Amazon Elastic File System (EFS) - file system management for EC2
- Amazon S3 - object storage
- AWS backup - centralised backup across AWS services
- AWS Storage Gateway - hybrid storage integration


### DNS, Auto Scaling and Load Balancing (Thursday 25 May 2023)
- Domain Name system
- EC2 Auto Scaling
- Elastic Load Balancing

#### Domain Name System
- DNS resolves IP address to a domain name

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/1e792f2c-f4e9-436b-8eee-18d95ee0ff7e)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/b105fd94-00ea-44dc-b85b-6d2775fd7ff7)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/520c8ca3-1e07-46a7-b8c5-7476eb145285)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/0b3078a1-3730-4fbc-9024-b6b8a473881c)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/1a60bbfb-6eac-465f-a2db-daca213654f2)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/910bd7d3-7d70-4939-b613-7654ff4bdf35)

### Scaling up vs out
- scaling up => more CPU, Ram / storage
- Some instances have 100s of CPUs
- Problem with instance is it leaves a lot to fail at once
- Scaling out is safer
- Scaling out => more instances
- scaling out has no limit - best practice

### Amazon EC2 Auto Scaling
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/7eec0fac-370e-4251-a435-fc3d7c793a4e)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/cbf7577d-ecd9-496f-8d05-b85b66707baf)


#### Create an Auto Scaling Group
These instructions are useful for deploying an auto scaling group:
https://github.com/TomSpencerLondon/digital-cloud/blob/main/6%20DNS%20-%20Auto%20Scaling%20and%20Load%20Balancing/Commands%20and%20Instructions.md


#### Accessing Services – Access Keys and IAM Roles

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/dd817ab2-d301-4ea0-8007-e58cc58a7899)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d4f46f66-4514-4c2a-a958-94320e27feb3)

#### Difference between Access Keys and IAM roles
IAM roles are better than access keys - they are more secure

#### EC2 Placement Groups
- Cluster - packs instances close together inside an AZ. This strategy enales workloads to achieve the low-latency network performance
necessary for tightly-coupled node-to-node communication that is typical of HPC applications
- Partition - spreads instances across logical partitions so that groups of instances in one partition
do not share the underlying hardware with groups of instances in different partitions. This strategy
is typically used by large distributed and replicated workloads, such as Hadoop, Cassandra and Kafka
- Spread - strictly places a small group of instances across distinct underlying hardware to reduce correlated failures

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/52cf187c-a9a6-463e-8873-b399c802efc8)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/e56d6d75-e474-4dc3-b5c3-3edfe57f543b)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b53185e3-3e1d-4dea-9ffa-2cad04955c61)

#### EC2 Placement Group Use Cases

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/037e481e-2258-41f1-82cb-2608e9ee248a)

#### Elastic Network interfaces (ENI, ENA, EFA)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b5fa87fb-3996-439a-9640-c43ee90ee5c6)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d34dab4c-179b-4ebc-a41e-c282f0aaa673)


#### Course overview (Solutions Architect Associate)
This is the link for the course:
https://digitalcloud.training/aws-saa-module-03-jun23-6b9d/

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/2c446fe5-2152-467d-a157-bea450a23928)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/ac139b01-a2ef-41f1-90eb-35bd3df12cd4)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/1f9d044c-8ee0-4592-8cf9-ac48385ff2e0)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/879fe60f-325c-43c9-891f-276b3691f0f3)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b55e7965-e614-49e6-8cce-e85bfa28277b)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d911cdd3-cfe7-4ab5-a846-cbe830fafb30)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b601711e-dc13-4ca5-b9cb-fb8779e77d3e)

The measurement of Availability is driven by time loss whereas the measurement of Reliability is driven by the frequency and impact of failures.

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b11b2bf9-c67b-46c3-9c84-36196cf4b896)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/7a229d0c-8203-473e-b223-b15e3caa3867)

Diagramming tools:
https://aws.amazon.com/architecture/icons/

Useful uptime calculator:
https://uptime.is/

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/1781ba43-4115-4511-8316-e2f03bbdfe98)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/10ca8e6f-5c14-4b75-8959-ab9528ea2519)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/1fea951f-7d95-4982-8981-6bc1ac77c646)

Low Recovery Point Objective and Recovery Time Objective both low for the above architecture. The cost is now $11770 a month.

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/844da54c-b035-4a1e-8a12-1b3c1e80dd1c)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b263ff8e-0051-40fe-bfbe-9060bf03557c)

#### AWS Pricing calculator
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/62c10347-4627-4fc6-97ba-fb5247aadd6d)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/f784502b-11e1-4b64-8638-9c5be69bb64e)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d300c9d0-51f8-4b7b-81b4-313aa34019af)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/b5aab24e-b760-4d98-9816-4b39c5ccd794)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/fa8cdb9d-9508-466a-a658-c0d88dab891a)

### Public, Private and Elastic IP Addresses
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/7118b53b-1650-48d7-9959-146d7f85baa5)

### NAT for Public Addresses
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/0552d364-b582-4206-a639-45444248a3f0)


### Private subnets and bastion hosts
Creating a bastion host involves connecting to the instance in the public subnet which then connects to the private subnet.
We will look at implementing this in this next section.
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/48536d52-0548-4826-a2ec-e8e2a8b9dc1e)

This link is useful for setting up a bastion host to a server in a private subnet:
https://digitalcloud.training/ssh-into-ec2-in-private-subnet/

```bash
tom@tom-ubuntu:~/Desktop$ chmod 400 my-ec2.pem
tom@tom-ubuntu:~/Desktop$ ssh-add my-ec2.pem
Identity added: my-ec2.pem (my-ec2.pem)
tom@tom-ubuntu:~/Desktop$ ssh -A ec2-user@172.31.9.49
^C
tom@tom-ubuntu:~/Desktop$ ssh -A ec2-user@100.27.48.209
The authenticity of host '100.27.48.209 (100.27.48.209)' can't be established.
ED25519 key fingerprint is SHA256:gXZaCc4wZjNhvgjwcEgZGYNW4aoZs1YbLR1jeGqzG9w.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '100.27.48.209' (ED25519) to the list of known hosts.
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
[ec2-user@ip-172-31-9-49 ~]$ ssh ec2-user@172.31.81.221
The authenticity of host '172.31.81.221 (172.31.81.221)' can't be established.
ED25519 key fingerprint is SHA256:rhrP5HeIPq9XJMAnkEcUA6ssVCp5tuZMs1HVCg0mkYA.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '172.31.81.221' (ED25519) to the list of known hosts.
   ,     #_
   ~\_  ####_        Amazon Linux 2023
  ~~  \_#####\
  ~~     \###|
  ~~       \#/ ___   https://aws.amazon.com/linux/amazon-linux-2023
   ~~       V~' '->
    ~~~         /
      ~~._.   _/
         _/ _/
       _/m/'
[ec2-user@ip-172-31-81-221 ~]$ 

```
Here we have connected to our public instance and then used this public instance to connect to the instance in the
private subnet.

#### NAT Gateways and NAT Instances Overview

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d1865c68-a5af-4f42-8df8-50f3ba191ff6)

#### Nat instances are not used as much as NAT gateways

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/58a40388-570f-4e87-a591-5202ff6764c6)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/0ead298d-d865-4f1b-8195-b6197a51c425)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/576f4ec4-66b9-49b3-81d9-1620d5493fd6)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/12ab4fd8-161f-40cd-a2ca-2e355e3c7a0a)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/d51c2eb9-e47c-4d82-bdc1-dfc07d98eb15)

### Nitro instances and Nitro enclaves
https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/instance-types.html
- Nitro is the underlying platform for the next generation of EC2 instances
- Support for many virtualized and bare metal instance types
- Breaks functions into specialized hardware with a Nitro Hypervisor
- Specialized hardware including:
  - Nitro cards for VPC
- Performance, security and innovation - HPC
- Nitro Enclaves - security - isolated and hardened virtual machines

### EC2 Pricing Options

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/bb3f4581-546d-4ba9-b479-d82ae648c422)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/f471339e-dac9-4b13-88bb-fb35a2773447)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/6b71b297-7a06-4f38-aa60-e71a1264e8cf)

### Architecture patterns - Amazon EC2

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/5ab3c2ce-e2f8-4e8e-a482-89ffd5754b52)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/ca9e1a11-4c1f-4df8-a4c5-40e3b4c7574b)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/2814d9ac-e125-4bb6-837a-02ce09ce1019)

### Exam Cram EC2

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/ad6d15c6-1736-4a0c-9759-4d171d6336a5)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/04152070-3274-4c52-8cee-0c6d30d192c1)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/aa389c90-63eb-4507-b967-3c090d68b605)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/082c85c3-2d37-4b65-8e7b-811131a5b5a2)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/72f12c5d-8dba-4bca-94b3-98e6d859396e)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/83b8b69f-3f14-42e3-a539-4efd2d5a06ab)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/e07d9fbe-8b94-42c7-9522-0cfa3fdb7e76)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/7664277c-6110-407d-a010-4c3b92baa6d2)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/35348a61-62f5-4eb4-a7a2-174a216b1b5c)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/c43cc355-d1df-42d7-a5b7-cb7e44f02c92)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/bfe9fe94-b020-498d-97b7-fad936036650)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/74220958-83ec-4a2e-a012-2bda42bde50c)

![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/01636657-06e8-4fc3-998f-275bbecf7031)

#### Cheat sheets
https://digitalcloud.training/amazon-ec2/

https://digitalcloud.training/amazon-vpc/

Build your skills, test yourself, and practice in a sandbox environment with the following recommended Challenge Labs:

Can You Implement a Repeatable Compute Layer with EC2? [Advanced]
Connect to an EC2 Instance by Using RDP [Guided]
Create a Custom AMI from an Existing EC2 Instance [Guided]
Can You Configure a Resilient EC2 Server Farm? [Expert]


### Migrate to AWS
https://cloudacademy.com/course/aws-migration-services-4036/aws-migration-services/?context_id=125&context_resource=lp

#### 3 stage migration
#### Assess 
- first stage of migration
- helps to determine how prepared you are as an organization to begin migration to AWS
- Enables formulation of goals and objectives
- Present an effective business case
- Services:
  - Migration evaluator
  - AWS Migration Hub
#### Mobilize
- Emphasis on detailed migration planning and strategy
- helps to define migration strategies consiting of
  - relocate
  - repurchase
  - rehost
  - retire
  - replatform
  - retain
  - refactor
- Identifies any skills gaps in your workforce
- Services
  - AWS Application Discovery Service
  - AWS control tower
#### Migrate and Modernize
- used to design your deployments and solutions
- identify any dependencies and understand interconnectivity required
- validate designs
- Services
  - AWS migration for DBs and applications
    - AWS Application Discovery Service
    - AWS Database Migration Service (AWS DMS)
  - AWS migration services used for migration of data:
    - AWS Transfer family
    - AWS dataSync
    - AWS Snow Family 
    - AWS Service Catalog

#### Assess
- AWS migration evaluator
  - baseline premises environment
  - projects cost using cost modeling and data analysis
  - accelerates successful digital transformation to AWS
- compute storage and microsoft licenses
- keep expenditure low for migration
- recommendations of resources
- reduce costs by 50%
- Agentless collector tool
- AWS Migration Hub
  - Quick insight report
  - highlighting recommendations
  - projected and expected costs
  - issues found
  - Dashboard overview of migration project
  - discover and migrate services
  - Powerful for overview
  - Run from AWS management console
  - discover and audit server inventory
  - Migration hub import
  - Migration evaluator collector
  - AWS Agentless Discovery Connector
  - AWS Application discovery agent
- Understand the environment
#### Mobilize (Migration and strategy planning)
Two services:
  - AWS Application Discovery Service
    - AWS Migration Hub
    - Amazon Athena
    - Amazon QuickSight
    - Agent based discovery / agentless discovery
    - Agent installed across fleet of servers
    - When Agent registered - connects to AWS Application discovery and AWS Migration hub
      - TLS connection
    - Agentless discovery - AWS discovery connector
    - Gets information on each VM
    - Connector with .ova file connects to AWS Application discovery service + AWS migration hub - only every 60 minutes
  - AWS Control Tower
    - Multi account strategy for migration
    - Landing zone - multi account architecture follows well architected framework
    - Created from a series of blueprints
    - Root OU, Core OU 

#### Migrate and Modernize 
Servers, database and applications
  - AWS Application Migration Service
  - AWS Database Migration Service
  - AWS Service Catalog

Data Migration
  - AWS Transfer family
  - AWS DataSync
  - AWS Storage Gateway
  - AWS Snowball Edge

#### AWS Application migration service
- migrate applications with minimal downtime and interruption
- Lift and shift approach
- Converts physical machines to run on AWS
- AWS replication service - workflow for the migration

#### AWS Database Migration Service
- Effective migration for databases
- From and to databases
- Move data to Amazon Redshift
- AWS Schema conversion tool
- Endpoints created - replication tasks to move data
- AWS service catalog
  - Allows end users to select pre-approved services
- AWS Service Catalogue

#### AWS DataSync
- service allows easy transfer of data from on premises
- AWS S3
- AWS Amazon Elastic File System
- Amazon FSx for Windows File Server
- AWS Snowcone


### IAM Access - 7/6/23
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/dfb19158-a67f-4234-a4e5-5abf7d0db98d)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/7ba2a7e4-b722-476a-b64a-bf00b9da5230)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/5db637c6-5ed6-4925-94a1-2d967f518ea3)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/f50dfcbb-a338-4f74-ab56-140ba567214b)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/962df06f-028d-4e42-8703-87ab9c10fcf3)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/c28da505-62da-4e2a-8678-8edaf2a5abd6)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/6d277e7b-93a4-40fd-af0a-901ab9e1c2a2)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/99717083-55ba-4852-8126-21cd55d7195a)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/3a4d269b-723e-40c4-9f6e-bd98b57efa60)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/0b8e7ca4-0ed7-418c-8979-c2c879c4293b)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/90c36c5d-6832-4f8e-ab6f-268eee5686cf)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/8620be69-1293-4fe6-9d4b-185d536baac0)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/8d73cdde-a103-47c0-89e1-8943a264436a)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/717b1a2a-82be-4ef1-b5a3-622770dfce2f)

### Auto Scaling, Amazon ELB (Saturday 10/6/23)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/cba2f6d7-fa92-4e08-b7f2-0e4c49cb78ac)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/76774039-9aa0-431e-88ac-1947c50bddf5)

![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/6651897b-68c5-49a4-b71f-6a13970fa78a)


