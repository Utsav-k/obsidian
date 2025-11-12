
## 10 Essential System Design Problems

| #      | Problem                                    | Key Learnings                                                                                                                                    | Why It's Critical                                                                                       |
| ------ | ------------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------- |
| **1**  | **URL Shortener**                          | Hashing algorithms, base62 encoding, DB schema design, handling collisions, read-heavy scaling, caching strategies                               | Perfect starter - teaches fundamentals without overwhelming complexity. Shows DB design + basic scaling |
| **2**  | **News Feed (Twitter/Facebook)**           | Fan-out patterns (fan-out on write vs read), ranking algorithms, caching layers, handling hot users, pagination, timeline consistency            | Core social media pattern used everywhere. Teaches trade-offs between consistency and performance       |
| **3**  | **Rate Limiter**                           | Token bucket/leaky bucket/sliding window algorithms, distributed rate limiting, Redis patterns, handling race conditions across servers          | Teaches distributed systems concepts and algorithm choices. Asked frequently at all levels              |
| **4**  | **Consistent Hashing / Load Balancer**     | Consistent hashing ring, virtual nodes, health checks, load balancing algorithms (round-robin, least connections), service discovery             | Fundamental distributed systems concept. Understanding this unlocks many other designs                  |
| **5**  | **Chat/Messaging System (WhatsApp/Slack)** | WebSockets vs long polling, message queue architecture, message delivery guarantees, read receipts, group chat fan-out, offline message handling | Teaches real-time systems, persistent connections, and reliability patterns                             |
| **6**  | **Video Streaming (YouTube/Netflix)**      | CDN architecture, adaptive bitrate streaming, video encoding/transcoding, blob storage, thumbnail generation, view count consistency             | Teaches handling large files, CDN usage, async job processing, eventual consistency                     |
| **7**  | **Notification Service**                   | Push notifications (FCM/APNs), email/SMS queues, user preferences, retry logic, dead letter queues, rate limiting per user                       | Teaches message queuing, reliability patterns, handling failures gracefully                             |
| **8**  | **Typeahead/Autocomplete**                 | Trie data structure, prefix matching, caching hot queries, personalization, handling typos, freshness vs performance trade-offs                  | Teaches caching strategies, data structure choices at scale, balancing latency and accuracy             |
| **9**  | **Distributed Cache (Redis/Memcached)**    | Cache eviction policies (LRU/LFU), cache invalidation strategies, cache-aside vs write-through, cache stampede problem, sharding cache           | Deep dive into caching - the most important scaling tool. Understanding this improves all other designs |
| **10** | **Web Crawler**                            | BFS/DFS at scale, URL frontier/queue, politeness policy, deduplication (bloom filters), robots.txt handling, distributed crawling                | Teaches queue-based systems, handling massive scale, dealing with constraints, distributed coordination |

## Bonus Problems (If You Have Time)

| Problem | Key Learnings |
|---------|---------------|
| **Design Uber/Lyft** | Geospatial indexing (quadtree/geohash), real-time matching algorithms, location updates, surge pricing |
| **Design Instagram** | Image storage/CDN, follow graph, feed generation, like counter at scale, stories/ephemeral content |
| **Design Dropbox/Google Drive** | File chunking, sync algorithms, conflict resolution, metadata storage, delta sync |
| **Design Ticketmaster** | Handling spikes, preventing double booking, distributed locking, inventory management, queue systems |

## How to Study These

**Week 1-2:** Do problems 1-6 (core fundamentals)
**Week 3:** Do problems 7-10 (advanced patterns)
**Week 4:** Review all 10, can you explain each in 5 minutes?

**For each problem:**
1. **Spend 30-45 min designing yourself first** (don't look at solutions)
2. **Compare with standard solution** - what did you miss?
3. **Draw the architecture diagram** - can you redraw it from memory?
4. **Know the numbers** - capacity estimation, QPS, storage needs
5. **Understand trade-offs** - why this choice over alternatives?

These 10 problems cover 90% of system design interview concepts. Master these and you can tackle almost any design question.