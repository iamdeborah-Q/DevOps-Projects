# Implementing a Client Server Architecture Using MySQL Database Management System(DBMS)

Client-server refers to architecture in which two or more computers are connected together over a network to send and receive requests between one another. In this project, two computers(severs) were made to connect to each other using MySQL which is a Database Management System. 

# Project Architecture
![Client-server2_IMAGE](https://user-images.githubusercontent.com/122198373/211237432-e6565178-9aad-4702-99db-02a0cc26aaed.png)


# Step I:
To demonstrate a basic client-server architecture using MySQL, two virtual servers(ubuntu 20.04 LTS) were launched in AWS cloud and are designated as;

    Server A – ‘mysql-server’
    Server B – ‘mysql-client’
Note: Make sure both instances are on same subnet
![EC2_CREATED_ORIGINAL](https://user-images.githubusercontent.com/122198373/211241415-b798cc99-3841-4c2f-82f8-04b64a718beb.png)

 
# Step II: Installing MySQL server in Server-A
On the Mysql-server  install MySQL Server software. 
![SERVER_INSTALLED_ORIGINAL](https://user-images.githubusercontent.com/122198373/211242412-f3148b34-5001-4f5b-8649-f2417fcadd85.png)
![server_status_checked-original](https://user-images.githubusercontent.com/122198373/211242789-45dfe6c1-d9a5-434b-b161-488d4707ca58.png)


For the remote host (server A) to be able to connect to server B, mysql-server is configured to allow connections from server B:

    Edit the mysqld.cnf file: $ sudo nano /etc/mysql/mysql.conf.d/mysqld.cnf
    Change the bind-address from ‘127.0.0.1’ to ‘0.0.0.0’ to allow connection from any IP address: 
    
  ![bind_server_client](https://user-images.githubusercontent.com/122198373/211243901-bfea8a3f-2757-403a-8689-37245003955a.png)  

Configure MysQL-server and create database and a user

    Set up a password with sudo mysql_secure_installation and create a user
    Create user

![MYsql_server_USER_CREATED](https://user-images.githubusercontent.com/122198373/211245174-070da34b-030a-4401-854f-e6c972f321fc.png)

Add a rule to the security group by Opening port 3306 on Mysql-server to allow for connection. Both server can communicate using private IPs since they belong to the same subnet 

![CLIENT_sever_IP](https://user-images.githubusercontent.com/122198373/211246464-86b0b227-2f84-44f4-9916-f3e0c29df99f.png)
![IP_CONNECTED](https://user-images.githubusercontent.com/122198373/211246485-54d5acef-0de5-4d2c-b324-b07bb0869984.png)




# Step III: Install MySQL-Client form server B
On mysql client Linux Server install MySQL Client software. 
![MYSQL_CLINET_INSATLLED_ORIGINAL](https://user-images.githubusercontent.com/122198373/211243301-4ee1ea97-0856-40e8-ad95-713e489f00ac.png)
