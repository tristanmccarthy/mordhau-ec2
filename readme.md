# Mordhau-ec2

This set of Ansible scripts supports the provision and termination of a private Mordhau server running in Amazon Web Services (AWS).

By default the server runs on a t2.micro instance which doesn't cost anything under Amazon's free tier accounts. Initial tests have shown that this size of instance is fine for a couple of people playing with bots before the ping starts to suffer. This is fine for testing purposes but would need to be scaled up for a real server (see further customisation).

Mostly followed the guide on the Mordhau forums here: https://mordhau.com/forum/topic/10348/dedicated-server-hosting-guide-linux/ 
and automated it as I went. I switched from a RHEL AMI using apt-get to an AWS Linux image using yum to install dependencies due to lower costs when scaling up this OS.

## Pre-Requisites
1. Create an AWS account
2. Create a set of AWS access keys in your AWS account (under IAM -> Users)
3. Create an SSH keypair in your AWS account (under EC2 -> Network & Security -> Key Pairs)
4. Install Ansible (ideally under linux)
5. Install Boto and Boto3

## Steps - build server
1. Clone this repository
2. Delete the existing `secrets.yml` file (it's encrypted, you won't be able to use it)
3. Create a new `secrets.yml` file with the following command:
```
ansible-vault create secrets.yml
```
4. Select a passwords for the secrets file when prompted
5. Enter the following values in the `secrets.yml` file
```
aws_access_key: <your access key>
aws_secret_key: <your secret key>
key_name: <your ssh key pair name>
``` 
6. Modify `/roles/mordhau/tasks/files/Game.ini` and change the ServerName, ServerPassword and AdminPassword values to your own. You'll probably also want to clear the list of Admins steamid's.
7. Run the build process with the following command:
```
ansible-playbook -i hosts --ask-vault-pass gameserver.yml
```
8. Enter the password you chose for your secrets file when prompted
NOTE: The above command will terminate any existing gameserver instances before creating, to ensure you only ever have one.  

## Steps - terminate server
1. Run the terminate process with the following command:
```
ansible-playbook -i hosts --ask-vault-pass terminate.yml
```
2. Enter the password you chose for your secrets file when prompted

## Further customisation (Optional)
1. If you want to try running a full scale 64 person server you will need to increase the instance size. This will incur AWS costs and I've not done any testing to figure out what instance you'd need. The type can be changed in the `instance_type` value in `/group_vars/all.yml`. Full list of instance types: https://aws.amazon.com/ec2/instance-types/
2. Change the map rotation and other settings in the `/roles/mordhau/tasks/files/Game.ini` to change what maps appear for voting
3. The current settings create instances in eu-west-2 (London). If you want something closer to home this can be changed in the `region` value in `/group_vars/all.yml`. Full list of regions: https://docs.aws.amazon.com/general/latest/gr/rande.html
