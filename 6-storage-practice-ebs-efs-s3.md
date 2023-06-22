# Lab 1: Working with EBS 

## Launch instances in multiple AZs
1. Create a security group
aws ec2 create-security-group --group-name StorageLabs --description "Temporary SG for the Storage Service Labs"
2. Add a rule for SSH inbound to the security group
aws ec2 authorize-security-group-ingress --group-name StorageLabs --protocol tcp --port 22 --cidr 0.0.0.0/0
3. Launch instance in US-EAST-1A
aws ec2 run-instances --image-id ami-0aa7d40eeae50c9a9 --instance-type t2.micro --placement AvailabilityZone=us-east-1a --security-group-ids sgr-0f14c1ff8a6d0f719
4. Launch instance in US-EAST-1B
aws ec2 run-instances --image-id ami-0aa7d40eeae50c9a9 --instance-type t2.micro --placement AvailabilityZone=us-east-1b --security-group-ids sg-031bb5986b09d8ebc

## Create and Attach an EBS Volume
1. Create a 10GB gp2 volume in us-east-1a with a name tag of 'test-volume-1'
2. List non-loopback block devices on instance
sudo lsblk -e7
3. Attach the volume to the instance in us-east-1a
4. Rerun the command to view block devices

## Create a filesystem and mount the volume
1. Create a filesystem on the EBS volume
sudo mkfs -t ext4 /dev/xvdf
2. Create a mount point for the EBS volume
sudo mkdir /data
3. Mount the EBS volume to the mount point
sudo mount /dev/xvdf /data
4. Make the volume mount persistent
Run: 'sudo nano /etc/fstab' then add '/dev/xvdf /data ext4 defaults,nofail 0 2' and save the file

# Lab 2: Working with EFS

## Create an EFS File System
1. Add rule to the security group to allow the NFS protocol from group members
aws ec2 authorize-security-group-ingress --group-id sg-031bb5986b09d8ebc --protocol tcp --port 2049 --source-group sg-031bb5986b09d8ebc
2. Create an EFS file system through the console, and add the StorageLabs security group to the mount targets for each AZ


## Mount using the NFS Client (perform steps on both instances)
1. Create an EFS mount point
mkdir ~/efs-mount-point
2. Install NFS client
sudo yum -y install nfs-utils
3. Mount using the EFS client
sudo mount -t nfs4 -o nfsvers=4.1,rsize=1048576,wsize=1048576,hard,timeo=600,retrans=2,noresvport fs-023adf2979936b0be.efs.us-east-1.amazonaws.com:/ ~/efs-mount-point
4. Create a file on the file system
5. Add a file system policy to enforce encryption in-transit
6. Unmount (make sure to change directory out of efs-mount-point first)
sudo umount ~/efs-mount-point
4. Mount again using the EFS client (what happens?)

## Mount using the EFS utils (perform steps on both instances)
1. Install EFS utils
sudo yum install -y amazon-efs-utils
2. Mount using the EFS mount helper
sudo mount -t efs -o tls fs-023adf2979936b0be.efs.us-east-1.amazonaws.com:/ ~/efs-mount-point

# Lab 3: Working with S3

1. Upload object using the s3api (change bucket and path)
aws s3api put-object --bucket my-bucket --key my-object-key --body /path/to/local/file
2. Download an object using the s3api (change bucket and path)
aws s3api get-object --bucket my-bucket --key my-object-key /path/to/local/file
3. Use curl with a presigned URL to download an object (change bucket, path, and object)
curl -o /path/to/local/file "$(aws s3 presign s3://my-bucket/my-object-key --expires-in 3600)"
4. Use a Python script to upload an object

***Run the following commands:***

sudo yum install python3-pip
pip3 install boto3
nano upload_to_s3.py ***and add code (below), then save***
python3 upload_to_s3.py


***Python Code - update BUCKET-NAME and FILE-NAME***

import boto3

s3 = boto3.resource('s3')

# Replace with your S3 bucket name and the name to give the object
bucket_name = 'BUCKET-NAME'
object_name = 'FILE-NAME'

# Replace with the local file path to upload
file_path = '/home/ec2-user/FILE-NAME'

# Upload the file to S3
try:
    s3.Bucket(bucket_name).upload_file(file_path, object_name)
    print('Successfully uploaded {} to s3://{}/{}'.format(file_path, bucket_name, object_name))
except Exception as e:
    print('Error uploading file to S3: {}'.format(e))