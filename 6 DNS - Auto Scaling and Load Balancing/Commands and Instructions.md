# Lab 1 - Create DNS Records


## User data for launching 2 instances in different AZs

#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
EC2AZ=$(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/placement/availability-zone)
echo '<center><h1>This Amazon EC2 instance is located in Availability Zone: AZID </h1></center>' > /var/www/html/index.txt
sed "s/AZID/$EC2AZ/" /var/www/html/index.txt > /var/www/html/index.html

- In the security group allow ICMPv4 (all) and port 80 inbound

## Create DNS records and test resolution

1. Create the A, MX, and CNAME records as per the image
2. Test resolving the names using nslookup, ping, and a browser

To retrieve specific records types set the type in the CLI, e.g.:
set type=MX
set type=A
set type=CNAME
set type=NS

# Lab 2 - Create an Auto Scaling Group

## Launch EC2 static website and create an AMI

1. Copy index.html and website.css from the zip file into an S3 bucket
2. Launch an EC2 instance with an IAM role for S3 permissions and the user data below

### Change the bucket name

#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
cd /var/www/html
aws s3 cp s3://YOUR-BUCKET-NAME/index.html ./index.txt
aws s3 cp s3://YOUR-BUCKET-NAME/website.css ./website.css
EC2AZ=$(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/placement/availability-zone)
sed "s/AZID/$EC2AZ/" /var/www/html/index.txt > /var/www/html/index.html

3. Create an AMI from the resulting instance

## Create a Launch Template and Auto Scaling Group

1. Create a Launch Template that uses the AMI
2. Create an Auto Scaling group that uses the launch template
3. Attach at least 3 AZs and launch 3 instances


# Lab 3 - Attach an Elastic Load Balancer

1. Create a Target Group and attach it to the Auto Scaling group
2. Create an Application Load Balancer across all AZs that have instances

# Lab 4 - Add a Custom Domain

1. Use Amazon Route 53 to create an alias to the load balancer
2. Test using the custom domain name

