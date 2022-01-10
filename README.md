# Prepup
Interview prepup
# System design

Horizontal scalable
Vertical scalable

Horizonal scalable is cheep
Mutiple number of same instances willbe running on a cluster, which is managed under a load balancer.
This load balancer evenly distributes load (requests from your users) onto your group/cluster of  application servers.

 prob: For example, user Steve interacts with your service, he may be served at his first request by server 2, then with his second request by server 9 and then maybe again by server 2 on his third request. 

 Steve should always get the same results of his request back, independent what server he  “landed on”. That leads to the first golden rule for scalability: every server contains exactly the same codebase and does not store any user-related data, like sessions or profile pictures, on local disc or memory. 

 sol: Sessions need to be stored in a centralized data store which is accessible to all your application servers. It can be an external database or an external persistent cache, like Redis. An external persistent cache will have better performance than an external database. By external I mean that the data store does not reside on the application servers. Instead, it is somewhere in or near the data center of your application servers. 

 Prob:what about deployment? How can you make sure that a code change is sent to all your servers without one server still serving old code? This tricky problem is fortunately already solved by the great tool Capistrano. It requires some learning, especially if you are not into Ruby on Rails, but it is definitely both the effort.

 Sol: After “outsourcing” your sessions and serving the same codebase from all your servers, you can now create an image file from one of these servers (AWS calls this AMI - Amazon Machine Image.) Use this AMI as a “super-clone” that all your new instances are based upon. Whenever you start a new instance/clone, just do an initial deployment of your latest code and you are ready!

https://www.lecloud.net/post/7295452622/scalability-for-dummies-part-1-clones

#  2. Next bottle neck is Database
 Prob: your servers can now horizontally scale and you can already serve thousands of concurrent requests. But somewhere down the road your application gets slower and slower and finally breaks down. The reason: your database. It’s MySQL, isn’t it?
 
 NAT Gateway - will be created in public subnet 
 if u want to talk to internet  devices has to define only public subnet.

# Sol 1:
Path #1 is to stick with MySQL and keep the “beast” running. Hire a database administrator (DBA,) tell him to do master-slave replication (read from slaves, write to master) and upgrade your master server by adding RAM, RAM and more RAM. he needs to implemnet “sharding”, “denormalization” and “SQL tuning”

# sol 2:
 1. denormalise the schema ( no joins in mysql like nosqlDB)  
 2. intoduce cache:But even if you successfully switch to the latest and greatest NoSQL database and let your app do the dataset-joins, soon your database requests will again be slower and slower. You will need to introduce a cache.

https://www.lecloud.net/post/7994751381/scalability-for-dummies-part-2-database

# introduce cache
# 1 - Cached Database Queries
# 2 - Cached Objects
 your class assemble a dataset from your database and then store the complete instance of the class or the assembed dataset in the cache.
 when your class has finished the “assembling” of the data array, directly store the data array, or better yet the complete instance of the class, in the cache! This allows you to easily get rid of the object whenever something did change and makes the overall operation of your code faster and more logical.

# Some ideas of objects to cache:

# user sessions (never use the database!)
# fully rendered blog articles
# activity streams
# user<->friend relationships 
 With Redis and a clever key’ing there may be a chance that you even can get completly rid of a database. But if you just need to cache, take Memcached, because it scales like a charm.


https://www.lecloud.net/post/9246290032/scalability-for-dummies-part-3-cache

# Asynchronism
For long running queries u need to add async mechanism
# Async #1
 Very often this paradigm is used to turn dynamic content into static content.  Pages of a website, maybe built with a massive framework or CMS, are pre-rendered and locally stored as static HTML files on every change. Often these computing tasks are done on a regular basis, maybe by a script which is called every hour by a cronjob. This pre-computing of overall general data can extremely improve websites and web apps and makes them very scalable and performant. Just imagine the scalability of your website if the script would upload these pre-rendered HTML pages to AWS S3 or Cloudfront or another Content Delivery Network! Your website would be super responsive and could handle millions of visitors per hour!

# Async #2
 A user comes to your website and starts a very computing intensive task which would take several minutes to finish. So the frontend of your website sends a job onto a job queue and immediately signals back to the user: your job is in work, please continue to the browse the page. The job queue is constantly checked by a bunch of workers for new jobs. If there is a new job then the worker does the job and after some minutes sends a signal that the job was done. The frontend, which constantly checks for new “job is done” - signals, sees that the job was done and informs the user about it. I know, that was a very simplified example. 

 If you now want to dive more into the details and actual technical design, I recommend you take a look at the first 3 tutorials on the RabbitMQ website. RabbitMQ is one of many systems which help to implement async processing. You could also use ActiveMQ or a simple Redis list. The basic idea is to have a queue of tasks or jobs that a worker can process. Asynchronism seems complicated, but it is definitely worth your time to learn about it and implement it yourself. Backends become nearly infinitely scalable and frontends become snappy which is good for the overall user experience. 


https://www.lecloud.net/post/9699762917/scalability-for-dummies-part-4-asynchronism



# Performance vs scalability
A service is scalable if it results in increased performance in a manner proportional to resources added. Generally, increasing performance means serving more units of work, but it can also be to handle larger units of work, such as when datasets grow.1

# Another way to look at performance vs scalability:

* If you have a performance problem, your system is slow for a single user.
* If you have a scalability problem, your system is fast for a single user but slow under heavy load.


# Performance Vs Scalability

* Perfomance ->if you have performance problem, your system is slow for a single user.
* scalability -> if you have scalability problem, your system is fast for single user but slow under heavy load.

# Latency Vs Throughput
* You should strive for maximal throughput with acceptable latency

https://www.slideshare.net/jboner/scalability-availability-stability-patterns/

# CAP theorem
C - consistency
A - Availability
P - Partitioning


At any point You can have only 2
# Centralized System (like RDBMS)
* CA

 ACID -> Atomic Consistance Isolate Durable
# Disturbuted system ( we will have network partition P)
* CP 
* AP

# CAP in practice only 2 systems 
* There is only one choice to make, in case of network partion

BASE -> Basically Available Soft state Eventually


# Availablity pattern:
* Fail over
* Replication
 
Replication: 
 *  Master slave
 * Tree replication
 * Master Master
 * Buddy replication


# Scaling RDBMS: sharding
* Partationing
* Replication
![image](https://user-images.githubusercontent.com/50334391/147863768-83c1a704-62fb-424b-b806-bca0e4663593.png)
![image](https://user-images.githubusercontent.com/50334391/147863776-f06ceeda-6185-4eb6-b7db-ba25adb5ad42.png)

Eventually good enough

![image](https://user-images.githubusercontent.com/50334391/147863794-a9b1b8a9-16e3-44c0-ad31-ee2ab1fc11c2.png)
When RDBMS is not good
Scaling the reads to RDBMS is hard
Scaling writes to RDMS is impossible

# sol: NOSQL ( Not only SQL)
Key value database
column database
Document database
Graph database
Datastructure Database

* Performance vs scalability
* Latency vs throughput
* Availability vs consistency
# Performance vs scalability
If you have a performance problem, your system is slow for a single user.
If you have a scalability problem, your system is fast for a single user but slow under heavy load.

# Latency vs throughput
Latency is the time to perform some action or to produce some result.

Throughput is the number of such actions or results per unit of time.

Generally, you should aim for maximal throughput with acceptable latency.

#  Availability vs consistency
  * CAP Theorem
   https://robertgreiner.com/cap-theorem-revisited/
   * Consistency - A read is guaranteed to return the most recent write for a given client.
   * Availability - A non-failing node will return a reasonable response within a reasonable amount of time (no error or timeout).
Partition Tolerance - The system will continue to function when network partitions occur.


# VCN
Virtual cloud network : similar to corporate network ..
only with in the corporate network things can be accessed.. but not outside ones..
Both applications and DB..can't be accessed from outside..
Public  subnet
private subnet

# Public subnet: External facing network..
if u want to be accessed from internet .. u hav to keep it in public subnet like applications
# Private subnet: with in the VCN
only can be accessed with in the network.. like DB
![image](https://user-images.githubusercontent.com/50334391/148666623-2fcafdea-3ba9-43e4-8bd9-aefdabb79da6.png)
Route table routes the network traffic to the desired server
1. as soon as u try to hit the ip.. it verifies in the route table and ..route to desired destination.. 
2. if ur ip is 172.31.0.0/16 and target local .. it means if the ip is with in the range then route to local server
3. 0.0.0.0/0 igw-> route to internet gateway.. that means public network.
Public Subnet  access internet via Internet gatway
Private subnet .. if it need to download patches and any installation it access internet 
3 ways u can do it 

![image](https://user-images.githubusercontent.com/50334391/148666771-9559d181-5e06-4351-a067-4eddb2622a79.png)

# These NAT gateways created in public subnet which allows private subnet to communicate to internet.
