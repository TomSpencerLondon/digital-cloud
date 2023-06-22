# Lab 1: Create SCP for Restricting EC2 Instance Types

==This lab requires that you have completed the HOL "Create Organization and add Account"==

1. In AWS Organizations, enable Service Control Policies (SCPs)
2. Create a policy called "RequireT2Micro"
3. Enter the following JSON code:

***Code for the SCP***

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "RequireMicroInstanceType",
      "Effect": "Deny",
       "Action": "ec2:RunInstances",
      "Resource": "arn:aws:ec2:*:*:instance/*",
      "Condition": {
        "StringNotEquals":{               	
          "ec2:InstanceType":"t2.micro"
        }
      }
    }
  ]
}
```

4. Attach the SCP to the OU that contains the production account (OU1)
5. Switch roles to administer the production account

## Switch role using:
Account number of the production account
Role: OrganizationAccountAccessRole

6. In Amazon EC2 attempt to launch a t2.micro instance. It should work
7. Next, attempt to launch any other EC2 instance type. It should fail
8. Switch back to the management account, and attach the SCP to the Root OU
9. Attempt to launch a t2.medium instance. What happens?

# Lab 2: Create VPC and Subnets

1. Create a VPC choosing the "VPC and more" option
2. Provide a name for the VPC
3. Create 1 NAT Gateway per AZ
4. Use defaults for other selections

# Lab 3: Security Group Practice

***all actions to be performed using the VPC created in Lab 2***

1. Create a security group called SGA
2. Create a security group called SGB
3. Configure security group rules:

## Rules for SGA
Inbound HTTP from anywhere
Inbound SSH from anywhere
Outbound SSH to SGB
Outbound All ICMP - IPv4 to SGB
Outbound HTTP and HTTPS to anywhere

## Rules for SGB
Inbound All ICMP - IPv4 from SGA
Inbound SSH from SGA
Outbound - NONE

4. Launch an instance in a public subnet, use SGA, and add the following user data:

***user data for public instance***

```bash
#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
```

5. Launch an instance in a private subnet, use SGB, make sure to attach a key pair
6. Attempt to make the following connections:

## 1. Connections tests (all should work)
- Connect via web browser to public IP of public instance (should display the Apache test page)
- Connect via EC2 instance connect to public instance
- Ping the private instance's private IP address from the public instance
- Using the key pair info, connect via SSH to the private instance

### Connection commands:
Create a file on the public instance with the contents of the key pair you downloaded (same file name)
Run sudo chmod 600 <filename>
Run ssh -i <filename> ec2-user@<ip address>

## 2. Connection tests from the private instance (should not work)
- Ping google.com from the private instance
- Ping the public instance's private IP from the private instance
- Attempt to retrieve the Apache test page using 'curl http://<private ip of public instance>'

7. Fix the above issues securely

# Cleanup

1. Terminate the EC2 instances
2. Delete the VPC