# ANSIBLE CONFIGURATION MANAGEMENT



Ansible Client as a Jump Server (Bastion Host)

A Jump Server (sometimes also referred as Bastion Host) is an intermediary server through which access to internal network can be provided. If you think about the current architecture you are working on, ideally, the webservers would be inside a secured network which cannot be reached directly from the Internet. That means, even DevOps engineers cannot SSH into the Web servers directly and can only access it through a Jump Server – it provide better security and reduces attack surface.

On the diagram below the Virtual Private Network (VPC) is divided into two subnets – Public subnet has public IP addresses and Private subnet is only reachable by private IP addresses.

![image](https://user-images.githubusercontent.com/122198373/237016952-787a16f6-7bf5-4508-be21-a82b8cda2e78.png)




STEP-I - Install and Configure Ansible on EC2 Instance
  
  1. Install ansible on jenkins server and rename it to Jenkins-Ansible. (refer to project on how to install jenkins server)
 
          sudo apt update
          sudo apt install ansible
          ansible --version


![image](https://user-images.githubusercontent.com/122198373/237021515-1d73d94d-ed5a-4312-ac31-adff4ecd3b67.png)

  2. Create a new repository called ansible-config-mgt on github and set up webhook to trigger the ansible built

          https://<jenkins_url:port/github-webhooks>

   On the Jenkins server, create a job called ansible and configure automatic builds when a trigger is made on the ansible-config-mgt directory via GITScm polling.
   Create a new Freestyle project ansible in Jenkins and point it to your ‘ansible-config-mgt’ repository.

