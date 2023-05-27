# Lab 1: Create a Custom VPC

1. Create a VPC choosing the "VPC and more" option
2. Provide a name for the VPC, e.g. lab1-custom-vpc
3. No NAT Gateway
4. Use defaults for other selections
5. Modify the public subnets to auto assign IPv4 addresses

# Lab 2: Security Groups and NACLs

***all actions to be performed using the VPC created in Lab 1***

1. Create a security group called SGA
2. Create a security group called SGB
3. Configure security group rules:

## Rules for SGA
Inbound HTTP from anywhere
Inbound SSH from anywhere
Outbound - leave the default rule to allow all traffic anywhere

## Rules for SGB
Inbound HTTP from anywhere
Inbound SSH from anywhere
Inbound All ICMP - IPv4 from SGA
Outbound - leave the default rule to allow all traffic anywhere

4. Launch two instances in the same or separate public subnets within the custom VPC. Add one to SGA and one to SGB

- Use the following user data:

***user data for instances***

#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd

6. Attempt to make the following connections (all should work):

- Connect via web browser to public IP each instance (should display the Apache test page)
- Connect via EC2 instance connect to each instance
- Ping the the private IP address of the instance in SGB from the instance in SGA
- Attempt to retrieve the Apache test page on the instance in SGA from the instance in SGA using 'curl http://<public ip of public instance>'

7. Make the following changes:
- Enable inbound HTTP access only from your home IP address for SGA (then test)
- Use a NACL to deny traffic from your home IP (then test again)
- Update SGB so that it only allows HTTP from SGA
- Enable ICMP in both directions

# Lab 3 - Deploy a NAT Gateway

1. Deploy a NAT gateway in a public subnet of the custom VPC
2. Update the route table of the private subnets
3. Launch an instance in a private subnet using the user data from lab 2
4. Update security groups as needed
5. Test you can access the webpage on the private instance using curl and the private IP (from a public instance)

# Lab 4 - Test the S3 Gateway Endpoint

1. Launch an instance in a public subnet of the VPC with S3 read only permissions
2. Connect to the instance using EC2 Instance Connect 
3. Run 'aws s3 ls' - you should receive a list of buckets
4. Edit the policy on the S3 gateway endpoint to change 'Allow' to 'Deny'
5. Run 'aws s3 ls' again - it should not have changed (should work)
6. Edit the S3 gateway endpoint to add the public route table
7. Run 'aws s3 ls' again - this time access should be denied
8. Revert the policy and it should start working again (via the S3 gateway endpoint)

# Cleanup

1. Terminate the EC2 instances
2. Delete the NAT gateway and release the elastic IP
3. Delete the VPC


# Lab 5: Deploy a Load-balanced Auto Scaling group with AWS CloudFormation

1. Create an AWS CloudFormation stack using the following tempalte file:

https://s3.us-west-2.amazonaws.com/cloudformation-templates-us-west-2/ELBWithLockedDownAutoScaledInstances.template
