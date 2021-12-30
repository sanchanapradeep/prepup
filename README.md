# Prepup
Interview prepup

Horizontal scalable
Vertical scalable

Horizonal scalable is cheep
Mutiple number of same instances willbe running on a cluster, which is managed under a load balancer.
This load balancer evenly distributes load (requests from your users) onto your group/cluster of  application servers.

# prob: For example, user Steve interacts with your service, he may be served at his first request by server 2, then with his second request by server 9 and then maybe again by server 2 on his third request. 

## Steve should always get the same results of his request back, independent what server he  “landed on”. That leads to the first golden rule for scalability: every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory. 

# sol: Sessions need to be stored in a centralized data store which is accessible to all your application servers. It can be an external database or an external persistent cache, like Redis. An external persistent cache will have better performance than an external database. By external I mean that the data store does not reside on the application servers. Instead, it is somewhere in or near the data center of your application servers. 

# Prob:what about deployment? How can you make sure that a code change is sent to all your servers without one server still serving old code? This tricky problem is fortunately already solved by the great tool Capistrano. It requires some learning, especially if you are not into Ruby on Rails, but it is definitely both the effort.

# Sol: After “outsourcing” your sessions and serving the same codebase from all your servers, you can now create an image file from one of these servers (AWS calls this AMI - Amazon Machine Image.) Use this AMI as a “super-clone” that all your new instances are based upon. Whenever you start a new instance/clone, just do an initial deployment of your latest code and you are ready!

https://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones

#2. Next bottle neck is Database
#Prob: your servers can now horizontally scale and you can already serve thousands of concurrent requests. But somewhere down the road your application gets slower and slower and finally breaks down. The reason: your database. It’s MySQL, isn’t it?

#Sol 1:Path #1 is to stick with MySQL and keep the “beast” running. Hire a database administrator (DBA,) tell him to do master-slave replication (read from slaves, write to master) and upgrade your master server by adding RAM, RAM and more RAM. he needs to implemnet “sharding”, “denormalization” and “SQL tuning”

#sol 2:
#1. denormalise the schema ( no joins in mysql like nosqlDB)  
#2. intoduce cache:But even if you successfully switch to the latest and greatest NoSQL database and let your app do the dataset-joins, soon your database requests will again be slower and slower. You will need to introduce a cache.

https://www.lecloud.net/post/7994751381/scalability-for-dummies-part-2-database



https://www.lecloud.net/post/9246290032/scalability-for-dummies-part-3-cache



https://www.lecloud.net/post/9699762917/scalability-for-dummies-part-4-asynchronism

