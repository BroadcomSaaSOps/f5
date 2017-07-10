# F5 Automation

### Purpose
This playbook automates the addition and removal of pool members in the F5, for
the purposes of RHEL5->RHEL6 migrations. This will likely be enhanced for more later.

### Overview
Automation for the F5 includes the following steps:
1. Add new node
2. Add new node as disabled pool member
3. Enable new pool member
4. Disable old pool member
5. Wait for (X) time to allow connections to drain from old pool member
6. Remove old pool member
7. Delete old node

### Usage
1. RDP and/or SSH into the respective environment's ansible machine (us5nprepo01)
2. 

### Jenkins
(TODO) This playbook can be frontended in [Jenkins](http://canpjen02.ca.com/jenkins/#TODO)
```
1. Go to http://canpjen02.ca.com/jenkins/job/#TODO
2. Provide a server list to update #TODO
3. Click "build" to start
```

### TODO
The following features are on the radar and may be added in the future:
1. Jenkins frontend
2. Proper connection draining check via API and/or wait_for module
3. Encrypt user input credentials (not stored to fs, but are plaintext in memory)

### Pitfalls and manual steps
N/A

### Other Notes
1. These two VMs are ready for testing in US5NP.
```
US5NPAPP298    10.47.33.206      10.47.35.195      10.47.13.173
US5NPAPP299    10.47.33.207      10.47.35.196      10.47.13.174
```
2. Prerequisite packages
```
  ansible-latest (2.2 minimum)
  easy_install pip
  pip install bigsuds
  passlib (for encrypting user input credentials - untested/TODO)
```
