## DEVOPS TOOLING WEBSITE SOLUTION



In previous project [(Project 6()] I implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. Moving further I will add some more value to my solutions that a member of a DevOps team could utilize. 



In this project, i will introduce a set of DevOps tools that will help members of team in their day to day activities in managing, developing, testing, deploying and monitoring different projects.By adding a Network file System(NFS) to my system architecture(website). The NFS host the static files needed to server the site to the public while the Database stores the dynamic data. This architecture makes our webserver stateless: meaning we can easily configure and scale the webserver up or down as the need arises.



## ARCHITECTURAL DESIGN

![Screenshot 2023-01-23 181130](https://user-images.githubusercontent.com/122198373/214173130-bba052be-b862-4e4a-bc1a-c5ee29913eee.png)



This project consists of the following servers:

    Web server(RHEL)
    Database server(Ubuntu + MySQL)
    Storage/File server(RHEL + NFS server)
    
    

## STEP I. PREPARING NFS SERVER

- Spin up a new EC2 instance with (RHEL Linux 9 Operating System).
* Configure LVM on the Server.
* Create 3 Logical Volumes (lv-apps, lv-logs, and lv-opt)
* Format the disks as xfs

Create 3 mount points on /mnt directory for the logical volumes as follow:


     Mount lv-apps on /mnt/apps – To be used by webservers
     Mount lv-logs on /mnt/logs – To be used by webserver logs
     Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8




![NFS_mounted_GIT](https://user-images.githubusercontent.com/122198373/214183586-3b596762-54b9-42de-8d51-6a49d328a22d.png)



Install the nfs-server on the nfs instance and ensure that it starts on system reboot



    sudo yum -y update
    sudo yum install nfs-utils -y
    sudo systemctl start nfs-server.service
    sudo systemctl enable nfs-server.service
    sudo systemctl status nfs-server.service


![nfs_server_run_GIT](https://user-images.githubusercontent.com/122198373/214186105-106006a0-32e4-4fd1-a09e-1fab64a15a1d.png)


* Set up permission that will allow the Web servers to read, write and execute files on NFS:


![nfs_permission_change](https://user-images.githubusercontent.com/122198373/214188829-765d9c6b-c355-49fd-a573-d5af70300cc1.png)

* Export the mounts for webservers’ subnet CIDR to connect as clients. For simplicity, install all the three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security. To check the subnet CIDR – open the EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:



![image](https://user-images.githubusercontent.com/122198373/214203410-1b2bf4cd-37dd-4084-b75c-74b2b16a3de5.png)


Configure access to NFS for clients within the same subnet by editing the following file

        sudo vi /etc/exports

        /mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
        /mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
        /mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

        Esc + :wq!

        sudo exportfs -arv
        
![nfs_expoted_GIGT](https://user-images.githubusercontent.com/122198373/214193452-5dc56c93-314d-48d1-8ef6-ee973e966a13.png)

Note: Use (rpcinfo -p | grep nfs) command to Check which port is used by the NFS server and add it as an inbound rule.  Also for NFS server to be accesible the following inbound ports had to be opened TCP 111, UDP 111, UDP 2049

* To check what port is used by NFS 

![image](https://user-images.githubusercontent.com/122198373/214194369-3ce83efa-658a-4cb3-be03-7ded18b5c759.png)

* Set up NFS security group using the following ports: TCP 111, UDP 111, UDP 2049

![image](https://user-images.githubusercontent.com/122198373/214194433-bb1ca499-db96-4b68-8901-9b0ac5263c34.png)


## STEP-II: CONFIGURE THE DATABASE SERVER

* Create an Ubuntu Server on AWS which will serve as our Database. Ensure its in the same subnet as the NFS-Server.

Install mysql-server

    sudo apt -y update
    sudo apt install -y mysql-server

* To enter the DB environment run

sudo mysql

    Create a database and name it tooling
    Create a database user and name it webaccess
    Grant permission to webaccess user on tooling database to do anything only from the webservers subnet cidr

![DB_mysql_created](https://user-images.githubusercontent.com/122198373/214198307-c9abff34-dad1-41cd-828e-415766a47774.png)




## STEP III: PREPARE THE WEBSERVERS

* Launch a new EC2 instance with RHEL 8 Operating System

* Install NFS client

        sudo yum install nfs-utils nfs4-acl-tools -y


![WebServer_Nfs_installed](https://user-images.githubusercontent.com/122198373/214200608-3752cef7-c1ea-4967-b3ff-b9c54d3649f0.png)

* Mount /var/www/ and target the NFS server’s export for apps

         sudo mkdir /var/www
         sudo mount -t nfs -o rw,nosuid <NFS-Server-Private-IP-Address>:/mnt/apps /var/www

![Nfs_client_we1_mounted_git](https://user-images.githubusercontent.com/122198373/214200744-5dc4cd36-bdb7-4511-a308-24ef05578ae0.png)


* Verify that NFS was mounted successfully by running df -h. Make sure that the changes will persist on Web Server after reboot:

             sudo vi /etc/fstab

      Add following line

            <NFS-Server-Private-IP-Address>:/mnt/apps /var/www nfs defaults 0 0

* Install Remi’s repository, Apache and PHP

     
         sudo yum install httpd -y

         sudo dnf install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm

         sudo dnf install dnf-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm

         sudo dnf module reset php

         sudo dnf module enable php:remi-8.2

         sudo dnf install php php-opcache php-gd php-curl php-mysqlnd

         sudo systemctl start php-fpm

         sudo systemctl enable php-fpm

         setsebool -P httpd_execmem 1



Repeat steps 1-5 for another 2 Web Servers.

* Verify that Apache files and directories are available on the Web Server in /var/www and also on the NFS server in /mnt/apps. If you see the same files – it means NFS is mounted correctly. You can try to create a new file touch test.txt from one server and check if the same file is accessible from other Web Servers.

- Locate the log folder for Apache on the Web Server and mount it to NFS server’s export for logs. Repeat step №4 to make sure the mount point will persist after reboot.

* Fork the tooling source code from Darey.io Github Account to your Github account. (Learn how to fork a repo here)

+ Deploy the tooling website’s code to the Webserver. Ensure that the html folder from the repository is deployed to /var/www/html

* Open TCP port 80 to allow access from the browser. Run <public_ip_address>/index.php on a web browser to access the website.

![tooling](https://user-images.githubusercontent.com/122198373/214205618-465a173b-1433-4713-9501-90f496f26756.png)


* Disable SElinux using this command: sudo setenforce 0. To make it parmanent use the command below and set SELINUX=disabled

        sudo vi /etc/sysconfig/selinux

* Update the website’s configuration to connect to the database (in /var/www/html/functions.php file). Apply tooling-db.sql script to your database using this command mysql -h -u -p < tooling-db.sql

Create in MySQL a new admin user with username: myuser and password: password:

![MYQL_connected_GIT](https://user-images.githubusercontent.com/122198373/214205741-716ad06e-d602-46b4-b14e-c45bac3153cf.png)

Login by running the following on the browser <public_ip_address>/login.php.

![image](https://user-images.githubusercontent.com/122198373/214205724-334951b2-d0a9-4d0c-9d46-961bb0f37d24.png)
