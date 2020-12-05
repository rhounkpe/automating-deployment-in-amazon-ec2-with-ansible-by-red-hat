# Automating Deployment in Amazon EC2 with Ansible
## By Red Hat
### Course by Chris Caillouet

## Description
Learn how to automate creation and provisioning of Amazon Elastic Compute Cloud (Amazon EC2) instances using Ansible. In this course, we use Ansible to provision a virtual private cloud (VPC) and multiple Amazon EC2 instances, find existing Amazon Machine Images (AMIs) and create custom AMIs automatically, dynamically create Ansible inventories of running instances, and clean up all cloud resources when we are done with them.

## Preparing our Ansible Controle Node

### Prepare a control node for use
- The Ansible Control Node is installed with Ansible and manages our hosts and EC2 environment.
- The control node can be any machine that has Python 2 or Python 3 installed.
- In my case, I prefere to set up the control node outside the AWS network so it can provision everything. I am using my Laptop.

#### Installing Ansible on the Control Node
- The control node requires the installation of several pakages.
- The following example assumes that we are installing Ansible with pip on a Centos or Red Hat Enterprise Linux based system:
1- Use the following command to install pip (Python Package Manager):
$ sudo yum install python3-pip
2- Use pip to install Ansible 2.9 and the AWS module dependencies.
$ sudo pip3 install ansible boto boto3


### Create an AWS identity and Access Management (IAM) user and its access keys for use by Ansible
- After installing Ansible on the Control Node, we need to set up an AWS account for it to use.
- This should not be our AWS account root user!
- Instead, we should create an individual IAM user for Ansible to use.
- This allows us to limit how much access Ansible has to our account and its resources.
- A good discussion of best practicies with AWS identity and Access Management (IAM) is at: https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html

### Create AWS User and Retain the Access Key and Secret Key
- Creating a new user is the first step in managing a new deployment for an account.
- Sign in to the AWS Management Console and open the IAM console.


User name: redhatuser
Group: redhatgroup
Group attached policies: AmazoneEC2FullAccess and AmazoneVPCFullAccess
AWS access type: 'Programmatic access and AWS Management Console access' and 'Console password type'

### The AWS Access and Secret Access Keys
- Download the .csv file. This is the only opportunity to save this file.
- The secret key will later be stored in a variables file called info.yml.
- If access to the secret key is not available, delete the key and create another one.

### Providing Authentication Data to Ansible as Variables
- When we have the access keys, we can prepare a variable file to store our plays.
- Some examples of data is helpful to store as variables:
-- AWS user id
-- AWS secret key
-- AWS SSH Key name
-- AWS region selection

- For better security, use ansible-vault to encrypt the variable files containing your authentication secrets.
-- ansible-vault encrypt vars/info.yml

### Creating a variable file containing Access Keys
The following example outlines one way to provide access keys as variables to a play:
1. Create a directory for this Ansible project. In our example, name it AWS-VPC.
2. Create a directory inside AWS-VPC called vars.
3. Create a file inside the vars directory called info.yml. This file will store the AWS keys, the region, and the ssh keyname used by the playbooks:
    - aws_id: YOUR_AWS_ACCESS_KEY_ID
    - aws_key: YOUR_AWS_SECRET_KEY
    - aws_region: us-west-2
    - ssh_keyname: demo_key
4. In your play, load vars/info.yml to make those values available to the play.


