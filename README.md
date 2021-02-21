# Inframind_5_Round_2

## Hosting WordPress on AWS

This architecture provides a set of YAML templates for deploying WordPress stack on AWS using following AWS Services :-
 AWS Services | 
--- | 
Amazon Virtual Private Cloud (Amazon VPC) | 
Amazon Elastic Compute Cloud (Amazon EC2) |
Auto Scaling |
Elastic Load Balancing (Application Load Balancer) |
Amazon Relational Database Service (Amazon RDS) |
Amazon Elastic File System (Amazon EFS) |
Amazon CloudFront |
AWS CloudFormation |

## Architecture Digram
![Architecture Digram](https://github.com/ranjsa/Inframind_5_Round_2/blob/main/aws_arch_v1.png)

## Steps to Run Wordpress Stack
* The repository consists of a set of nested templates which are executed in order from the master template. Run the master template to create the entire stack, entering the appropriate parameters. Nested templates can be run individually in order, entering the appropriate input parameters for each stack.
* To launch the entire stack and deploy a WordPress site on AWS, click on one of the Launch Stack links below or download the Master template and launch it locally.

| AWS Region Code | Name | Launch |
| --- | --- | --- 
| us-east-1 |US East (N. Virginia)| [![cloudformation-launch-stack](images/cloudformation-launch-stack.png)](https://console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/new?stackName=WordPress&templateURL=https://s3.amazonaws.com/aws-refarch/wordpress/latest/templates/aws-refarch-wordpress-master-newvpc.yaml) |

* Override PHP.ini defaults by downloading an overrides ini file from Amazon S3
https://mywordpressbalti.s3.amazonaws.com/20-aws.ini

### Input Parameters

#### General AWS
- EC2 Key Name Pair
- SSH Access CIDR block (to access bastion host)
- Email address for WordPress administration and SNS notifications
- Site Domain Name (e.g. 'example.com') - use this only if you will use your own custom domain name
- Select if you want to create a DNS record set for your custom dist domain name
- Select if you want to use a AWS CloudFront to cache images at AWS edge locations

#### Network
- Number of Avilability Zones (AZs) - from 2 to 6
- Select the individual Availability Zones where resources will be launched (the number of selected AZs must match the number of AZs selected above)
- VPC CIDR block
- CIDR block for public subnets 1 to 6
- CIDR block for web subnets 1 to 6
- CIDR block for data subnets 1 to 6

#### File System
- Amazon EFS performance mode
- Encrypted file system (boolean)
- AWS KMS Customer Master Key ARN (if enabling encryption and using customer-managed CMK)
- Clone EFS, use the System ID of an existing EFS Filesystem. The data is copied to the Site Directory. If the Site Directory exists on the source filesystem, only the contents of the site directory are copied. So make sure if you are copying from an existing Wordpress Cloudformation template, that the site directory is the same.
- Add dummy data to the file system to achieve higher throughput & IOPS beyond the amount of data your WordPress environment will use. This value is in GiB.
- The instance type that will be used to dd dummy data into the file system
- Select if you want to create alarms that send SNS notifications when the file system's burst credit balance drops below certain thresholds.
- The instance type that will be used to dynamically adjust alarm thresholds as permitted throughput changes.
- The 'Warning' threshold has a default value of 180 minutes. This means that a CloudWatch alarm will send an email notification 180 minutes before the credit balance drops to zero, based on the latest permitted throughput rate.
- The 'Critical' threshold has a default value of 60 minutes. This means that a CloudWatch alarm will send an email notification 60 minutes before the credit balance drops to zero, based on the latest permitted throughput rate.

#### Database
- Database Name
- Database Master Username
- Database Master Password
- DB Restore from Snapshot, enter the cluster Snapshot name from the RDS console. (formatted rds:wordpress-stack-name-rds-xxxxxxxx-databasecluster-apzdbrozmzcn-snapshot-date)
- Database Size
- Database Instance Class Type
- Encrypted database storage (boolean)
- AWS KMS Customer Master Key ARN (if enabling encryption and using customer-managed CMK)
- Create ElastiCache cluster (boolean)
- ElastiCache Node Type

#### Web Parameters
- Create CloudFront distribution (boolean)
- Create Route 53 record set (boolean)
- PHP Version (5.5, 5.6, or 7.0 - recomended)
- Web Instance Type
- The maximum number of instances in the web tier auto scaling group
- The minimum (and desired) number of instances in the web tier auto scaling group

#### WordPress Parameters
- WordPress Title
- WordPress Administrator Username
- WordPress Administrator Username Password
- WordPress Main Language of the site
- Wordpress Site Directory

## Master Template
The master template receives all input parameters and passes them to the appropriate nested template which are executed in order based on dependencies.
Review the template here [aws-master.yaml](https://mywordpressbalti.s3.amazonaws.com/master-newvpc.yaml)

## New VPC Template
Review the template here [aws-newvpc.yaml](https://mywordpressbalti.s3.amazonaws.com/newvpc.yaml)

### Default VPC and subnet IP ranges

The 'newvpc' stack defaults to the following network design (but these can be changed via master parameters):

| Item | CIDR Range | Usable IPs | Description |
| --- | --- | --- | --- |
| VPC | 10.0.0.0/16 | 65,536 | The whole range used for the VPC and all subnets |
| Web Subnet | 10.0.0.0/22 | 1022 | Private subnet in first Availability Zone |
| Web Subnet | 10.0.4.0/22 | 1022 | Private subnet in second Availability Zone |
| Web Subnet | 10.0.8.0/22 | 1022 | Private subnet in third Availability Zone |
| Web Subnet | 10.0.12.0/22 | 1022 | Private subnet in fourth Availability Zone |
| Web Subnet | 10.0.16.0/22 | 1022 | Private subnet in fifth Availability Zone |
| Web Subnet | 10.0.20.0/22 | 1022 | Private subnet in sixth Availability Zone |
| Data Subnet | 10.0.100.0/24 | 254 | Private subnet in first Availability Zone |
| Data Subnet | 10.0.101.0/24 | 254 | Private subnet in second Availability Zone |
| Data Subnet | 10.0.102.0/24 | 254 | Private subnet in third Availability Zone |
| Data Subnet | 10.0.103.0/24 | 254 | Private subnet in fourth Availability Zone |
| Data Subnet | 10.0.104.0/24 | 254 | Private subnet in fifth Availability Zone |
| Data Subnet | 10.0.105.0/24 | 254 | Private subnet in sixth Availability Zone |
| Public Subnet | 10.0.200.0/24 | 254 | Public subnet in first Availability Zone |
| Public Subnet | 10.0.201.0/24 | 254 | Public subnet in second Availability Zone |
| Public Subnet | 10.0.202.0/24 | 254 | Public subnet in third Availability Zone |
| Public Subnet | 10.0.203.0/24 | 254 | Public subnet in fourth Availability Zone |
| Public Subnet | 10.0.204.0/24 | 254 | Public subnet in fifth Availability Zone |
| Public Subnet | 10.0.205.0/24 | 254 | Public subnet in sixth Availability Zone |

## Security Groups Template
Review the template here [aws-securitygroups.yaml](https://mywordpressbalti.s3.amazonaws.com/dashboard.yaml)

## Amazon EFS File System Template
Review the template here [aws-efsfilesystem.yaml](https://mywordpressbalti.s3.amazonaws.com/efsfilesystem.yaml)

## Amazon EFS Alarms Template
Review the template here [aws-efsalarms.yaml](https://mywordpressbalti.s3.amazonaws.com/efsalarms.yaml)

## Amazon ElastiCache Template
Review the template here [aws-elasticache.yaml](https://mywordpressbalti.s3.amazonaws.com/elasticache.yaml)

## Amazon Elastic Load Balancing - Application Load Balancer Template
Review the template here [aws-publicelb.yaml](https://mywordpressbalti.s3.amazonaws.com/publicalb.yaml)

## Amazon RDS Template
Review the template here [aws-rds.yaml](https://mywordpressbalti.s3.amazonaws.com/rds.yaml)

## Amazon CloudFront Template
Review the template here [aws-cloudfront.yaml](https://mywordpressbalti.s3.amazonaws.com/cloudfront.yaml)

## WordPress Web Template
Review the template here [aws-web.yaml](https://mywordpressbalti.s3.amazonaws.com/web.yaml)

## Amazon Route 53 Template
Review the template here [aws-route53.yaml](https://mywordpressbalti.s3.amazonaws.com/route53.yaml)

## Amazon CloudWatch Dashboard Template
Review the template here [aws-dashboard.yaml](https://mywordpressbalti.s3.amazonaws.com/dashboard.yaml)
