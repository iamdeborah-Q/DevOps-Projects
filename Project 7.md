## DEVOPS TOOLING WEBSITE SOLUTION



In previous project [(Project 6()] I implemented a WordPress based solution that is ready to be filled with content and can be used as a full fledged website or blog. Moving further I will add some more value to my solutions that a member of a DevOps team could utilize. 

In this project i will introduce a set of DevOps tools that will help my team in their day to day activities in managing, developing, testing, deploying and monitoring different projects.


## ARCHITECTURAL DESIGN






This project builds on the 3-tier architecture we saw earlier by adding a Network file System(NFS) to our system architecture(website). The NFS host the static files needed to server our site to the public while the database stores the dynamic data. This architecture makes our webserver stateless: meaning we can easily configure and scale our webserver up or down as need arises.
