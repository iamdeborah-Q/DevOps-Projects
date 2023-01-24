## 



In project seven, We extended our 3- tier architecture for website by adding an NFS server. NFS holds static files shared across the webserver. This architecture makes our webservers stateless. However In our previous architecture, it implies we wll need to access each webserver directly from its public IP. This is not a good use of our resources because one server may unintentionally process all the request.
To solve this problem, we need a single point of entry which routes traffic accross our webserver(3). Depending our configuration, the webserver can equally share the incoming request.
Load Balancers provide this single point of entry. They can be software based or hardware based. Examples of software based load balancer are:
