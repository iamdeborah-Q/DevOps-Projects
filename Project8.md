## LOAD BALANCER SOLUTION WITH APACHE



I introduced the concept of file sharing on multiple web servers to access same shared content on an independent external NFS server.

For the website solution implemented, how can we balance users request traffic across the 3 web servers we setup (horizontal scaling) so as not to put excess load on a single server whilst the others are idle.

We implement a Load Balancing solution using apache2 so as to handle routing users request to our web servers
