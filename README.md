Setup a [Classrequests](https://github.com/yorkulibraries/classrequests) instance quickly with Vagrant and Ansible. The Ansible playbooks can also be used to deploy to a real production server in a similar fashion.


## Getting started

The following steps to provision a *development* instance of Classrequests.  

Clone this repo:
```
git clone https://github.com/yorkulibraries/vagrant-classrequests.git
```

Change into the vagrant-classrequests directory:
```
cd vagrant-classrequests
```

Clone the [ansible-rails](https://github.com/yorkulibraries/ansible-rails) project:
```
git clone https://github.com/yorkulibraries/ansible-rails.git
```

Clone the Classrequests project for development: (**NOTE:** we use SSH here to clone the Classrequests project because we want to be able to make changes.)
```
git clone git@github.com:yorkulibraries/classrequests.git
```

Install the Ansible dependencies.

```
ansible-galaxy install -r requirements.yml
```

Bring up the box with RAILS_ENV=development (this is the default):

```
vagrant up
```

Watch for any error/failed tasks. If all is good then the instance is ready to use for testing.

Edit /etc/hosts and add an entry like followed so you can access the app from a browser at http://classrequests.me.ca/

```
192.168.56.168 classrequests.me.ca
```

## Verifying emails are sent in development

In development environment, mails are "caught" by MailCatcher. You can see all the emails by going to the MailCatcher web interface at http://classrequests.me.ca:1080/

## Making changes

**NOTE: Assuming you have provisioned the box with the default RAILS_ENV=development.**

The directory **/home/classrequests/classrequests** in the box is a synced folder in the your local machine's **vagrant-classrequests** folder.
You can make changes on your local machine in **vagrant-classrequests/classrequests** folder and it is changed in the vagrant box too. 

## Running unit tests

**NOTE: Assuming you have provisioned the box with the default RAILS_ENV=development.**

SSH into the box as user **classrequests**
```
ssh classrequests@127.0.0.1 -p2222
cd classrequests
RAILS_ENV=test bundle exec rake db:reset
RAILS_ENV=test bundle exec rake test
```

## Provision a vagrant box with RAILS_ENV=production

If you want to bring the box up with RAILS_ENV=production, then specify the environment variable when you run vagrant up:

```
RAILS_ENV=production vagrant up
```

## Provisioning Classrequests on a remote server/VM

Assuming you have a remote server dedicated for running Classrequests. And suppose there is a DNS record for the server such as **classrequests.yourdomain.ca**.

The following steps will install/configure MYSQL and Classrequests on that server.

Create an **inventory** file with the name/IP address of the remote server, similar to the one below:
```
me    ansible_host=192.168.56.168

[rails_app_servers]
me
```

Install Mysql and Classrequests on the target server

```
ansible-playbook -i inventory app_provision.yml -e"rails_env=production app_domain=yourdomain.ca mysql_root_password=db_root_password mysql_host=localhost mysql_user=classrequests mysql_password=classrequests_db_password" --limit me 
```

## About Classrequests
Take a look at [Classrequests](https://github.com/yorkulibraries/classrequests) repo for Classrequests code.
