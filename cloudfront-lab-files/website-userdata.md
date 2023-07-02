#!/bin/bash
yum update -y
yum install -y httpd
systemctl start httpd
systemctl enable httpd
cd /var/www/html
aws s3 cp s3://s3-website-123456743/index.html ./
aws s3 cp s3://s3-website-123456743/website.css ./
cp index.html index.txt
rm -rf index.html
EC2AZ=$(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/placement/availability-zone) 
sed "s/AZID/$EC2AZ/" /var/www/html/index.txt > /var/www/html/index.html