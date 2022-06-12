## WEB SOLUTION WITH WORDPRESS

In this project I will be setting up a storage infrastructure on two Linux servers, implement a basic web solution using WordPress and MySQL as its backend database Relational Database Management System (RDBMS).

This project is divided into two stages

First stage : Configure storage subsystem for Web and Database servers based on Linux OS. This involves working with disks, partitions and volumes in Linux.

Second stage : Install WordPress and connect it to a remote MySQL database server.

Now with the first stage I will create two Linux servers, one for Web server and the other for Database server

![alt text](./Images/Servers.JPG)

Next - Create 3 disk each 10GB in size for both Web server & Database server, attach them to both servers.

![alt text](./Images/Second%20Attempt/Database%20server/step1a%20attach%20all%20volumes%20to%20database%20server.JPG)

Launch the Linux Webserver, open terminal to begin configuration.  

Run <code>lsblk</code> command to inspect what block devices are attached to the server.

![alt text](./Images/Second%20Attempt/step%201%20lsblk.JPG)

Run <code>gdisk</code> utility to create a single partition on each of the 3 disks

<code>sudo gdisk /dev/xvdf</code>

Type <code>n</code> to create a new partition,

Select <code>1</code> to set it as default partition number

Type <code>p</code> to preview the changes made 

Type <code>w</code> to write to disk 

Finally type <code>y</code> to confirm to proceed.

Do this for the next 2 disk. 

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdf%20step%201.JPG)

<code>sudo gdisk /dev/xvdh</code>

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdh%20step%201.JPG)

<code>sudo gdisk /dev/xvdg</code>

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdg%20step%201.JPG)

Use the <code>lsblk</code> utility to view the newly configured partition on each of the 3 disk

![alt text](./Images/Second%20Attempt/lsblk%20ultility%20step1.JPG)
 
 Next install lvm2 package using <code>sudo yum install lvm2</code>

 ![alt text](./Images/Second%20Attempt/install%20lvm2%20step%202.JPG)

 ![alt text](./Images/Second%20Attempt/lmv2%20successfully%20installed%20step2.JPG)

 Use <code>pvcreate</code> utility to mark each of the 3 disk as a physical volumes (PVs) to be used by LVM.

 <code> sudo pvcreate /dev/xvdf1</code>

<code>sudo pvcreate /dev/xvdg1</code>

<code>sudo pvcreate /dev/xvdh1</code>

![alt text](./Images/Second%20Attempt/step%203%20pscreate%20ultility.JPG)

Verify that your Physical volume has been created successfully by running <code>sudo pvs</code>

![alt text](./Images/Second%20Attempt/step%203a%20sudo%20pvs.JPG)

Use vgcreate utility to add all 3 PVs to a volume group (VG). Name the VG webdata-vg

<code>sudo vgcreate webdata-vg /dev/xvdh1 /dev/xvdg1 /dev/xvdf1</code>

![alt text](./Images/Second%20Attempt/step%204%20vgcreate.JPG)

Verify that your VG has been created successfully by running <code>sudo vgs</code>

![alt text](./Images/Second%20Attempt/step%205%20very%20VG%20is%20created%20sudo%20vgs.JPG)

Now repeat the steps above to configure 3 Disk for Data server 

Next use <code>lvcreate</code> utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size.

NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

<code>sudo lvcreate -n apps-lv -L 14G webdata-vg</code>

![alt text](./Images/Second%20Attempt/step%206%20lvcreate%201a.JPG)

<code>sudo lvcreate -n logs-lv -L 14G webdata-vg</code>

![alt text](./Images/Second%20Attempt/step%206%20lvcreate%201b.JPG)