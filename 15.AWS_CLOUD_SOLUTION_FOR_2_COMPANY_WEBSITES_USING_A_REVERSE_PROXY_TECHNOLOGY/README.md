## AWS CLOUD SOLUTION FOR 2 COMPANY WEBSITES USING A REVERSE PROXY TECHNOLOGY



In this project, I will build a secure infrastructure inside AWS VPC (Virtual Private Cloud) network for a fictitious company (Choose an interesting name for it) that uses WordPress CMS for its main business website, and a Tooling Website (https://github.com//tooling) for their DevOps team. As part of the company’s desire for improved security and performance, a decision has been made to use a reverse proxy technology from NGINX to achieve this.

Cost, Security, and Scalability are the major requirements for this project. Hence, implementing the architecture designed below, ensure that infrastructure for both websites, WordPress and Tooling, is resilient to Web Server’s failures, can accomodate to increased traffic and, at the same time, has reasonable cost.

## Project Design Architecture Diagram

![image](https://user-images.githubusercontent.com/122198373/235584926-2ec2d10f-7f36-41d9-85fd-b548f784a7f1.png)


STEP 1: Starting Off AWS Project


Properly configure your AWS account and Organization Unit Watch How To Do This Here
Create an AWS Master account. (Also known as Root Account)

Within the Root account, create a sub-account and name it DevOps. (You will need another email address to complete this)

Within the Root account, create an AWS Organization Unit (OU). Name it Dev. (We will launch Dev resources in there) Move the DevOps account into the Dev OU.

Login to the newly created AWS account using the new email address.

Create a free domain name for your fictitious company at Freenom domain registrar here.

Create a hosted zone in AWS, and map it to your free domain from Freenom. Watch how to do that here


![image](https://user-images.githubusercontent.com/122198373/235619396-fa99f889-864f-4403-a323-e7145d4e1e02.png)
      
      NOTE : As you proceed with configuration, ensure that all resources are appropriately tagged, for example:

                Project:
                Environment:
                Automated: (If you create a recource using an automation tool, it would be )

STEP II : Setting Up the Infractructure

1. Create VPC ( Virtual Private Cloud)

![Screenshot 2023-05-02 050130](https://user-images.githubusercontent.com/122198373/235823233-8331af59-6601-4e14-9ecb-82a05492bc38.png)









2. Create subnets as shown in the architecture



![image](https://user-images.githubusercontent.com/122198373/235823045-21123c3d-4795-453b-9430-66b6eedf6b03.png)





3. Create a route table and associate it with public subnets

![rtb](https://user-images.githubusercontent.com/122198373/235828171-d4160522-c29c-43cd-b0a9-d7310762fad5.png)









4. Create a route table and associate it with private subnets


![image](https://user-images.githubusercontent.com/122198373/235828132-f6b12171-e4d4-48db-ba1c-c2d7da9ed332.png)

