# Lab 1: Assume Role via AWS CLI

## Make sure you have at least one s3 bucket before doing this exercise

1. Create an AWS CloudShell environment
2. Create an IAM role with a custom trust policy (trust policy code below)
3. Attach the `AmazonS3ReadOnlyAccess` and `AWSCloudShellFullAccess` permissions policies
4. Practice switching to the role using the console
5. Test permissions through the console

***Trust policy required on the IAM role (insert user account ARN)***

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::YOUR-ACCOUNT-NUMBER:user/YOUR-USER-NAME"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

6. Test permissions using the CLI

```bash
aws s3 ls 
aws s3 mb s3://RANDOM-BUCKET-NAME
aws s3 rb s3://EXISTING-BUCKET-NAME
```

7. Assume Role Command (switch back first)

aws sts assume-role --role-arn arn:aws:iam::706054169063:role/my-s3-ro-role-tom --role-session-name sts-assumerole-test

8. Set temporary credentials (update values) | Use the "set" command on Windows

```bash
export AWS_ACCESS_KEY_ID=ASIA2IZA3UHTWB6H7HVW
export AWS_SECRET_ACCESS_KEY=hOFpP82PF/ugyK1rUV+gePDjB6raxNH/ZmgQOXoQ
export AWS_SESSION_TOKEN=IQoJb3JpZ2luX2VjEID//////////wEaCWV1LXdlc3QtMiJGMEQCIH7O8CzKVMK+wMNj+u9RfajBUsbFGsYqOoEDdJJqIDLWAiBo4/W6BmYSysdgmsSToxiQIuXfIu8pSFhnJ8Otc9AN7yqpAgjJ//////////8BEAIaDDcwNjA1NDE2OTA2MyIMHK+SsRwflcjOBcfIKv0BfA8tvYdiGkWgkWs0r9RMLPydHeBcoGFOI7GfyDTfdG8jnBgCabhp1OBSPHbAOxo0VDjpEdFpqyVL2mkm8GYNwfRzsz0iCVHmGmAqcb5rx2cob/39AGd4Tplt7nzGVuqNStJZhaHdWojmuBzxCsM6lAcy6HNoqktx2PLmlyowSF6tfA25bpSdu0yhkodDHwkqiIDcrXJJXuIqlTdW7KIpdvAopPpzqJO8tOHtjYYTkqfNzcOqYcQ75DysCYdtWrUuopEq14G9s3g/tgttWKi5ERAuFATUIh4wvBaas/RfX8xXDSwgoKRUEPHKD1M0z+rI51kNdfJMcA+34FWBzTCSvJmkBjqeAacN6IM+a/4qQZcuL/RjK8CgOIbTsLemiVsEFupGD2FA4jBpnvz4FZtfRifiwZQmYyi2FBNjxyZdvatRhCFtHpnyQqKbq9raYjA6v7XIwjQTaW8Wbp6b8JknS14yQiOyNJTIopaE2IjQ+UvOS6JohOR+93BU0MWQxIWCnJm/tgp/yF+s730+QMWRF+PXxaVMGASHFEMhlwafSQxeNYk3
```
***Replace "[ACCESS_KEY_ID]", "[SECRET_ACCESS_KEY]", and "[SESSION_TOKEN]" with the values returned in the "Credentials" block of the "sts assume-role" command***

9. Test Access
Run the commands again to create and delete a bucket. You should receive an access denied message.

```bash
aws s3 mb s3://RANDOM-BUCKET-NAME
aws s3 rb s3://EXISTING-BUCKET-NAME
```

10. Remove temporary security credentials & access keys

```bash
unset AWS_ACCESS_KEY_ID
unset AWS_SECRET_ACCESS_KEY
unset AWS_SESSION_TOKEN
```

# Lab 2: Delegate Access to S3 using the AWS CLI

## Exercise 1: Create IAM Role for EC2

1. Create an IAM role with a trust policy that uses the EC2 use case (or use the code below)
2. Attach the AmazonS3FullAccess permissions policy

## Exercise 2: Launch instance and assume role

1. Launch EC2 instance
aws ec2 run-instances --instance-type t2.micro --image-id <AMI-ID> --region us-east-1
2. Describe EC2 instance
aws ec2 describe-instances --instance-ids <INSTANCE-ID>
3. Create instance profile
aws iam create-instance-profile --instance-profile-name s3-instance-profile
4. Add role to instance profile
aws iam add-role-to-instance-profile --role-name sts-assumerole-test --instance-profile-name s3-instance-profile
5. Connect to EC2 Instance Connect and run the same S3 test commands as earlier. They should fail
6. Attach instance profile to EC2 instance
aws ec2 associate-iam-instance-profile --iam-instance-profile Name=s3-instance-profile --instance-id <INSTANCE-ID>
7. Back in EC2 instance connect, run the same S3 test commands as earlier. They should now work successfully
6. Terminate EC2 instance
aws ec2 terminate-instances --instance-ids <INSTANCE-ID>
7. Remove role from instance profile
aws iam remove-role-from-instance-profile --role-name sts-assumerole-test --instance-profile-name s3-instance-profile
8. Delete instance profile
aws iam delete-instance-profile --instance-profile-name s3-instance-profile


***Trust relationship for role (for EC2)***

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Principal": {
                "Service": "ec2.amazonaws.com"
            },
            "Action": "sts:AssumeRole"
        }
    ]
}
```

# Lab 3: Implement ABAC with EC2

1. Create an IAM user for testing purposes
2. Attach an inline policy with the permissions below
3. Launch an Amazon EC2 instance
4. Add a tag to the instance (modify the user name): Owner=YOUR-TEST-USER-NAME
5. Log in as the test user and attempt to start and stop the instance
6. Delete the tag and test again

***Permissions policy required on the IAM user - DO NOT MODIFY***

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "ec2:StartInstances",
                "ec2:StopInstances"
            ],
            "Resource": "arn:aws:ec2:*:*:instance/*",
            "Condition": {
                "StringEquals": {"aws:ResourceTag/Owner": "${aws:username}"}
            }
        },
        {
            "Effect": "Allow",
            "Action": "ec2:DescribeInstances",
            "Resource": "*"
        }
    ]
}
```

