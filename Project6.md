## IMPLEMENTING WORDPRESS WEB SOLUTION

Client-Server architecture describe the relationship between two or more connectecd computer sharing data across the network. The computer that request for data is known as the client while the one that processes the request is known as the server.
In this project, two computers(severs) were made to connect to each other using MySQL which is a Database Management System. The following steps were taken in setting up a client-server architecture using MySQL:

## STEP I:PREPARING WEB SERVER

To demonstrate Client-Server architecture we will be using two Ec2 instance with mysql-server and mysql-client respectively.

   * Create and configure two Linux-based virtual servers (EC2 instances in AWS).
   * On the EBS console, create 3 storage volumes for the instance. This serves as additional external storage to our EC2 machine

![VOLUME_1](https://user-images.githubusercontent.com/122198373/214749494-11626b5d-b809-4283-846d-2bc16cd18ae8.png)

![Screenshot_20221214_011556](https://user-images.githubusercontent.com/122198373/214753235-49e4f0b7-350a-4e9f-9f1a-535c1c469e04.png)

* SSH into the instance and on the EC2 terminal, view the disks attached to the instance. This is achieved using the lsblk command.
   
![PIC_1](https://user-images.githubusercontent.com/122198373/215355522-baefa535-8d93-4472-a507-a9e5aa9a7b62.png)

- To see all mounts and free spaces on our server

![PIC_2](https://user-images.githubusercontent.com/122198373/215355862-f43f43f6-fddc-461c-b8d2-5afb7de6e015.png)

- Create single partitions on each volume on the server using gdisk  

![PIC_3](https://user-images.githubusercontent.com/122198373/215355878-0469d2aa-f256-4b50-b909-2e7dbab2c8cc.png)

- Installing LVM2 package for creating logical volumes on a linux server.

![image](https://user-images.githubusercontent.com/122198373/215356041-10273206-8653-4017-8505-a701e91aee6a.png)

- Creating Physical Volumes on the partitioned disk volumes
    sudo pvcreate <partition_path>

![PVCreate_1](https://user-images.githubusercontent.com/122198373/215356233-d5f05a0e-aa97-4b10-b01d-50e2b051dc2d.png)

- Next we add up each physical volumes into a volume group
   sudo vgcreate <grp_name> <pv_path1> ... <pv_path1000> 
   
![VG_Create](https://user-images.githubusercontent.com/122198373/215356508-e6a6b57b-b85e-41bc-8910-ae735f38d4e1.png)

- Creating Logical volumes for the volume group   sudo lvcreate -n <lv_name> -L <lv_size> <vg_name>

![image](https://user-images.githubusercontent.com/122198373/215356612-47fc4e1f-c7aa-4a07-b101-7eca7b687634.png)

- Our logical volumes are ready to be used as filesystems for storing application and log data.
            Creating filesystems on the both logical volumes
            
 ![FileSystem_Created](https://user-images.githubusercontent.com/122198373/215356868-55aa8eee-5254-4ba2-a27b-9e7f560cc5a3.png)
 
- The apache webserver uses the html folder in the var directory to store web content. We create this directory and also a directory for collecting log data of our application

![Var_CREATED](https://user-images.githubusercontent.com/122198373/215357088-0b9daef8-40b5-4709-b506-3f0d1b4242cc.png)

- For our filesystem to be used by the server we mount it on the apache directory . Also we mount the logs filesystem to the log directory

![MOUNT_CREATED_WEB](https://user-images.githubusercontent.com/122198373/215357433-8dda5f3e-12d2-48cd-be8d-0a6edd161426.png)

- Mount logs logical volume to var logs

![Log_VAR_Mounted](https://user-images.githubusercontent.com/122198373/215357695-42194197-df10-465e-ae6c-5e7c37578913.png)

- Restoring back var logs data into var logs
    
![ReSynC_created](https://user-images.githubusercontent.com/122198373/215357777-31d69ad1-f587-4011-916e-4130032be5c3.png)

## Persisting Mount Points

 - To ensure that all our mounts are not erased on restarting the server, we persist the mount points by configuring the /etc/fstab directory

 - Sudo blkid to get UUID of each mount points

![UUI_Created](https://user-images.githubusercontent.com/122198373/215358161-10670f1c-97c1-43d0-8963-a6a2a575010a.png)

- Sudo vi /etc/fstab to edit the file

![VI_fstab](https://user-images.githubusercontent.com/122198373/215358468-84c5235d-d2c6-4c03-bfae-2314ef1a097c.png)

- Testing mount point persistence

![fstab_verified](https://user-images.githubusercontent.com/122198373/215358705-a7e69f7e-fd11-474e-b1a3-898c56604fd3.png)

## STEP II: PREPARING DATABASE SERVER
- Repeated all the steps taken to configure the web server on the db server. Changed the apps-lv logical volume to db-lv

![db_verified](https://user-images.githubusercontent.com/122198373/215359020-b8d92b89-80b9-4873-8374-c08daa3560d1.png)

## STEP III: CONFIGURATION WEB SERVER

- Run updates and install httpd on web server

yum install -y update
- sudo yum -y install wget httpd php php-mysqlnd php-fpm php-json

         Start web server

- Starting_web_server

![HTTL_WEB_Created](https://user-images.githubusercontent.com/122198373/215365043-7d56d77a-73dd-489e-8d4c-8051529eee96.png)


 - Installing php and its dependencies
 

        sudo yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-9.noarch.rpm
        sudo yum install yum-utils http://rpms.remirepo.net/enterprise/remi-release-9.rpm
        sudo yum module list php
        sudo yum module reset php
        sudo yum module enable php:remi-8.2
        sudo yum install php php-opcache php-gd php-curl php-mysqlnd
        sudo systemctl start php-fpm
        sudo systemctl enable php-fpm
        setsebool -P httpd_execmem 1

  - Restarting Apache: sudo systemctl restart httpd

  - Downloading wordpress and moving it into the web content directory
  

        mkdir wordpress
        cd   wordpress
        sudo wget http://wordpress.org/latest.tar.gz
        sudo tar xzvf latest.tar.gz
        sudo rm -rf latest.tar.gz
        cp wordpress/wp-config-sample.php wordpress/wp-config.php
        cp -R wordpress /var/www/html/

 - Configure SELinux Policies
 

        sudo chown -R apache:apache /var/www/html/wordpress
        sudo chcon -t httpd_sys_rw_content_t /var/www/html/wordpress -R
        sudo setsebool -P httpd_can_network_connect=1
      

- Starting database server

![SET_DB_mysql](https://user-images.githubusercontent.com/122198373/215360158-b181bb9b-1f9d-484a-be61-bebda760a35a.png)

## STEP IV: INSTALLING MySQL ON DATABASE (DB) SERVER

        sudo yum update
        sudo yum install mysql-server
        
- To ensure that database server starts automatically on reboot or system startup


        sudo systemctl restart mysqld
        sudo systemctl enable mysqld

## STEP V: SETTING UP THE DATABASE (DB) SERVER

![DB_mysql](https://user-images.githubusercontent.com/122198373/215365510-5a36523f-b49e-4637-8647-d533a8816ad5.png)

- Ensure that we add port 3306 on our db server to allow our web server to access the database server.

![Screenshot 2022-12-26 143013](https://user-images.githubusercontent.com/122198373/215365691-5d2643b5-fca0-471f-946f-86700f6465ca.png)



## Connecting Web Server to DB Server

- Installing mySQl client on the web server so we can connect to the db server


        sudo yum install mysql
        sudo mysql -u admin -p -h <DB-Server-Private-IP-address>

![image](https://user-images.githubusercontent.com/122198373/215366356-efa875eb-8cfa-47ff-8e71-6b8dbbe9552d.png)

- On the web browser, access web server using the public ip address of the server

![image](https://user-images.githubusercontent.com/122198373/215366557-745097b5-9fec-4d91-be51-ac8ccd273043.png)


![wordpressImage4](https://user-images.githubusercontent.com/122198373/215366772-528d94b0-7f4b-433b-b0e4-13266a5552ca.png)




