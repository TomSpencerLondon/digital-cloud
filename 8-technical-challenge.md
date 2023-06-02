# Challenge 1

1. Create a custom VPC and configure with public subnets and internet connectivity. Use the details below:

- CIDR Block: 10.0.0.0/16
- Subnet 1:
    CIDR Block: 10.0.0.0/24
    Availability Zone: us-east-1a

- Subnet 2:
    CIDR Block: 10.0.1.0/24
    Availability Zone: us-east-1b

- Subnet 3:
    CIDR Block: 10.0.2.0/24
    Availability Zone: us-east-1c

2. Launch an EC2 instance in the custom VPC using the Amazon Linux AMI
3. Install a webserver with a custom webpage using the HTML below:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Success Page</title>
</head>
<body>
    <h1>Success!</h1>
    <p>Your website is displaying correctly</p>
</body>
</html>
```

4. Validate that you can successfully access the website from the internet using the instance's public IP or DNS name

# Challenge 2

1. Create an AMI from the instance used in the previous challenge
2. Create an Auto Scaling group that uses the AMI with 2 running instances
3. Place the ASG behind an Application Load Balancer
4. Configure a Route 53 record to point to the ALB using a custom domain
5. Validate that you can successfully access the website from the internet using the custom domain name

# Challenge 3

1. Create an Amazon S3 static website using the following HTML code:

```html
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
```

2. Make it public
3. Validate that you can access the static website using the S3 website endpoint

