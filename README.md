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

- how to design and build multi-tier web architectures with services such as Amazon EC2 Auto Scaling, Amazon Elastic Load Balancing (ELB), AWS Route 53, AWS Lambda, Amazon API Gateway and Amazon Elastic File System (EFS)
- how to create Docker container clusters on Amazon Elastic Container Services (ECS), set up serverless event-driven AWS Lambda Functions with Amazon API Gateway and Amazon Kinesis integrations, and geographically redundant database services with Amazon Relational Database Service (RDS)
- how to configure Amazon Virtual Private Clouds (VPC), subnets, and route tables and setup best practice Security Group configurations
- how to build repeatably and securely with AWS CloudFormation, set up a PaaS with AWS Elastic Beanstalk, configure Amazon S3 bucket policies and share data between multiple AWS accounts
- how to use application integration services including AWS Step Functions, Amazon MQ, SNS, SWF and SQS


### AWS Identity and Access Management

![iam_access (3)](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/0a518c9f-a587-4e6c-8712-406395416f1d)

The identity and access principals are used to check authentication. The principals can then create resources across AWS regions according
to the authorization of the principal.

![iam_management](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/fcd41e70-25e3-4ed5-ab77-411014ba5ef4)

#### Users, Groups, Roles and Policies

A group is a way of organizing users. We then attach the policy to the user group. The user gains the policies added to the group.
Identity based policies define the permissions applied to the group through the policy. Roles are used for delegation and are assumed.
Policies define the permissions for the identity or the resources they are associated with. The root user has full permissions. It is a best
practice to avoid using the root user account + enable MFA. You can create up to 5000 individual user accounts in IAM. Users have no permissions
by default. Each user will have a friendly name and an Amazon Resource Name (arn):
```bash
arn:aws:iam:6453234333532:user/Andrea
```

Access keys or username/password can be used for authentication. We collect users in a group and then apply the permissions to users using policies.
IAM roles are identities in IAM that have specific permissions. Roles are assumed by users, applications and services:
```bash
sts:AssumeRole
```
Once assumed, the identity becomes the role and gains the role's permissions. IAM policies are documents that define permisssions:
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

You can use Username and Password to log into the AWS Management Console. To log in through the CLI or API we use Access key ID and secret.
Multifactor authentication:
Something you know -> Something you have -> Something you are

### AWS Security Token Service (STS)

In order for an EC2 instance to access an application we can add an Instance Profile. In the profile we have a trust policy and a permissions policy.
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
Identity based policies are JSON permissions policy documents that control waht actions an identity can perform, on which resources, and under what conditions.
We can create inline policies for specific users and roles. We can also have managed policies which can be managed by AWS or the account owner. The managed policies
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

Resource based policies grant the specified principal permission to perform specific actions on the resource. For instance this policy allows the user
to perform any action on s3. We can also attach resource based policies to the IAM role. A trust policy is also an example of a resource based policy.

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

Here the permissions boundary means that although the user has full control of S3, CloudWatch, EC2 and IAM the permissions boundary means that she does not have permissions to
allow an application to use S3 for instance. You don't get granted permissions through a permissions boundary but permissions boundary controls the privilege escalation.

This is a useful overview of the logic for permissions:
![image](https://github.com/TomSpencerLondon/LeetCode/assets/27693622/4fcf03d6-06a9-4874-924e-dc400e90c7df)

