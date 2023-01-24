
# LOAD BALANCER SOLUTION WITH APACHE



I introduced the concept of file sharing on multiple web servers to access same shared content on an independent external NFS server.

For the website solution implemented, how can we balance users request traffic across the 3 web servers we setup (horizontal scaling) so as not to put excess load on a single server whilst the others are idle.

We implement a Load Balancing solution using apache2 so as to handle routing users request to our web servers




    Two RHEL8 Web Servers

    One MySQL DB Server (based on Ubuntu 20.04)

    One RHEL8 NFS server


![image](https://user-images.githubusercontent.com/122198373/214214721-525be06c-6897-47c1-99b1-3ddbff858fe5.png)

It is important to open up TCP port 80 in the load balancers inbound rule as requests are made through it.

![image](https://user-images.githubusercontent.com/122198373/214215523-e3d91ea1-2b8f-4b2b-b358-de413424cab2.png)


# CONFIGURE APACHE AS A LOAD BALANCEER

   * Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb,
   * Open TCP port 80 on Project-8-apache-lb
 
Install apache2, libxml and then configure apache for loadbalancing via enabling proxy and proxy_balancer





    

      sudo apt update
      sudo apt install apache2 -y
      sudo apt-get install libxml2-dev

      #Enable following modules:
      sudo a2enmod rewrite
      sudo a2enmod proxy
      sudo a2enmod proxy_balancer
      sudo a2enmod proxy_http
      sudo a2enmod headers
      sudo a2enmod lbmethod_bytraffic

      #Restart apache2 service
      sudo systemctl restart apache2
      sudo systemctl status apache2




* Restart apache2 service

    sudo systemctl restart apache2

* Make sure apache2 is up and running

    sudo systemctl status apache2

* Configure load balancing:


sudo vi /etc/apache2/sites-available/000-default.conf

    #Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

    <Proxy "balancer://mycluster">
             BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
             BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
             ProxySet lbmethod=bytraffic
             # ProxySet lbmethod=byrequests
     </Proxy>

      ProxyPreserveHost On
      ProxyPass / balancer://mycluster/
      ProxyPassReverse / balancer://mycluster/

     #Restart apache server

     sudo systemctl restart apache2


