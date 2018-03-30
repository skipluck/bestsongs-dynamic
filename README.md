# Best Songs (Dynamic)
Project extends [Best Songs (Static)](https://github.com/skipluck/bestsongs-static) to demonstrate using Ansible to:
* Create AWS Security Groups
* Create RDS instance, database and import sample data
* Launch, Start, Stop and Terminate EC2 instances
* Use dynamic inventory to
  * Configure EC2 instances as web server and application server
  * Deploy sample web application queries the RDS database and displays a random song from [list of 100 songs](http://www.johnsandford.org/prey16x1.html)

## Prerequisites
* AWS Account
* AWS IAM Role with Access Keys and appropriate policies attached
* Amazon EC2 Key Pairs

## Running this project
Open terminal window and run the following commands

### Install Python3 and required packages on Ubuntu Linux
````
sudo apt-get update
sudo apt-get install python3 python3-dev python3-pip libmysqlclient-dev mysql-client
pip3 install --upgrade pip
pip3 install virtualenv
````
### Cloning this project from github
````
git clone https://github.com/skipluck/bestsongs-dynamic
````

### Creating Ansible Project Virtual Environment
````
cd bestsongs-dynamic
virtualenv --python=/usr/bin/python3 BestSongs
source BestSongs/bin/activate
````

### Installing Ansible and other packages
````
pip3 install ansible boto boto3 "pywinrm>=0.3.0" mysqlclient
````

### Download AWS Dynamic Inventory files
````
wget https://raw.github.com/ansible/ansible/devel/contrib/inventory/ec2.py
wget https://raw.githubusercontent.com/ansible/ansible/devel/contrib/inventory/ec2.ini
chmod +x ec2.py
````
### Set environment variables
````
export AWS_ACCESS_KEY_ID='AK123'
export AWS_SECRET_ACCESS_KEY='abc123'
export ANSIBLE_NOCOWS=1
export ANSIBLE_HOST_KEY_CHECKING=false
export ANSIBLE_INVENTORY=`pwd`/ec2.py
export EC2_INI_PATH=`pwd`/ec2.ini
````

### Running playbooks
#### Create EC2 Security Groups
````
ansible-playbook -i localhost create_security_group.yml
````
#### Create RDS instance, database and import sample data
````
ansible-playbook -i localhost create_rds.yml
````
#### Create EC2 instances
````
ansible-playbook -i localhost create_ec2_instances.yml
````
Above 3 playbooks can be run at once by
````
ansible-playbook -i localhost create_env.yml
````
**Note**: Currently there is a bug in boto RDS module that returns TypeError and can be fixed by
``body = response.read().decode('utf-8')`` in rds2/layer1.py. See https://github.com/boto/boto/issues/2677.

#### Deploying application
````
ansible-playbook -u ubuntu deploy_app.yml
````

### Exiting Virtual Environment
````
deactivate
````
