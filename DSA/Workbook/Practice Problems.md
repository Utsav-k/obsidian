### HLD

1. Scale
   
   Horizontal, Vertical
1. Consistency
2. CAP Theorem 
   
- **Consistency**: All nodes see the same data at the same time. When a write is made to one node, all subsequent reads from any node will return that updated value.
    
- **Availability**: Every request to a non-failing node receives a response, without the guarantee that it contains the most recent version of the data.
    
- **Partition Tolerance**: The system continues to operate despite arbitrary message loss or failure of part of the system (i.e., network partitions between nodes).
  
  
1. Indexing (Databases)
2. Consistent Hashing (Databases) [DynamoDB]
3. Authentication 
4. Encryption
5. Monitoring

Load Balancing - Use RoundRobin in most cases
Caching - Eviction Policies are important to know
Application Server Cache
CDN
Cache Invalidation
Partitioning (important)
Consistent Hashing, VNodes
Data Replication
Distributed Caching (MemCached, Redis) - **Cache Write Strategy**, Eviction Policy, Invalidation
Data Sharding
Queue (KAFKA, RabbitMQ) - Streaming the data
Databases (NoSQL, SQL, DynamoDB, Blob Storage(s3))  [Search Optimized Database (Tokenization, Inverted Index)]
SNS (Notification)
Streams / Event Sourcing

