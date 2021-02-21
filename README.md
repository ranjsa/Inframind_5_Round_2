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
