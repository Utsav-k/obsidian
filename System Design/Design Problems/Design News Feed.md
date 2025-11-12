
## Important Part - The feed generation

### Core Problem

How to efficiently show users a sorted feed of posts from people/entities they follow.

---

### 1. Two Fundamental Approaches

#### **PUSH Model (Fan-out-on-Write)**

- **What:** When user posts, immediately push the post to ALL followers' feeds
    
- **Workflow:**
    - User A creates post → System gets A's followers → Inserts post into each follower's pre-computed feed
- **Storage:** Pre-computed feeds stored in cache (Redis sorted sets)
- **Pros:**
    - ✅ **Fast reads** - feed is ready instantly
    - ✅ Simple retrieval
- **Cons:**
    - ❌ **Slow writes** - expensive for users with many followers
    - ❌ High storage cost
#### **PULL Model (Fan-out-on-Read)**

- **What:** When user requests feed, fetch posts from all people they follow
- **Workflow:**
    - User opens app → Get their follow list → Fetch latest posts from each → Merge & sort
- **Pros:**
    - ✅ **Fast writes** - just store post once
- **Cons:**
    - ❌ **Very slow reads** - too many queries for users following many people
    - ❌ Doesn't scale
---

### 2. Hybrid Approach (Industry Standard)

#### **Solution: Use Both**

- **Regular users:** PUSH model (pre-compute their feeds)
- **Celebrities/influencers:** PULL model (fetch their posts on-demand)
#### **How it works:**

1. **For feed generation:**
    - Get pre-computed feed from cache (for normal friends)
    - Get recent posts from celebrities (fetched live)  
        = Merge both streams & return
---

### 3. Key Components & Data Flow

#### **Write Path (When posting):**
text
User Posts → Store Post → Queue Message → [Worker] → Get Followers → Update Followers' Feeds (Cache)
#### **Read Path (When loading feed):**
text
User Requests Feed → Get Pre-computed IDs from Cache → Hydrate with Post Details → Return Feed
#### **Critical Services:**
- **Post Service:** Manages posts
- **Social Graph:** Manages follow relationships
- **Feed Service:** Serves the final feed
- **Cache (Redis):** Stores pre-computed feeds as sorted sets

---

### 4. Important Optimizations

- **Celebrity Problem:** Skip PUSH for users with >10K followers
- **Ranking:** Use ML model instead of simple chronology
- **Caching:** Redis sorted sets with TTL
- **Async Processing:** Use message queues to decouple services

---

### 5. **Key Takeaway**

**Use PUSH for most users, PULL for celebrities, and a HYBRID approach to get the best of both worlds - fast reads without making writes too slow.**
