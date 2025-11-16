## Core Fundamentals

### Scalability Concepts

- Vertical vs horizontal scaling
- Load balancing strategies (round-robin, consistent hashing, weighted)
- Auto-scaling patterns and triggers
### Data Storage Fundamentals

- SQL vs NoSQL trade-offs and when to use each
- Database sharding strategies (horizontal, vertical, functional)
- Replication patterns (master-slave, master-master)
- ACID properties and eventual consistency
### Caching Strategies

- Cache-aside, write-through, write-back patterns
- Cache levels (browser, CDN, application, database)
- Cache eviction policies (LRU, LFU, TTL)
- Cache stampede and thundering herd problems
______________________________
## Distributed Systems Core 
### CAP Theorem and Consistency Models

- Strong vs eventual consistency trade-offs
- Partition tolerance implications
- Real-world examples (Amazon DynamoDB, Google Spanner)

### Message Queues and Event Streaming

- Queue vs topic patterns
- At-least-once vs exactly-once delivery
- Event sourcing and CQRS patterns
- Popular systems: Kafka, RabbitMQ, SQS

### Microservices Architecture

- Service decomposition strategies
- API gateway patterns
- Inter-service communication (sync vs async)
- Circuit breaker and bulkhead patterns

## Advanced Patterns 

### Distributed Algorithms

- Consensus algorithms (Raft, Paxos basics)
- Distributed locking mechanisms
- Leader election patterns
- Vector clocks and logical time

### Data Processing Patterns

- Batch vs stream processing trade-offs
- Lambda architecture vs Kappa architecture
- Map-Reduce patterns and modern alternatives

### Observability and Reliability

- Monitoring, logging, and tracing strategies
- SLA/SLO/SLI definitions and measurement
- Chaos engineering principles
- Disaster recovery and backup strategies

## System-Specific Knowledge

### Search Systems

- Inverted indexes and ranking algorithms
- Elasticsearch/Solr architecture patterns
- Real-time vs batch indexing strategies

### Real-time Systems

- WebSocket vs Server-Sent Events
- Push notification architectures
- Real-time analytics pipelines

### Content Delivery

- CDN strategies and edge computing
- Image/video processing and optimization
- Global content distribution patterns

## Practice and Integration

**Mock System Design Problems** Practice these classic problems in order:

1. URL shortener (Bit.ly) - covers basics
2. Chat system (WhatsApp) - real-time communication
3. Social media feed (Twitter) - fan-out patterns
4. Video streaming (YouTube) - content delivery
5. Ride-sharing (Uber) - location-based services
6. Payment system (Stripe) - transactional consistency
7. Search engine (Google) - large-scale indexing

**Key Success Strategies:**

- Always start with requirements gathering and capacity estimation
- Drive the conversation - don't wait for prompts
- Discuss trade-offs explicitly for every major decision
- Consider failure scenarios and recovery strategies
- Practice drawing clean diagrams quickly