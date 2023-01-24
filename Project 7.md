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

![nfs_AWS_SUBnet_ID](https://user-images.githubusercontent.com/122198373/214191476-ce69762d-cfd0-43cb-94a8-197225b9f852.png)




Configure access to NFS for clients within the same subnet by editing the following file

        sudo vi /etc/exports

        /mnt/apps <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
        /mnt/logs <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)
        /mnt/opt <Subnet-CIDR>(rw,sync,no_all_squash,no_root_squash)

        Esc + :wq!

        sudo exportfs -arv
        
![nfs_expoted_GIGT](https://user-images.githubusercontent.com/122198373/214193452-5dc56c93-314d-48d1-8ef6-ee973e966a13.png)

Checked which port is used ny the NFS server and add it as an inbound rule. rpcinfo -p | grep nfs also for NFS server to be accesible the following inbound ports had to be opened TCP 111, UDP 111, UDP 2049
![image](https://user-images.githubusercontent.com/122198373/214193852-7d7fa5f2-62fc-45d2-a767-92c4ba15b4e0.png)

