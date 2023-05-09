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

  2. Create a new repository called ansible-config-mgt on github and set up webhook to trigger the ansible builts

          https://<jenkins_url:port/github-webhooks>


  
![WEBHOK_NEW](https://user-images.githubusercontent.com/122198373/237032520-17041eb8-44d1-404d-96fe-3a712b66ddf1.png)



3. Create a new free style project on the Jenkins server and point it to your ‘ansible-config-mgt’ repository.
  

![image](https://user-images.githubusercontent.com/122198373/237037290-7b89d7c7-6337-45d1-bdef-ab7e9db9756c.png)



4. Test your setup by making some change in README.MD file in master branch and make sure that builds starts automatically and Jenkins saves the files (build artifacts) in following folder 



![image](https://user-images.githubusercontent.com/122198373/237040037-4a1ea754-c20a-4a66-8ce0-43f91af36fdc.png)




Note: Trigger Jenkins project execution only for /main (main) branch.

Now your setup will look like this:


![image](https://user-images.githubusercontent.com/122198373/237041043-89adf5a3-0895-434f-9e71-15fcd6721271.png)




Tip: Every time you stop/start your Jenkins-Ansible server – you have to reconfigure GitHub webhook to a new IP address, in order to avoid it, it makes sense to allocate an Elastic IP to your Jenkins-Ansible server (you have done it before to your LB server in Project 10). Note that Elastic IP is free only when it is being allocated to an EC2 Instance, so do not forget to release Elastic IP once you terminate your EC2 Instance.

Step II – Prepare your development environment using Visual Studio Code

1. First part of ‘DevOps’ is ‘Dev’, which means you will require to write some codes and you shall have proper tools that will make your coding and debugging comfortable – you need an Integrated development environment (IDE) or Source-code Editor. There is a plethora of different IDEs and Source-code Editors for different languages with their own advantages and drawbacks, you can choose whichever you are comfortable with, but we recommend one free and universal editor that will fully satisfy your needs – Visual Studio Code (VSC).
2. After you have successfully installed VSC, configure it to connect to your newly created GitHub repository.
3. Clone down your ansible-config-mgt repo to your Jenkins-Ansible instance

