## Redis & Memcached

### How Redis Works: The Core Idea

At its heart, **Redis (Remote Dictionary Server)** is an in-memory data structure store. Think of it as an extremely fast, non-relational database that keeps all its data in your server's RAM (Random Access Memory) instead of on a hard disk (SSD/HDD).

This fundamental choice—storing data in memory—is the source of its incredible speed, allowing for read and write operations in microseconds.

Here's a simplified view of its architecture:

1. **In-Memory First:** All data operations (GET, SET, etc.) happen directly in RAM. This is why it's so fast—accessing RAM is thousands of times faster than accessing even the fastest SSD.
2. **Single-Threaded:** Redis uses a single thread to handle commands. This avoids the complexity and overhead of locks and context switching, making the core engine very efficient and predictable. The bottleneck becomes network I/O, not CPU.
3. **Persistence (Optional):** Since RAM is volatile (data is lost on a restart), Redis offers ways to persist data to disk:
    - **RDB (Redis Database File):** Takes periodic snapshots of the entire dataset. It's compact and good for backups/disaster recovery.
    - **AOF (Append-Only File):** Logs every write command. It's more durable (you can lose less data in a crash) but the log files can become large.
    - **You can use both.** This is the common setup for durability.
4. **Replication:** Redis supports a simple master-replica architecture. You can have multiple replica nodes that hold a copy of the master's data. This provides read scalability and high availability.
### Key Features of Redis
Some of the most fundamental data structures supported by Redis:
- Strings
- Hashes (objects/dictionaries)
- Lists
- Sets
- Sorted Sets (Priority Queues)
- Bloom Filters (probabilistic set membership; allows false positives)
- Geospatial Indexes
- Time Series
In addition to simple data structures, Redis also supports different communication patterns like Pub/Sub and Streams, partially standing in for more complex setups like Apache Kafka or AWS SNS (Simple Notification Service) / SQS (Simple Queue Service).
### Where to Use Redis in System Design

Redis is a versatile tool, but it's crucial to use it for the right job.

1. **Caching (Most Common Use Case)**
    - **Purpose:** Speed up read-heavy applications by storing frequently accessed data (e.g., database query results, rendered HTML fragments, session data) in memory.
    - **Pattern:** The application first checks Redis for the data. On a cache miss, it fetches from the primary (slower) database, stores the result in Redis, and then returns it.
    - **Example:** Caching product details on an e-commerce site.
2. **Session Store**
    - **Purpose:** Store user session information (like login state, shopping cart items) in a centralized, fast store. This is essential for stateless backend services and horizontal scaling.
    - **Why it's good:** Fast access improves user experience. If a user's next request goes to a different application server, it can still retrieve the session from Redis.
3. **Leaderboards & Counting**
    - **Purpose:** Use the **Sorted Set** data structure to effortlessly maintain real-time leaderboards.
    - **Example:** A game where you can update a player's score with `ZADD` and get the top 10 players with `ZRANGE`. Also great for real-time vote counting.
4. **Rate Limiting**
    - **Purpose:** Prevent abuse by limiting how many requests a user can make in a given time window (e.g., 1000 requests per hour per API key).
    - **How:** Use Redis counters with expiration times to track the number of requests efficiently and atomically.
5. **Message Broker & Pub/Sub**
    - **Purpose:** Enable communication between different parts of a distributed system.
    - **Example:** A service can `PUBLISH` a message to a channel (e.g., "order_created"), and other services `SUBSCRIBE` to that channel to react to the event (e.g., update inventory, send email).
        
6. **Real-Time Features**
    - **Purpose:** Power features that require low-latency updates.
    - **Example:** Live chat, real-time comment streams on a live broadcast, or collaborative editing. Redis Pub/Sub or its streaming capabilities are often used here.
### Main Limitations

1. **Data Must Fit in Memory:** This is the biggest constraint. The cost of RAM is higher than disk, so storing massive datasets (e.g., hundreds of GBs) can become very expensive. (Redis Enterprise offers a tiered storage option to mitigate this).
2. **Persistence Overhead:** While persistence is optional, enabling it (especially AOF) can create a performance trade-off. Writing to disk is slower than writing to RAM, so configuring persistence requires careful consideration.
3. **Single-Threaded Nature:** While a strength for simplicity, it can be a limitation for very heavy operations. A single, slow command (like `KEYS *`) can block all other requests.
4. **Not a Full-Featured Database:** Lacks rich querying (no SQL), relationships, or complex transactions. It's not suitable for the primary source of truth for complex, relational data.
5. **Limited Support for Data Modeling:** While data structures are rich, complex data relationships are hard to model compared to a document or relational database.

### Memcached
Key Characteristics:
- Simple key-value store - strings only
- No persistence - pure volatile cache
- Client-side sharding - distribution handled by clients
- No replication - nodes are independent
- Multi-threaded - can utilize multiple CPU cores

**Choose Memcached if:**
- ✅ Do you only need simple key-value storage?
- ✅ Is it OK to lose all cache data on restart?
- ✅ Are you caching many small, independent items?
- ✅ Do you need maximum simplicity?

**Choose Redis if:**
- ✅ Do you need complex data structures?
- ✅ Should cache data survive restarts?
- ✅ Do you need replication and high availability?
- ✅ Are you building real-time features?

## Elasticsearch & OpenSearch


