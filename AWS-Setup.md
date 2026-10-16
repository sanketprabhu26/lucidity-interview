Assumptions: 
AWS accounts are setup and the VPC peering is already configured for this script to run. 
iam folder has two roles, these roles has to be attached to respective EC2 instances. 

AWS Accounts	
Controller Account (999999999999)
Role: AnsibleController 

Production Account (111111111111)	
Role: AnsibleTarget	

Staging Account (222222222222)
Role: AnsibleTarget

Controller account: 
Sample Trust policy:
json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "Service": "ec2.amazonaws.com" },
    "Action": "sts:AssumeRole"
  }]
}

Sample Permissions policy:
json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AssumeSpokeInventoryRoles",
      "Effect": "Allow",
      "Action": "sts:AssumeRole",
      "Resource": [
        "arn:aws:iam::111111111111:role/AnsibleTarget",
        "arn:aws:iam::222222222222:role/AnsibleTarget"
      ]
    },
    {
      "Sid": "ReadSshPrivateKey",
      "Effect": "Allow",
      "Action": "secretsmanager:GetSecretValue",
      "Resource": "arn:aws:secretsmanager:ap-south-1:999999999999:secret:ansible/ssh-key-*"
    }
  ]
}


Staging and Production account:
Sample Trust policy:
json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Effect": "Allow",
    "Principal": { "AWS": "arn:aws:iam::999999999999:role/AnsibleController" },
    "Action": "sts:AssumeRole"
  }]
}

Sample Permissions policy:
json
{
  "Version": "2012-10-17",
  "Statement": [{
    "Sid": "DynamicInventoryReadOnly",
    "Effect": "Allow",
    "Action": [
      "ec2:DescribeInstances",
      "ec2:DescribeTags",
      "ec2:DescribeRegions"
    ],
    "Resource": "*"
  }]
}

