
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



![image](https://user-images.githubusercontent.com/122198373/214220256-fec8c291-9a84-40e8-90d3-689add689934.png)



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


Note: Only 2 servers were added to the proxy list and also other ways to route traffic aside bytraffic includes byrequests, bybusyness, heartbeats which can be specified in ProxySet lbmethod=? .


![image](https://user-images.githubusercontent.com/122198373/214220367-ab14443b-b9bf-4b9d-9a18-aab810ea1541.png)



If in the Project-7 you mounted /var/log/httpd/ from your Web Servers to the NFS server – unmount them and make sure that each Web Server has its own log directory.

Restart the apache2 server sudo systemctl restart apache2

On the web browser, test the load balancing connection using the public Ip address of our load balancer server. 

![image](https://user-images.githubusercontent.com/122198373/214222734-e7847e60-f08c-4259-96d4-d7fd77e87668.png)



## CONFIGURE DNS NAMES (Locally)

In order not to always provide webserver private ip address whenever a new web server needs to be added on the list of loadbalancer proxy, we can specify them on the hosts file and provide a domain name for each which suites us

        sudo vi /etc/hosts

![image](https://user-images.githubusercontent.com/122198373/214223976-7ac246e2-fb3d-4780-9a7b-92b3850f588d.png)

![image](https://user-images.githubusercontent.com/122198373/214227651-3306df4e-6380-44be-9d80-70c7710dd185.png)


To see this is play we can curl our DNS name on the loadbalancer server. Since the DNS names are local DNS configuration we can only access them locally hence the loadbalancer uses them locally to target the backend web servers


Server-1
![image](https://user-images.githubusercontent.com/122198373/214227319-2af0c0ea-1cc3-423f-af99-1a08feac93f7.png)

Server-2

![image](https://user-images.githubusercontent.com/122198373/214227809-35841806-496d-4546-a949-a9ab4ef27059.png)
