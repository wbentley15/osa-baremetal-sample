Steps to setup an OSA on bare metal hardware


*** Target Hosts ***

Copy your public key into the following folder:  rpcv10-cloud/roles/hosts-prep/files

Update the ‘hosts' file located within the rpcv10-cloud folder with the server names used in your ‘localhost’ file located in group_vars.

Execute the following play books:
cd rpcv10-cloud
ansible-playbook -i hosts base.yml
ansible-playbook -i hosts compute_update.yml
ansible-playbook -i hosts compute_lxc.yml
ansible-playbook -i hosts infra_lxc.yml
ansible-playbook -i hosts network_update.yml [this will do the network magic and reboot…will take some time for the instances to come back up]


*** Deployment Host ***

Update the ‘hosts' file located within the rpcv10-deploy-cloud folder with the deployment server name.

Within the rpcv10-deploy-cloud/roles/hosts-prep/files folder update/add the following:
Copy your public and private key into the folder
Update the 'hosts' file in the following folder with the IPs assigned to your servers

Execute the following play books:
cd rpcv10-deploy-cloud
ansible-playbook -i hosts base.yml
ansible-playbook -i hosts network_update.yml [this will do the network magic and reboot…will take some time for the instances to come back up]


*** Beginning RPCv10 Install ***

SSH into your deployment server via the public IP

Install fping
apt-get install fping

Use fping to make sure all the servers are communicating over the container network defined.

Example:
fping 172.29.236.2 172.29.236.5 172.29.236.6 172.29.236.9 172.29.236.10 172.29.236.11 172.29.236.12 172.29.236.13

Change Into RPC Playbooks
cd /opt/os-ansible-deployment/rpc_deployment/

Execute the following play books:
ansible-playbook -e @/etc/rpc_deploy/user_variables.yml playbooks/setup/host-setup.yml
ansible-playbook -e @/etc/rpc_deploy/user_variables.yml playbooks/infrastructure/haproxy-install.yml
ansible-playbook -e @/etc/rpc_deploy/user_variables.yml playbooks/infrastructure/infrastructure-setup.yml
ansible-playbook -e @/etc/rpc_deploy/user_variables.yml playbooks/openstack/openstack-setup.yml

Pending all play books finish with no failures, your environment should be up and running, go to the public IP of the deployment server for the Horizon dashboard:
http://<public IP of deployment server>