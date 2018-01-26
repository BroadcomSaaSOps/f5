# F5 Automation

### Purpose
This playbook automates the addition and removal of pool members in the F5, for the purposes of RHEL5->RHEL6 migrations.  
This can be enhanced with more functionality later.

### Overview
Automation for the F5 includes the following steps:
1. Add new node
  1. Add new node in a disabled state
  2. Add new node in an enabled state
2. Add new node as disabled pool member
3. Enable new pool member
4. Disable old pool member
5. Wait for (X) time to allow connections to drain from old pool member
6. Remove old pool member
7. Delete old node

### Usage
1. Create an inventory file with the following format (replace us5 with au1/mun/sc5/us5 as needed):
```
[us5:children]
addmembers
delmembers

[addmembers]
new_rhel6_hostname1 f5_node_pool=poolname f5_node_port=14001 f5_node_ip=10.10.10.10
new_rhel6_hostname2 f5_node_pool=poolname f5_node_port=14002 f5_node_ip=10.10.10.10

[delmembers]
old_rhel5_hostname1 f5_node_pool=poolname f5_node_port=14001 f5_node_ip=10.10.10.10
old_rhel5_hostname2 f5_node_pool=poolname f5_node_port=14002 f5_node_ip=10.10.10.10
```

2. RDP and/or SSH into the respective environment's ansible machine:
  - ssh us5npopsansi01
  - ssh sc5opsansi01
  - ssh demunopsansi01
  - ssh au1opsansi01

3. Change directory to where the automation is staged:
```
cd /usr/share/ansible/f5
```

4. Run the playbook, substituting in the appropriate values:
```
ansible-playbook -i /path/to/inventory/file.hosts site.yml --tags="[tags]" --check
```
where [tags] is one or more of the following (comma separated):
  - addnodedisabled
  - addnode
  - addpoolmember
  - disablepoolmember
  - delpoolmember  
and --check runs in 'testing' or 'dry-run' mode.
**Always use --check on your first run to address any mistakes before the real execution.**
example:
```
(all in one)
ansible-playbook -i inventory/us5.hosts site.yml --tags="addnode,addpoolmember,disablepoolmember,wait,delpoolmember"
-or-
(split across 2 times)
ansible-playbook -i inventory/us5.hosts site.yml --tags="addnodedisabled,addpoolmember"
ansible-playbook -i inventory/us5.hosts site.yml --tags="addnode,addpoolmember,disablepoolmember"
```



A demonstration can be downloaded [here](https://catechnologies.webex.com/svc3200/svccomponents/servicerecordings/servicerecordinginfo.do?RCID=4d83c33a884d4cfca4ccb337d9a3d687&siteurl=catechnologies&apiname=viewrd.php&needFilter=false&rnd=3566110348&isurlact=true&entactname=%2FnbrRedirect.do&entappname=url3200&renewticket=0&serviceType=mc&targetAction=%2Fsvccomponents%2Fservicerecordings%2Fservicerecordinginfo.do&mywbxLink=yes&rID=105931402&recordID=105931402&targetApp=svc3200&action=info&SP=MC&fromUrlApi=1)
### Common Errors
1. *TODO* IP in use - F5 already has an entry with that IP address in use. Manually clean up the stale entry.
2. *TODO* IP or hostname in use - F5 already has an entry with that hostname. Manually clean up the stale entry.
3. *TODO* [Insert Error Message]. - Your input file has a zero in the last octet. Manually fix the input file.
4. *TODO* [Insert Error Message]. - Your input file lacks the header info. Add [env:children] to header and 'addmembers/delmembers'

### GOTCHAS
1. When removing a node from a pool, the node will also be removed from the F5. This is okay for this current migration maintenance, but could be dangerous in the future. This is a limitation of the F5 API and not something I can fix. The tag "delpoolmember" does exactly this. BE CAREFUL.
2. User F5 credentials are stored in memory as plain-text for the duration of the playbook execution. I tested Ansible's ability to hash+salt the password, which worked, but the F5 modules do not provide a method of decrypting them after that, so authentication fails. We will want to keep an eye on this Ansible module for that feature, should it be added.


### Other Notes
Prerequisite packages
```
  ansible 2.2+
  easy_install pip
  pip install bigsuds
```
This playbook includes a bootstrap role which can install the requisite packages. This should only need to run one time to prepare the Ansible server. To use it, try the "bootstrap" tag. Root is required for this, so it is an Infrastructure Server task only.
ex. $ ansible-playbook -i inventory/us5.hosts site.yml --tags="bootstrap" --check --ask-sudo-pass


### TODO
The following features are on the radar and may be added in the future:
1. Jenkins/Tower frontend
2. Proper connection draining check via API and/or wait_for module
3. Encrypt user input credentials (not stored to fs, but are plaintext in memory)
4. Encrypt Service accounts credentials with Vault
