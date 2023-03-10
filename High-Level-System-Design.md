# High Level System Design Core Concepts

---

# `Microservice vs Monolith vs SOA`

- It is an architectural development style in which the application is made up of loosely-coupled services handling only small portions of the functionality and data by communicating with each other directly using lightweight protocols like HTTP.
- In monolithic applications there is usually a single codebase containing all the required functionalities of application.

![](https://firebasestorage.googleapis.com/v0/b/boom-b9a18.appspot.com/o/HLD%2F1.png?alt=media&token=a3d83192-44f0-47e9-829f-7929dc04b256)

### Principals of Microservices

- Single responsibility
- Built around business capabilities
- Design for failure

### Microservice vs SOA

- SOA evolved in order to deal with the problems in monolithic architecture and became popular in the early 2000s
- In SOA, the large application is split up into multiple smaller services that are deployed independently but don’t communicate with each other directly.
- ESB is a middleware/server in SOA which allows services exposed to it to communicate even by different protocols or message standards.
- There is no guideline to have an independent database in SOA for each service unlike in Microservices
- The fundamental idea of the evolution of microservices from SOA is to refine availability and loose coupling by introducing additional guidelines.


### Disadvantage of Microservice Architecture

- Microservices are costly in terms of network usage.
- Microservices are less secure relative to monolithic applications due to the inter-services communication over the network.
- Skilled developers are required to work with microservices architecture.

---

# `Scaling`

### Vertical vs Horizontal Scaling

- In vertical scaling, we add more power(CPU, RAM, and DISK) to the existing server or using a bigger machine.
- In vertical scaling servers, scale up or scale down depending on the traffic.
- In horizonal scaling, we scale by adding more servers to the system by renting a number of machines and dividing the load among them.
- In horizontal scaling, servers scale out or scale in depending on the traffic.

### Advantages and Disadvantages of Horizontal & Vertical Scaling.

- There is always a limit on vertical scaling as we can only add power upto an extent.
- There is no limit to horizontal scaling since we can have as many as servers we want and distribute the load among them.
- Also there is high availability in horizontal scaling as there might be less single point of faliures.
- However we need skilled engineers for architecting horizontally scaling systems as complexity is higher than Vertical scaling.

### Scaling EC2 Instances

- We can scale EC2 instances both vertically and horizontally
- Suppose we are using t3.micro and there is a traffic boom,then the Application load balancer will spin up a m5.2XLarge and direct all the traffic to it .This is called vertical scaling.
- Suppose we are using a t3.micro in in EC2 autoscaling group, and there is a traffic boom then AWS will spin up new instances of specified criteria and distribute load among them.

### Scaling AWS Lambda

- AWS Lambda can only scale horizontally.
- When your lambda function is invoked, AWS Lambda allocates an instance of it to process the event having resources specified by developer.
- When the function code finishes running, it can handle another request.
- If the function is invoked again while a request is still being processed, another instance is allocated, which increasing the function's concurrency.
- When the labda instances are free, they scale in.
- However maximum concurrency limit of a particular Lambda function has to be specified by the developer.

### Scaling Container (Kubernetes)

- In kubernetes, container is running inside a pod which itself is running inside EC2 instance.
- The pod keeps a track of resource utilization of itsef.
- When the traffic comes in ,it will scale inside EC2 using Horizontal Pod Autoscaler (HPA).
- As the smallest unit of autoscaling in kubernetes is pod, full pod will be scaled horizontally.
- When EC2 is filled up, we have to scale the EC2 instance too which is called cluster autoscaling.



# `Load Balancer`

- When using scalable microservices, a client needs to be able to route its requests to one of the multiple backend server instances and make sure that no server gets overloaded.
- Also when a server instance faces downtime , LB distributes its requests among other relevant server instances.

### Types of Load Balancers

- `Application Load balancers(Layer 7)` distribute requests based upon data found in application layer protocols such as HTTP(eg. Cookies,Headers etc).
- `Network load balancers(Layer 4)` distribute requests based upon data found in network and transport layer protocols (IP, TCP, FTP, UDP).

### Client-Side Balancing VS Server-Side Load Balancing

- `Client-Side Load balancing`: Client fetches list of registered backend servers instances using service registry/naming server (eg. Netflix Eureka) and then route the request to one of these backend instances using client-side load balancing libraries like Netflix Ribbon.

- `Server-Side Load Balancing`: All backend server instances are registered with a central load balancer(Eg. Netflix Zuul) and are registered with service registry.A client requests this load balancer which then routes the request to one of the server instances.


### Classical Load balancing Algorithms

- **Round Robin Algorithms**: Distributes requests in rotation based on FCFS. 
- **Least Connection**: Distributes to servers with least active connections.
- **Resource Based**: Distributes Requests based on resources available with an instance.
- **Hash Based**: Creates a hash of source/destination IP Address/URL and allocate a server based on that hash.

Note: Most of these algorithms can be weighted too(Priority Based)

# `API-Gateway`

- An API-Gateway is a single point of entry in our system in microservice architecture.
- API Gateway can also perform various tasks like rate-limiting , authentication & authorization, logging requests/responses, etc
- A popular exammple of this is Netflix Zuul which acts as both an API-Gateway and Server-Side Load Balancer.


# `Message Queue Driven Architecture vs Event Driven Architecture`

## Message Queue Driven Architecture

- In a message-queue model, the publisher pushes messages to a queue where 1 or more subscriber can listen to a particular queue.
- In this each message is processed by only one consumer which then deletes it from the queue.
- Due to this it is also called pull model.
- It can be implemented by using RabbitMQ and also availible on AWS as Amazon SNS.

## Event Driven Architecture using Pub/Sub

- An event-driven system consist of typically consists of event emitters (or agents), event consumers (or sinks), and event channels.
- In Publiser/Subscriber model,which is an event-driven architecture,a publisher publishes a message to a topic and all the subscribers of that topic receives 1 copy of that message.
- It can be implemented by using RabbitMQ and also availible on AWS as Amazon SQS.


![](https://firebasestorage.googleapis.com/v0/b/boom-b9a18.appspot.com/o/xyz.png?alt=media&token=828bc2e9-96c7-4ce9-9638-863e02bf0246)

# `Streaming`

- In streaming, a single message is not enough to give the complete picture as we need to analyse whole stream of messages to understant the acual message.
-  We maintain a distributed log file and consumers move backward and forward within that file to re-process messages they've already received on command.
- In simple words, streaming is the passing of event log as they occur.
- For eg- When you visit amazon,all your clicks are put into stream to generate user data as a 1-2 clicks are usually not enough to get user preferences.
- It can be implemented using kafka and is also available on AWS as Amazon Kinesis.


Note: In messaging, we generally can't reprocess a message as it is deleted but we can do that using backup(Eg-batch layer) puting it back into queue from the backup. 

# `Web Sockets`

- WebSocket is a communications protocol for a persistent, bi-directional, full duplex TCP connection from a user's web browser to a server.
- A WebSocket connection is initiated by sending a WebSocket handshake request from a browser's HTTP connection to a server to upgrade the connection.

![](https://assets.website-files.com/5ff66329429d880392f6cba2/617a911c0c264f7bfbe7be5f_websocket%20work.png)

# `Caching`

- Caching is the process of storing and retriving data in/form the cache.
- Cache is a temporary storage area relatively small in size with faster access time.
- Caching improves latency and can reduce the load on your servers and databases.

## Caching at Different levels

- **Client Side Caching**: Caches are located on the client side like OS or Browser.
  - It also covers the caches that aew located on the servers which are acting as a client for someone like Reverse-Proxy.
- **CDN Caching**: CDN's like cloudfare caches static files like HTML, CSS, JavaScript, image, video, etc 
- **Web Server Caching**: Web servers can also cache requests, returning responses without having to contact application servers.
- **Database Caching**: Databses by default also have some ibuilt caching mechanisms.
- **Application Level Caching**: *Explained Below*


# `Application Level Caching`
- In application caching, the cache is placed between application and data stores.
- These caches are basically in-memory key-value stores like Memcached and Redis.
- As the data is held in RAM, it is much faster than databases where data is stored on disk.

**Application Caching is the most important and will be reffered as caching in the text from here**

## Ways of Caching
 
- **Caching Database Querry**: Hashed version of query is used as the cache key. A big limitation of this is *cache invalidation* as when one piece of data changes (for example, a table row) we need to invalidate all cached queries which include that row. 
- **Caching Object**: In objects caching pattern, you store the data as an object as you do in your application code.Your class can assemble a dataset from your database and then you can store the instance of the class or the assembled dataset in the cache

## Caching Stratagies(Redis)

- **Cache-Aside(Lazy Loading)**: It is a reactive approach.User first checks cache for data and returns it if found else it will get the data from the database and update it in cache.
- **Write-Through**: It is a proactive approach to maintain data-integrity.When user updates the data in database and it also updates the data in cache as a proactive measure.It is often used with lazy-loading for read. 
- Using cache aside without without write-through may lead to short term inconsistency, however with it there are chances of cache being filled with infrequently-accessed data so it is also recommended to add expiration time while write through.


## Cache Eviction Policies

- LRU
- LFU
- Least Time To Live (LTTL)
- MRU
- MFU

Must Read:
- [Why is in-memory cache not used directly in application?](https://stackoverflow.com/questions/19477821/redis-cache-vs-using-memory-directly#:~:text=Redis%20can%20be%20accessed%20by,done%20in%20a%20separate%20process.)
- [System Design of Cache](https://java-design-patterns.com/patterns/caching/#explanation)

# `Hashing Vs Encryption`

## Hashing

- A process of feeding a message of any length into a hash function to produce a fixed size string.
- Hashing is a *one-way* function as it is impossible to decrypt a hash to get origional password.
- Same input will always create a same output with same hashing algorithm.
- A little change in input will create vastly different output so that guessing is not possible.
<!-- - In system design, databases needs to be divided into multiple partitions and shards and hashing is useful in this. -->

## Encryption
- Encryption is process of encoding plaintext to cipher text which can only be decoded by authorized personnel.
- Unlike hashing,encryption is a *two-way* process as it is possible to get back the plaintext with the key.
- Encryption is of two types:
 - **Symmetric Encryption**: Symmetric encryption uses the same key for both encryption and decryption.This type of encryption is faster, but less secure.
 - **Asymmetric Encryption**: Asymmetric encryption uses two different keys, a private and public key, for encryption and decryption respectively.Public key is available to anyone and is uses to determine authenticity of data.


# `Consistent Hashing`

- Consistent Hashing is used in distributed systems to keep hash-table independent on number of servers.
- This allows servers and objects to scale by minimize key relocation.

### Implementation

- In simple hashing techniques we assign servers to objects by hashing the object parameter and distrubuting it among servers via modulous function but this technique will fail as we add a new server or a server fails.
- In consistent hashing, we hash both objects and servers and map them on a hash-ring.We move clockwise and assign all objects to the next comming server.If a system fails,all objects will be assigned to the next available server.
- One disadvantage of consistent hashing is that the servers and objects are distributed unevenly and there might be chances that many objects are assigned to one server leading to server failiure in a row and downfall of system.
- Therefore an optimization on consistent hashing is making each server appear at mutiple locations on ring using multiple virtual nodes of a server on hash-ring  insted a of a single real server.
- We use multiple hash functions on a single node to assign multiple different positions to it on hash-ring.
- As the virtual nodes increases,the distribution of object becomesamore balanced as when a single server fails naturally failing all its virtual nodes,the dependent objects are distributed among multiple servers not a single as previously it was.
- Consistent hahing used commonly in distributed systems ranging from partitioning in Amazon DynamoDB to Load balancers. 
- [Source Article](https://betterprogramming.pub/load-balancers-and-consistent-hashing-in-6-minutes-b5fc460aea4e)

# `Sharding vs Partitioning`

- Sharding is a technique to horizonally scale database by splitting data from a database into multiple database instances(Nodes) either in horizonal or vertical manner.
- Partitioning is a technique to divide data from a database vertically into different tables inside a single machine.

## Sharding Techniques

- **Key-Based/Hash-Based**: A key is passed into a hash-function and is mapped to a database instance.A more optimized way is consistent hashing.
- **Horizontal/Range-based** : Data is split based on the ranges of a particular attribute of an entity.However it might lead to uneven distribution.
- **Vertical**: We split the data column wise to different machine making it somewhat difficult when we want to process inter-related data.
- **Directory-Based Sharding**: We maintain a lookup service or lookup table for the original database and lookup to each entity using shard key from lookup table.

*PS*: Directory-based sharding is much more flexible than range based and key-based sharding because we are free to use any algorithm to assign to data entries to shards and it is easy to add shards dynamically in this approach.

# `CAP Theorem`

![](https://miro.medium.com/v2/resize:fit:640/format:webp/1*rxTP-_STj-QRDt1X9fdVlA.png)

- **Consistency**: Every read receives the most recent writes or an error.
- **Availabilty**: In an available system, if our client sends a request to a server and the server has not crashed, then the server must respond to the client.
- **Partition Tolerance**: The system continues to operate even if messages are being dropped(or delayed) by the network between nodes.
\
- CAP Theorem states it is impossible for a distributed system to simultaneously gurantee more than two out of the above three.
- In practical scenario,if we have distributed systems, partition tolerance must be high leaving one choose between availability and consistency.
- [Source Article](https://medium.com/system-design-blog/cap-theorem-1455ce5fc0a0) 

*Brain Strom*: If two EC2-instances are running inter-dependent tasks in different regions, are they highly available or fault tolerant?

# `Disaster Recovery`

- **RPO**:Recovery Point Objective sates the maximum time interval, transactions of which are allowed to be lost during a disaster.
  - RPO can also be stated as maximum frequency of backup. 
  - RPO can be reduced by increasing the frequency of the backups.
  - To achieve almost zero RPO, we need to have real-time data replication to a different region.
/
- **RTO**: It is defined as the maximum amount of time application is down after a disaster.

# `ACID VS BASE`
- [**Acid-vs-Base-Short-Detaled-Article-GFG**](https://www.geeksforgeeks.org/acid-model-vs-base-model-for-database/)

# `JSON Web Tokens`

- JSON Web Tokens is an industry-standard method for representing claims securely between two parties.
- It is widely used in stateless authentication and authorization.
- JWT consists of 3 Parts which  are **Header**,**Payload** and **Signature** joined with a (.) which are encoded in BASE64(Easily decoded without keys unlike hashes).
- **Header**: It consists of type of token and the algorithm used to sign the token(HMAC256/SHA256).
- **Payload**: It consists of token data and expiration time.
- **Signature**: BASE64-Encoded Header & Payload joined with (.) are hashed with a secret key.When server wants to verify JWT, it again hashes Header and Payload to generate a signature and matches it with signaure attached.

Note:JWT is only BASE64-Encoded so it is very easy to decode it but you can't generate a signature(which is verified) from Header and Payload without Secret Key making it highly secure.

![](https://firebasestorage.googleapis.com/v0/b/boom-b9a18.appspot.com/o/jwt.png?alt=media&token=97c23079-8c8a-474b-a0f6-a91e4d1e0c7a)

# `O-Auth 2.0`

- O-Auth2.0 is a mechanism that allow third parties to gain limited access of a resources on a service on behalf of resource owner.

### Workflow

- Client authorization request to authorization server and receives authorization grant if genuine user.
- As authorization server has indentified the resource owner,client sends a request with authorization grant to authorization server to issue access token with limited access.
- Access token is returned by authorization server to client.
- Client sends this access token to its Web Server.*(Missing in image)*
- Web server sends this access token with each request to resource server and successfully accesses resources posing as resource owner. 

![](https://i.ytimg.com/vi/CPbvxxslDTU/maxresdefault.jpg)
