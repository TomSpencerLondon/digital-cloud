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
aws s3 cp s3://my-website-data-weq2ed/index.html ./index.txt
aws s3 cp s3://my-website-data-weq2ed/website.css ./website.css
EC2AZ=$(TOKEN=`curl -X PUT "http://169.254.169.254/latest/api/token" -H "X-aws-ec2-metadata-token-ttl-seconds: 21600"` && curl -H "X-aws-ec2-metadata-token: $TOKEN" -v http://169.254.169.254/latest/meta-data/placement/availability-zone)
sed "s/AZID/$EC2AZ/" /var/www/html/index.txt > /var/www/html/index.html

## Create a Launch Template and Auto Scaling Group

1. Create a Launch Template with the above user data
    - needs instance profile for S3 admin or read only
    - also needs user data
2. Create an Auto Scaling group that uses the launch template
    - EC2 AutoScaling groups
    - Create Auto Scaling group
    - ASG1 for name
    - Use the launch template we created
    - Don't need to change VPC from default
    - Specify three availability zones (1a, 1b, 1c)
    - No Load balancer - we will do that later
    - Desired capacity: 3 (what you want it to be)
    - Minimum: 3 (lowest can go)
    - Maximum: 3 (most it can go)
    - create auto scaling group
You can also set a scaling policy if you want. We won't do that for the moment.

# Lab 3 - Attach an Elastic Load Balancer

1. Create a Target Group and attach it to the Auto Scaling group
2. Create an Application Load Balancer across all AZs that have instances

Next create a target group. Protocol and port should be 80 as that is what the instance are on.
Health checks on http /
Create target group. Go back to autoscaling groups. Select ASG1. Select load balancing - edit loadbalancing. Application, Network target groups for the load balancers and use the Target group created before.
Create the Application Loadbalancer. ALB1 for name. Scheme is internet facing. IPv4. Default vpc. Select 3 availability zones selected for auto-scaling.
Load balancer needs http protocol. Listener on port 80 and forward to Target group TG1 created earlier. The Target group should now have targets. They will start as initial and then move to healthy.

# Lab 4 - Add a Custom Domain

1. Use Amazon Route 53 to create an alias to the load balancer
2. Test using the custom domain name

Use alias to attach the load balancer with the below configuration:
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/f8396b84-9e03-4e21-be10-cb1b59b5937e)

We can then see the load balanced application in the browser:
![image](https://github.com/TomSpencerLondon/digital-cloud/assets/27693622/343f0137-631a-4eaa-8d57-8d9e316a0632)





#### My notes:
Clear negative DNS cache on computer:
sudo dscacheutil -flushcache; sudo killall -HUP mDNSResponder

18.209.210.214
54.198.226.251

```bash
tom@tom-ubuntu:~$ ping www.tomawslabs.link
PING www.tomawslabs.link (54.198.226.251) 56(84) bytes of data.
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=1 ttl=107 time=91.8 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=2 ttl=107 time=91.3 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=3 ttl=107 time=90.0 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=4 ttl=107 time=90.9 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=5 ttl=107 time=91.8 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=6 ttl=107 time=92.8 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=7 ttl=107 time=91.4 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=8 ttl=107 time=91.6 ms
64 bytes from ec2-54-198-226-251.compute-1.amazonaws.com (54.198.226.251): icmp_seq=9 ttl=107 time=91.7 ms
^C
--- www.tomawslabs.link ping statistics ---
9 packets transmitted, 9 received, 0% packet loss, time 8009ms
rtt min/avg/max/mdev = 89.970/91.478/92.796/0.722 ms
tom@tom-ubuntu:~$ nslookup
> www.tomawslabs.link
;; communications error to 127.0.0.53#53: timed out
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
Name:	www.tomawslabs.link
Address: 54.198.226.251
Name:	www.tomawslabs.link
Address: 18.209.210.214

```

mail server:
```bash
tom@tom-ubuntu:~$ nslookup
> set type=MX mail.tomawslabs.link
> exit

tom@tom-ubuntu:~$ nslookup
> set type=MX
> mail.tomawslabs.link
;; communications error to 127.0.0.53#53: timed out
Server:		127.0.0.53
Address:	127.0.0.53#53

Non-authoritative answer:
mail.tomawslabs.link	mail exchanger = 2 mailserver2.tomawslabs.link.
mail.tomawslabs.link	mail exchanger = 1 mailserver1.tomawslabs.link.
```