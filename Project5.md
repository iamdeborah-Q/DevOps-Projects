# Implementing a Client Server Architecture Using MySQL Database Management System(DBMS)

Client-server refers to architecture in which two or more computers are connected together over a network to send and receive requests between one another. In this project, two computers(severs) were made to connect to each other using MySQL which is a Database Management System. 

# Project Architecture
![Client-server2_IMAGE](https://user-images.githubusercontent.com/122198373/211237432-e6565178-9aad-4702-99db-02a0cc26aaed.png)


To demonstrate a basic client-server architecture using MySQL, two virtual servers(ubuntu 20.04 LTS) were launched in AWS cloud and are designated as;

    Server A – ‘mysql-server’
    Server B – ‘mysql-client’
Note: Make sure both instances are on same subnet
![EC2_CREATED_ORIGINAL](https://user-images.githubusercontent.com/122198373/211241415-b798cc99-3841-4c2f-82f8-04b64a718beb.png)

 
 On the Mysql-server  install MySQL Server software. 
