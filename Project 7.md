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



Spin up a new EC2 instance with RHEL Linux 8 Operating System.

Based on your LVM experience from Project 6, Configure LVM on the Server.

Instead of formating the disks as ext4 you will have to format them as xfs

Creat 3 Logical Volumes. lv-apps, lv-logs, and lv-opt

Create mount points on /mnt directory for the logical volumes as follow:

     Mount lv-apps on /mnt/apps – To be used by webservers
     Mount lv-logs on /mnt/logs – To be used by webserver logs
     Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8










