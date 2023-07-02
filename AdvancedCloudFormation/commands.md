# upload your cloudformation template to your AWS environment using the following command:

aws cloudformation create-stack --stack-name third-template-cli --template-body file://third_template.yaml

# check the status of your stack by running the following commands:

aws cloudformation describe-stacks --stack-name third-template-cli

# list stack resources 

aws cloudformation list-stack-resources --stack-name third-template-cli

# get the DNS of our load balancer using the ARN

aws elbv2 describe-load-balancers --load-balancer-arns arn:aws:elasticloadbalancing:us-east-1:706054169063:loadbalancer/app/third-Appli-15YPS2ILC41LW/eef070c020415d56 --query 'LoadBalancers[*].DNSName'

# replace the arn with your arn 

# curl our DNS name to check connectivity

while true; do curl http://third-Appli-15YPS2ILC41LW-514921866.us-east-1.elb.amazonaws.com; sleep 2; done

