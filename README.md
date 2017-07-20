# F5 Automation

### Purpose
This playbook automates the addition and removal of pool members in the F5, for
the purposes of RHEL5->RHEL6 migrations. This can be enhanced with more functionality later.

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
1. RDP and/or SSH into the respective environment's ansible machine
   ssh us5nprepo01
2. ssh /root/mktest
3. ansible-playbook -i inventory/us5np.hosts site.yml --tags=""
```
   where tags are one of the following:
   addnodedisabled
   addnode
   addpoolmember
   disablepoolmember
   wait
   delpoolmember
   delnode
```
A demonstration can be downloaded [here](https://catechnologies.webex.com/svc3200/svccomponents/servicerecordings/servicerecordinginfo.do?RCID=4d83c33a884d4cfca4ccb337d9a3d687&siteurl=catechnologies&apiname=viewrd.php&needFilter=false&rnd=3566110348&isurlact=true&entactname=%2FnbrRedirect.do&entappname=url3200&renewticket=0&serviceType=mc&targetAction=%2Fsvccomponents%2Fservicerecordings%2Fservicerecordinginfo.do&mywbxLink=yes&rID=105931402&recordID=105931402&targetApp=svc3200&action=info&SP=MC&fromUrlApi=1)

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

### GOTCHAS
1. When removing a node from a pool, the node will also be removed from the F5. This is okay for this current migration maintenance, but could be dangerous in the future. This is a limitation of the F5 API and not something I can fix.

### Other Notes
1. These two VMs are ready for testing in US5NP.
```
Hostname       PUB IP            MGMT IP           STG IP
US5NPAPP298    10.47.33.206      10.47.35.195      10.47.13.173
US5NPAPP299    10.47.33.207      10.47.35.196      10.47.13.174
us5npapp307    10.47.33.187      10.47.35.187      10.47.13.194
us5npapp308    10.47.33.188      10.47.35.188      10.47.13.195
```
2. Prerequisite packages
```
  ansible-latest (2.2 minimum)
  easy_install pip
  pip install bigsuds
  passlib (for encrypting user input credentials - untested/TODO)
```
