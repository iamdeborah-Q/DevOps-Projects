# LOAD BALANCER SOLUTION WITH NGINX AND SSL/TLS

In this project I would be implementing a Nginx Load Balancer, create a domain(using AWS Route 53), and also to configure a secure connection using SSL/TLS certificates, which secures the website from Man in the Middle attack. 

## CONFIGURE NGINX AS A LOAD BALANCER
* Create an EC2 VM based on Ubuntu Server 20.04 LTS and name it Nginx LB (do not forget to open TCP port 80 for HTTP connections, also open TCP port 443 – this port is used for secured HTTPS connections)
- Update /etc/hosts file for local DNS with Web Servers’ names (e.g. Web1 and Web2) and their local IP addresses
* Install and configure Nginx as a load balancer to point traffic to the resolvable DNS names of the webservers
- Update the instance and Install Nginx
        
               sudo apt install nginx
        
- Open the default nginx configuration file and paste the text below:
              
              sudo vi /etc/nginx/nginx.conf
              
       
    
      #insert following configuration into http section

         upstream myproject {
            server Web1 weight=5;
            server Web2 weight=5;

          }

        server {
            listen 80;
            server_name www.domain.com;
            location / {
              proxy_pass http://myproject;
            }
          }
          
          #comment out this line
          #include /etc/nginx/sites-enabled/*;


- Restart Nginx and make sure the service is up and running

            sudo systemctl restart nginx
            sudo systemctl status nginx



## REGISTER A NEW DOMAIN NAME AND CONFIGURE SECURED CONNECTION USING SSL/TLS CERTIFICATES

* Register a new domain name with any registrar of your choice in any domain zone (e.g. .com, .net, .org, .edu, .info, .xyz or any other)
        
- Assign an Elastic IP to your Nginx LB server and associate your domain name with this Elastic IP
        
- Update A record in your registrar to point to Nginx LB using Elastic IP address
 
     
![Record_Created](https://user-images.githubusercontent.com/122198373/218596336-8212a99d-8e55-4655-b174-48e1eb634cd4.png)




   
   
   
 
* Configure Nginx to recognize the new domain name. This was done by Updating the /etc/nginx/nginx.conf file with

                server_name www.<your-domain-name.com>


- Install certbot and request for an SSL/TLS certificate Make sure snapd service is active and running

               sudo systemctl status snapd

- Install certbot

               sudo snap install --classic certbot


![image](https://user-images.githubusercontent.com/122198373/218615489-ba9bb6b3-acb9-4717-a7c4-a1c311d6c6f3.png)



- Request your certificate (follow the certbot instructions – you will need to choose which domain you want your certificate to be issued for, domain name will be looked up from nginx.conf file so make sure you have updated it on step 4).


![image](https://user-images.githubusercontent.com/122198373/218607162-4e7bf7ef-d3b1-4279-812c-eb65fa19034c.png)





* Set up periodical renewal of your SSL/TLS certificate By default, LetsEncrypt certificate is valid for 90 days, so it is recommended to renew it at least every 60 days or more frequently.

- You can test renewal command in dry-run mode

                sudo certbot renew --dry-run

- Best pracice is to have a scheduled job that to run "renew" command periodically. Let us configure a cronjob to run the command twice a day. 
   To do so, lets edit the crontab file with the following command


                crontab -e

- Add following line:

                * */12 * * *   root /usr/bin/certbot renew > /dev/null 2>&1


![image](https://user-images.githubusercontent.com/122198373/218602700-5961ccb9-b9da-48df-917a-cabfeb4aca34.png)
