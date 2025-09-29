# Active-Active File Server

This project is to create a two node Active-Active file sever integrated with Active Directory for Boursa Kuwait. 

## Technologies used
1. Ansible
2. Git
3. PCS
4. Samba
5. Winbind
6. Kerberos

## Prerequisite
1. InstallBox - Virtual Machine/PC/Laptop with latest Ansible and Git installed.
2. Network access (ssh) to the two file servers from InstallBox.
3. A Red Hat subscription with two entitlements having High Availability and Resilient Storage add-on(s).
4. Private network IP address for cluster communication (e.g: 10.0.0.1; 10.0.0.2).
5. Two public IP addresses for file server.
6. Two additional static IPs for individual server access.
7. Shared storage created and presented to both the servers.
8. Internet access on the InstalBox to download source code from git and to install the required packages.
9. If not using Satellite server, internet connectivity on both the servers.
10. An 'A' record in DNS pointing to the two CTDB public addresses.

## Installation
1. Configure private IPs and static IPs on the servers.
2. Register the servers to either the Satellite server or Red Hat Network.
3. On InstallBox:
 	* Clone the repository from github.com: 
	```
	git clone https://github.com/0jas/BoursaKuwait.git
	```
	* Change directory: 
	```
	cd BoursaKuwait
	```
	* Run ansible playbook to deploy the cluster: 
	```
	ansible-playbook setup_ctdb_cluster.yaml --ask-vault-pass
	```
4. Once the playbook finishes execution, login to any server node and type ```pcs status``` at the prompt to check the status of the cluster.
5. The cluster is fully functional only after all the resources have started.
6. If this is the first time the cluster is setup, last resource "hasmbserver-clone" is in "Stopped" state as "krb5.keytab" is absent.
7. Join the nodes to the domain. Type the following commands on both the nodes: 
	* ```net ads join -U <username>```
	* ```net ads keytab add cifs -U <username>```
	* ```authconfig --enablewinbind --enablewinbindauth --enablemkhomedir --disablesssd --disablesssdauth --update```
8. Restart the cluster. Type the commands in any server node. 
	* ```pcs cluster stop --all```
	* ```pcs cluster start --all```
9. Now all the resources are in "Started" state.

## Checking the status of the cluster

1. Login in to any server node.
2. ```pcs status``` for cluster status.
3. ```ctdb status``` for ctdb status.
4. ```ctdb nodestatus``` for ctdb node status.
5. ```ctdb ip``` to list file server ips.
6. ```klist -k``` to list keys held in a keytab file. 
7. ```net ads info``` info of LDAP server.
8. ```wbinfo -u``` to list AD users.
9. ```wbinfo -g``` to list AD groups.
10. ```id <AD user>``` user info of AD user.
