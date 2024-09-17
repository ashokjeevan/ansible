# Ansible

**Install Ansible in Python virtual environment**

`python3 -m venv .venv`

Activate it: `. ./.venv/bin/activate`

Install Ansible using `pip3 install ansible`

**Ansible notes**

First command to test if the ssh connectivity works using Ansible:

`ansible all --key-file ../../keypair/**.cer -i inventory -m ping -u ec2-user`

Inventory file (consisting of IPs) is kept in a file called `inventory` in the same folder
`ansible.cfg` to configure the keypair location and inventory file, so that the `ansible` command will automatically use the config to run the playbook

**Note**
If Ansible is installed locally in the machine (outisde the venv), then `ansible.cfg` will be found at `/etc/ansible`. 
The `ansible.cfg` in our local folder will override the local machine's Ansible config file.

Once `ansible.cfg` is set up, then we can run the command:
`ansible all -m ping -u ec2-user`

Update cache in Amazon Linux EC2s:
`ansible all -m yum -a update_cache=true -u ec2-user`
Refer: `https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html`


**Playbooks**

Create `filename.yml` and run: `ansible-playbook filename.yml -u ec2-user`

Sep 8 2023

To gather facts about an EC2:

`ansible all -m gather_facts --limit <ip_address> -u ec2-user`
<br>
<br>
**Variables**

When using variables in playbook, use `"{{ <variable> }}"`
This variable is initialized in the `inventory` file alongside each entries.

Instead of an OS specific package manager, we can use `package` module in Ansible (generic package manager). This will use whatever package manager the target server uses.

Sep 11 2023
Grouping nodes together in inventory file.

Eg: 
<br>
```
[web-server]
99.99.99.99
123.123.123.123

[rds-server]
49.49.49.49
143.143.143.143
```

In playbook, refer to the above groups as:
Eg:
```
- hosts: web-server
  *******
  *******
- hosts: rds-server
  *******
  *******
```

**List tags**

**Note:** OS versions used in the `install_apache_using_tags.yml` are not correct. Only look at the usage of the tag.

`ansible-playbook --list-tags <playbook.yml>`

**Applying tag based playbook**

`ansible-playbook --tags "tag1,tag2" -u ec2-user`

### September 9 2024

To show detailed output: Add `-vvv` to the ansible execution command
-v: Standard verbosity level
-vv: Increased verbosity
-vvv: High verbosity
-vvvv: Extremely high verbosity

Note:
Understand why the following command worked:
ansible web_servers --private-key <private_key> -i inventory -m ping -u ec2-user

Adhoc command example:
ansible [pattern] -m [module] -a "[module options]"

ansible web_servers -m ansible.builtin.copy -a "src=remove_apache.yml dest=/tmp/hosts" -u ec2-user -i inventory

ansible-config -> View Ansible configuration

ansible-console -> REPL console for executing Ansible tasks.

ansible-doc -> plugin documentation tool

ansible-galaxy -> Command to manage Ansible roles and collections

ansible-inventory -> used to display or dump the configured inventory as Ansible sees it

ansible-pull -> Used to pull a remote copy of ansible on each managed node, each set to run via cron and update playbook source via a source repository. This inverts the default push architecture of ansible into a pull architecture, which has near-limitless scaling potential.

ansible-vault -> can encrypt any structured data file used by Ansible. This can include group_vars/ or host_vars/ inventory variables, variables loaded by include_vars or vars_files, or variable files passed on the ansible-playbook command line with -e @file.yml or -e @file.json. Role variables and defaults are also included

Because Ansible tasks, handlers, and other objects are data, these can also be encrypted with vault. If you’d like to not expose what variables you are using, you can keep an individual task file entirely encrypted.

__Example commands:__

ansible localhost -m ansible.builtin.apt -a "name=apache2 state=present" -b -K

Install httpd
ansible-playbook gather_facts.yml -i inventory -u ec2-user -b --private-key <private_key>

__Running Playbooks on AWS (Dynamic Inventory)__

If you're using Ansible with AWS, you can integrate it with your infrastructure using the AWS dynamic inventory plugin or EC2 inventory plugin to query EC2 instances directly.

__Example setup in your inventory file:__

yaml
Copy code
plugin: aws_ec2
regions:
  - us-east-1
keyed_groups:
  - key: tags.Name

To execute certain tasks in playbook:
--step

To make a dry run / check what's the ouput instead of executing:
--check

Register
Allows you to store the results of a task's execution in a variable. 
These results can include critical information like task success or failure, 
returned data, or even metadata about the host systems. By capturing these results,
you can make intelligent decisions, perform conditional tasks, and deeper insights
into your automation processes.

ignore_errors: yes/no
Use this in the global space in playbook to ignore the errors if it occurs (yes). If set to no, playbook will stop executing when the error occurs


Can use register in conditions. Eg: if the isntallation of a software is successful, proceed with the configuration of the software.

# Roles
https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html
