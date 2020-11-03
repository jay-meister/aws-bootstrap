# aws-bootstrap

## Starting from Scratch

**Create User & Admin IAM user**
- Create root user account
- Create an Administrator user with Administrators Group with AdministratorAccess permissions

**EC2**
- Launch new instance
- Default architecture & t2.micro, all defaults until Security - add TCP rule with port set to 8080
- Launch without a key/pair
- Open instance in console. Note Public DNS (IPv4) 

**Run application - via SSH in browser**
- Open instance in console, click "Connect" > "SSH in browser" > "Connect"
- Install node, install our basic server from github, run server

```bash
sudo yum -y update 
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash
. ~/.nvm/nvm.sh
nvm install node
```
```bash
mkdir logs
curl -sL https://github.com/jmurphyweb/aws-bootstrap/archive/master.zip --output master.zip
unzip master.zip
mv aws-bootstrap-master app
cd app
npm install
npm start
curl localhost:8080
# Hello World
```
- Take public dns from console & hit: `http://<public_dns>:8080`
- Now can create a budget for EC2 instance in Billing section

## Infrastructure as Code - CloudFormation

**AWS CLI**
```bash
# setup profile with access & secret key
aws configure --profile awsbootstrap
# check it is setup:
aws ec2 describe-instances --profile awsbootstrap
```

**Cloud Formation**
- we use a script to execute the cloud formation template
- create `deploy-infra.sh`
- copy contents below
- make executable: `chmod +x deploy-infra.sh`
```bash
#!/bin/bash

STACK_NAME=awsbootstrap 
# STACK_NAME refers to group of resources we will manage
REGION=eu-west-1 
CLI_PROFILE=awsbootstrap
# CLI_PROFILE is the profile we have set up

EC2_INSTANCE_TYPE=t2.micro 

# Deploy the CloudFormation template
echo -e "\n\n=========== Deploying main.yml ==========="
aws cloudformation deploy \
--region $REGION \
--profile $CLI_PROFILE \
--stack-name $STACK_NAME \
--template-file main.yml \
--no-fail-on-empty-changeset \
--capabilities CAPABILITY_NAMED_IAM \
--parameter-overrides \
  EC2InstanceType=$EC2_INSTANCE_TYPE
```