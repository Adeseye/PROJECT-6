## WEB SOLUTION WITH WORDPRESS

In this project I will be setting up a storage infrastructure on two Linux servers, implement a basic web solution using WordPress and MySQL as its backend database Relational Database Management System (RDBMS).

This project is divided into two stages

First stage :- Configure storage subsystem for Web and Database servers based on Linux OS. This involves working with disks, partitions and volumes in Linux.

Second stage :- Install WordPress and connect it to a remote MySQL database server.

Now with the first stage I will create two Linux servers, one for Web server and the other for Database server

![alt text](./Images/Servers.JPG)

Next - Create 3 disk each 10GB in size for both Web server & Database server, attach them to both servers.

![alt text](./Images/Second%20Attempt/Database%20server/step1a%20attach%20all%20volumes%20to%20database%20server.JPG)

Launch the Linux Webserver, open terminal to begin configuration.  

Run *lsblk* command to inspect what block devices are attached to the server.

![alt text](./Images/Second%20Attempt/step%201%20lsblk.JPG)

Run *gdisk* utility to create a single partition on each of the 3 disks

<code>sudo gdisk /dev/xvdf</code>

Type <code>n</code> to create a new partition,

Select 1 to set it as default partition number

Type *p* to preview the changes made 

Type *w* to write to disk 

Finally type *y* to confirm to proceed.

Do this for the next 2 disk. 

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdf%20step%201.JPG)

<code>sudo gdisk /dev/xvdh</code>

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdh%20step%201.JPG)

<code>sudo gdisk /dev/xvdg</code>

![alt text](./Images/Second%20Attempt/sudo%20gdisk%20dev%20xvdg%20step%201.JPG)

Repeat this step for 3 remaining disk for the Database server.

Use the <code>lsblk</code> utility to view the newly configured partition on each of the 3 disk

![alt text](./Images/Second%20Attempt/lsblk%20ultility%20step1.JPG)
 
 Next install <code>lvm2</code>