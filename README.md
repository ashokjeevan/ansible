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

To gather facts about an EC2
`ansible all -m gather_facts --limit <ip_address> -u ec2-user`

