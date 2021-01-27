# Practice Test: AWS Multi Environment

## Outline
- This is a practical exam for building an infrastracture which have multiple environments.
- Please build a specific diagram based on the architecture diagram through AWS.
- The task has four phases which ensure that each phase is built correctly.
　
## Notes
- Please use the login information carefully when logging in to the Management Console in AWS.
- Please stop running EC2 and RDS when you're not using them.

## Infra configuration diagram
![diagram](aws-multi-environment.png) 

## PHASE 1

### 1. Task
- Make a management environment

### 2. Requirements
- VPC
    - vpc-practice-mng
        - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Subnet
    - subnet-practice-mng-public-1a
    - subnet-practice-mng-public-1c
        - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Internet Gateway
    - igw-mng
- Security Group
    - practice-bastion-sg
        - Allow to inbound ssh
- EC2
    - practice-bastion
        - Amazon Linux 2
        - t3.micro
        - EBS(8GB)
        - Start up an instance to subnet-practice-mng-public-1a
        - Attach(Link) to practice-bastion-sg
        - Tag EXCLUDE_TERMINATE:true
- EIP
    - Allocate practice-bastion as the Elastic IP

### 3. Checklist
- To construct the infrastracture configuration diagram according to the requirements.
- To ensure that the SSH connection to practice-bastion is successful by ec2-user.

## PHASE 2

### 1. Task
- Make developed the environment

### 2. Requirements
- VPC
    - vpc-practice-dev
    - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Subnet
    - subnet-practice-dev-public-1a
    - subnet-practice-dev-public-1c
    - subnet-practice-dev-dmz-1a
    - subnet-practice-dev-dmz-1c
    - subnet-practice-dev-private-1a
    - subnet-practice-dev-private-1c
    - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Internet Gateway
    - igw-dev
- NAT Gateway
    - natgw-dev
        - Start up an instance to subnet-practice-dev-dmz-1a
- Security Group
    - alb-dev-sg
        - Allow(permit) to HTTP inbound
    - ec2-dev-sg
        - Allow(permit) to HTTP from ALB
        - Allow(permit) to SSH from practice-bastion
    - rds-dev-sg
        - Allow(permit) to MySQL(3306) from EC2
    - VPC Peering
        - Connect to vpc-practice-mng
- EC2
    - practice-dev-web01
        - Amazon Linux 2
        - t3.micro
        - EBS(8GB)
        - Start up an instance to subnet-practice-dev-dmz-1a
        - Attach(Link) to ec2-dev-sg
        - Tag EXCLUDE_TERMINATE:true
    - practice-dev-web02
        - Amazon Linux 2
        - t3.micro
        - EBS(8GB)
        - Start up an instance to subnet-practice-dev-dmz-1c
        - Attach(Link) to ec2-dev-sg
        - Tag EXCLUDE_TERMINATE:true
- Setup
    - Install the middleware below to EC2 instance
        - Apache(2.4)
        - PHP(7.3)
    - Set up the virtualhost
        - example-dev.com (ServerName)
        - /var/www/vhost/example-dev.com/public_html (DocumentRoot)
        - Install wordPress(latest)
- ALB
    - alb-practice-dev
        - Start up to below subnet
            - subnet-practice-dev-public-1a
            - subnet-practice-dev-public-1c
        - Attach(Link) to alb-dev-sg
        - Attach(Link) to instance below
            - practice-dev-web01
            - practice-dev-web02
- RDS
    - practice-dev-db01
        - MySQL(5.7)
        - db.t3.micro
        - Storage(Auto-expanding on)
        - Start up to below subnet
            - subnet-practice-dev-private-1a
            - subnet-practice-dev-private-1c
        - Attach(Link) to rds-dev-sg

## 3. Checklist
- To construct the infrastracture configuration diagram according to the requirements.
- To be able to ssh Connection to practice-dev-web01 as a bastion host.
- To display wordpress official site when accessing to "example-dev.com" from any browsers.

## PHASE 3

### 1. Task
- Make a production environment

### 2. Requirements
- VPC
    - vpc-practice-prod
    - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Subnet
    - subnet-practice-prod-public-1a
    - subnet-practice-prod-public-1c
    - subnet-practice-prod-dmz-1a
    - subnet-practice-prod-dmz-1c
    - subnet-practice-prod-private-1a
    - subnet-practice-prod-private-1c
    - Allocate CIDR Block from the private ip address that is specified in RFC1918
- Internet Gateway
    - igw-prod
- NAT Gateway
    - natgw-prod
        - Start up an instance to subnet-practice-prod-dmz-1a
- Security Group
    - alb-prod-sg
        - Allow(permit) to HTTP inbound
    - ec2-prod-sg
        - Allow(permit) to HTTP from ALB
        - Allow(permit) to SSH from practice-bastion
    - rds-prod-sg
        - Allow(permit) to MySQL(3306) from EC2
- VPC Peering
    - Connect to vpc-practice-mng
- EC2
    - practice-prod-web01
        - Amazon Linux 2
        - t3.micro
        - EBS(8GB)
        - Start up an instance to subnet-practice-prod-dmz-1a
        - Attach(link) to ec2-prod-sg
        - Tag EXCLUDE_TERMINATE:true
    - practice-prod-web02
        - Amazon Linux 2
        - t3.micro
        - EBS(8GB)
        - Start up an instance to subnet-practice-prod-dmz-1c
        - Attach(link) to ec2-prod-sg
        - Tag EXCLUDE_TERMINATE:true
- Setup
    - Install the middleware below to EC2 instance
        - Apache(2.4)
        - PHP(7.3)
    - Set up the virtualhost
        - example-prod.com (ServerName)
        - /var/www/vhost/example-prod.com/public_html (DocumentRoot)
        - Install wordPress(latest)
- ALB
    - alb-practice-prod
        - Start up to below subnet
            - subnet-practice-prod-public-1a
            - subnet-practice-prod-public-1c
        - Attach(link) to alb-prod-sg
        - Attach(link) to instance below
            - practice-prod-web01
            - practice-prod-web02
- RDS
    - practice-prod-db01
        - MySQL(5.7)
        - db.t3.micro
        - Storage(Auto-expanding on)
        - Start up to blow subnet
            - subnet-practice-prod-private-1a
            - subnet-practice-prod-private-1c
        - Attach(Link) to rds-prod-sg

### 3. Checklist
- To construct the infrastracture configuration diagram according to the requirements.
- To be able to ssh Connection to practice-prod-web01 as a bastion host.
- To display wordpress official site when accessing to "example-prod.com" from any browsers.

## PHASE 4

### 1. Task
- Fix a developed environment and a production environment as part of not working correctly.
- Explain how to solve the problem

### 2. Problem
- Post an article with a picture, it can be shown the picture whether or not when its posting

### 3. How to confirm this problem
- Post an article with a picture from wordpress management console
- Reload the browser in several times

### 4. Checklist
- The problem has been resolved