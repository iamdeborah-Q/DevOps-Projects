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

* Export the mounts for webservers’ subnet CIDR to connect as clients. For simplicity, you will install your all three Web Servers inside the same subnet, but in production set up you would probably want to separate each tier inside its own subnet for higher level of security. To check your subnet cidr – open your EC2 details in AWS web console and locate ‘Networking’ tab and open a Subnet link:

