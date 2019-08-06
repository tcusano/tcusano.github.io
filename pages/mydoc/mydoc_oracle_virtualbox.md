---
title: Oracle Vritual Box
tags:
keywords:
last_updated: July 6, 2019
sidebar: mydoc_sidebar
permalink: mydoc_oracle_virtualbox.html
folder: mydoc
---

## How to Mount share as different user 

When mount a shared folder on a virtual machine you find that the owner and group defaults to root. To mount the shared folder as a different user 
you woul run the below command:
 
```js
mount -t vboxsf -o rw,nosuid,nodev,uid=<user uid>,gid=<group uid> <share> <mount point>;
````

For example: I want to mount the software folder from my host machine I would use above command replacing the values:  

Therefore, the user uid can be found on the VM in /etc/passwd,
        group group gui can be found on your VM in /etc/group,
OR
	you can login as the oracle user and type in 'id' on the command prompt.


```js
mount -t vboxsf -o rw,nosuid,nodev,uid=54321,gid=54321 software /media/software
````
