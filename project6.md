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

Next use <code>lvcreate</code> utility to create 2 logical volumes. apps-lv (Use half of the PV size), and logs-lv Use the remaining space of the PV size.

NOTE: apps-lv will be used to store data for the Website while, logs-lv will be used to store data for logs.

<code>sudo lvcreate -n apps-lv -L 14G webdata-vg</code>

![alt text](./Images/Second%20Attempt/step%206%20lvcreate%201a.JPG)

<code>sudo lvcreate -n logs-lv -L 14G webdata-vg</code>

![alt text](./Images/Second%20Attempt/step%206%20lvcreate%201b.JPG)

Verify that your Logical Volume has been created successfully by running <code>sudo lvs</code>

![alt text](./Images/Second%20Attempt/step%207%20very%20logicaal%20volume%20sudo%20lvs.JPG)

Verify the entire setup

<code>sudo vgdisplay -v #view complete setup - VG, PV, and LV</code> 

![alt text](./Images/Second%20Attempt/step%208%20very%20the%20whole%20setup%20sudo%20vgdisplay.JPG)

Run <code>sudo lsblk</code>

![alt text](./Images/Second%20Attempt/step%209%20sudo%20lsblk.JPG)

Use mkfs.ext4 to format the logical volumes with ext4 filesystem

<code>sudo mkfs -t ext4 /dev/webdata-vg/apps-lv</code>
<code>sudo mkfs -t ext4 /dev/webdata-vg/logs-lv</code>

![alt text](./Images/Second%20Attempt/step%2010%20mkfs%20ext4%20logical%20volume.JPG)


Create **/var/www/html** directory to store website files

<code>sudo mkdir -p /var/www/html</code>

Create **/home/recovery/logs** to store backup of log data

<code>sudo mkdir -p /home/recovery/logs</code>

Mount **/var/www/html on apps-lv** logical volume

<code>sudo mount /dev/webdata-vg/apps-lv /var/www/html/</code>

![alt text](./Images/Second%20Attempt/step%2011%20making%20directory%20create%20logs%20mount%20apps-lv.JPG)


Use rsync utility to backup all the files in the log directory **/var/log** into **/home/recovery/logs** (This is required before mounting the file system)

<code>sudo rsync -av /var/log/. /home/recovery/logs/</code>

![alt text](./Images/Second%20Attempt/step%2012%20use%20resync%20ultility.JPG)


Mount **/var/log** on **logs-lv** logical volume. (Note that all the existing data on /var/log will be deleted. That is why step above is very important

<code>sudo mount /dev/webdata-vg/logs-lv /var/log</code>

Restore log files back into **/var/log** directory

<code>sudo rsync -av /home/recovery/logs/. /var/log</code>

![alt text](./Images/Second%20Attempt/step%2013%20mount%20logs%20restore%20log%20files.JPG)

Next is to update /etc/fstab file so the mount configuration will persist/remain after a restart of the server.

The UUID of the device will be used to update the /etc/fstab file;

<code>sudo blkid</code>

High light and copy the UUID details 

![alt text](./Images/Second%20Attempt/step%2014%20update%20etc%20fstab%20file%20sudo%20blkid.JPG)

<code>sudo vi /etc/fstab</code>

Update /etc/fstab in this format using your own UUID and rememeber to remove the leading and ending quotes.

![alt text](image.jpg)

Test the configuration and reload the daemon

 <code>sudo mount -a</code>

 <code>sudo systemctl daemon-reload</code>

 ![alt text](./Images/Second%20Attempt/step%2015%20test%20configuration%20and%20reload%20daemon.JPG)

Verify your setup by running <code>df -h</code>

![alt text](./Images/Second%20Attempt/step%2016%20verify%20%20setup%20is%20running.JPG)

Now begin the second stage;

## CONFIGURE THE DATABASE SERVER

Launch a second RedHat EC2 instance that will have a role – ‘DB Server’
Repeat the same steps as for the Web Server, but instead of apps-lv create db-lv and mount it to /db directory instead of /var/www/html/

Create and attach 30GB Volume for the Database server 

![alt text](./Images/database%20server/another%2030GB%20volume%20for%20Database%20server.JPG)


Run <code>lsblk</code> command to inspect what block devices are attached to the Database server, run <code> gdisk</code> utility to create a single partition on each of the 3 disks

<code>sudo gdisk /dev/xvdf</code>

![alt text](./Images/database%20server/mount%20xvdf%20database%20server%201.JPG)

<code>sudo gdisk /dev/xvdh</code>

![alt text](./Images/database%20server/mount%20xvdh%20database%20server%203.JPG)


<code> sudo gdisk /dev/xvdg</code>

![alt text](./Images/database%20server/mount%20xvdf%20database%20server%202.JPG)

Install <code>Lvm2</code>

![alt text](./Images/database%20server/installing%20lvm2%20on%20databse%20server%202.JPG)

Install wget, Apache and it’s dependencies

<code>sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json</code>

![alt text](./Images/database%20server/install%20wget%2C%20apache%20and%20its%20dependences%20step%2010.JPG)

