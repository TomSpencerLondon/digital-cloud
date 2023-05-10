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

