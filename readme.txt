Mordhau-ec2
===============

This set of Ansible scripts supports the provision and termination of a private Mordhau server running in Amazon Web Services (AWS).

By default the server runs on a t2.micro instance which is free under Amazon's free tier accounts and is suitable for basic testing and small scale use. If you are willing to pay a little, a simple config change will increase the size of the instance to support larger scale battles.
Early version, will involve to include integration with a build server (probably circleci) and a better ansible structure.

Pre-Requisites
===============

1. Create an AWS account
2. Create a set of AWS access keys in your AWS account (under IAM -> Users)
3. Create an SSH keypair in your AWS account (under EC2 -> Network & Security -> Key Pairs)
4. Install Ansible (ideally under linux)
5. Install Boto and Boto3

Steps - build server
====================

1. Clone this repository
2. Delete the secrets.yml file
3. Create a new secrets.yml file with the following command:
  ansible-vault create secrets.yml
4. Select a passwords for the secrets file when prompted
5. Enter the following values in the secrets.yml file
  aws_access_key: <your access key>
  aws_secret_key: <your secret key>
  key_name: <your ssh key pair name>
6. Modify files/Game.ini and change the ServerName, ServerPassword and AdminPassword values to your own
7. Run the build process with the following command:
  ansible-playbook -i hosts --ask-vault-pass aws_provision.yml
8. Enter the password you chose for your secrets file when prompted

Steps - build server (Optional)
====================
1. Change the map rotation and other settings in the Game.ini to further customise the server
2. Change the instance_type value in aws_provision.yml to support higher utilisation (note, this will incur AWS hosting costs)

Steps - terminate server
===================
1. Run the terminate process with the following command:
  ansible-playbook -i hosts --ask-vault-pass aws_terminate.yml
2. Enter the password you chose for your secrets file when prompted
 
NOTE: This repo was created with the intention of creating a temporary server for testing purposes, terminated when not in use
