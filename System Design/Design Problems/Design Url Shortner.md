
https://chat.deepseek.com/share/n7hfbslrk923lt8g5s
### Key Learnings

**A. Generating the Short Code (The Heart of the System)**
- **Option 1: Hashing (e.g., MD5, SHA-1)**
    - Hash the long URL and take the first 7 characters.
    - **Problem:** **Hash Collisions.** Two different long URLs can produce the same short code.
    - **Solution:** On collision, append a sequence number and retry. This can be inefficient.
- **Option 2: Base62 Encoding**
    - **Idea:** Generate a unique number (e.g., from a database auto-incrementing ID) and convert it to a base62 string `[a-zA-Z0-9]`.
    - **Pros:** Simple, predictable, no collisions.
    - **Cons:**
        1. Exposes a predictable sequence (easy to guess other URLs).
        2. The single database (with a single auto-increment key) becomes a **bottleneck** for writes.
- **Option 3: Pre-Generated Keys (Offline Key Generation Service - KGS)**
    - **Idea:** A separate service pre-generates a massive pool of random, unique 6-8 character strings and stores them in a database (`keys_db`).
    - When an Application Server needs a new short code, it requests one from the KGS.
    - **Pros:** Extremely fast, no collisions, non-sequential.
    - **Cons:** Adds operational complexity; need to ensure the KGS is highly available and the key pool never runs out.
- **Recommended Solution:** **Use a combination.**
    - Use a **distributed ID generator** (like Twitter's Snowflake) to create a globally unique 64-bit integer.
    - Encode this integer using **Base62** to create the short code.
    - This avoids the single-DB bottleneck and is highly scalable. 
**HTTP Redirection:**
- **301 Moved Permanently:** Browser caches the redirect. Good for server load, as future requests may not hit our servers.
- **302 Found:** Temporary redirect. The browser will always hit our servers first. Good for analytics and tracking clicks.
- **Trade-off:** Discuss both. For most use cases, **302** is better for the business.
**Deletion:**
- **Lazy Deletion:** On a read request, if the link is expired, return a 404 and delete the record. Simple and effective.

__________________________________________________________________
### **URL Shortener System Design Interview**

#### **1. Question for the Candidate**

"We want to design a URL shortening service like TinyURL. Here are the key requirements:

- **Functional Requirements:**
    
    1. Given a long URL, the service should generate a unique, short alias for it.
        
    2. When a user accesses the short URL, they should be redirected to the original long URL.
        
    3. Short links should expire after a default time (e.g., 2 years), but users can optionally set a custom expiration time.
        
- **Non-Functional Requirements:**
    
    1. **High Availability:** The system must be highly reliable. Redirects should not fail.
        
    2. **Low Latency:** The URL redirection should be as fast as possible. This is the most frequent operation.
        
    3. **Scalable:** The system should be able to handle a massive volume of read (redirect) and write (shorten) requests.
        

Let's start by estimating the scale."

---

#### **2. Capacity Estimation (Back-of-the-Envelope)**

This section tests the candidate's ability to reason about scale.

- **Traffic Estimates:**
    
    - **Assumption:** 100 million new URLs shortened per month.
        
    - **Write (Shorten) QPS:** `100M / (30 days * 24 hours * 3600 seconds)`
        
        - `100,000,000 / 2,592,000 ≈ 386` writes/second. Let's round to **400 QPS**.
            
    - **Read (Redirect) QPS:** Assume a 100:1 read-to-write ratio.
        
        - Read QPS = 400 * 100 = **40,000 QPS**.
            
    - **This is a read-heavy system.**
        
- **Storage Estimates:**
    
    - **Assumption:** We store each URL mapping for 5 years on average.
        
    - Total records in 5 years: `100 million/month * 12 months * 5 years = 6 billion records`.
        
    - **Data Size per Record:**
        
        - `short_url` (alias): 7 bytes (e.g., `https://sh.ort/abc123`)
            
        - `long_url`: 500 bytes (average)
            
        - `created_at`: 7 bytes (timestamp)
            
        - `expiration`: 7 bytes
            
        - **Total:** ~521 bytes per record.
            
    - **Total Storage:** `6 billion * 521 bytes ≈ 3.126 TB`. Even with indexes, this is manageable.
        
- **Bandwidth Estimates:**
    
    - **Incoming (for writes):** `400 requests/sec * 521 bytes ≈ 208 KB/s`.
        
    - **Outgoing (for reads):** `40,000 requests/sec * 521 bytes ≈ 20.8 MB/s`.
        

---

#### **3. System APIs**

The candidate should define the service's interface.

- `createURL(api_key, long_url, custom_alias=None, expiration_date=None)`
    
    - **Parameters:**
        
        - `api_key` (string): For authentication and rate limiting.
            
        - `long_url` (string): The original URL to shorten.
            
        - `custom_alias` (optional string): User's custom short link.
            
        - `expiration_date` (optional DateTime): When the short link should expire.
            
    - **Returns:**
        
        - `success`: Short URL if created successfully.
            
        - `error`: Reason for failure (e.g., custom alias already taken, invalid URL).
            
- `deleteURL(api_key, short_url)`
    
    - (Optional, but good to discuss)
        

---

#### **4. Database Design**

The candidate should choose between SQL and NoSQL and justify their choice.

- **Schema:**
    
    - We need a simple table/collection to store the mappings.
        
    - **`url_mappings` Table:**
        
        - `hash` or `short_code` (VARCHAR(10)): **Primary Key**. The unique part of the short URL (e.g., `abc123`).
            
        - `long_url` (TEXT): **Not Null**. The original long URL.
            
        - `created_at` (DATETIME): Default current time.
            
        - `expiration_date` (DATETIME): When this record becomes invalid.
            
        - `user_id` (INT): To track who created it (if we have user accounts).
            
- **SQL vs. NoSQL:**
    
    - **SQL (e.g., MySQL, PostgreSQL):** Good choice. The schema is fixed and relational integrity is beneficial. Easy to query and index.
        
    - **NoSQL (e.g., Cassandra, DynamoDB):** Also a great choice for massive scalability and high write throughput. The access pattern is simple key-value lookups.
        
    - **Conclusion:** For this scale, a distributed **NoSQL** key-value store like DynamoDB or Cassandra is often preferred due to its horizontal scalability and low-latency reads.
        

---

#### **5. High-Level System Architecture**

The candidate should draw the core components.

text

[Client] 
    |
    | (1. POST /shorten with long_url)
    v
[Application Servers] (Stateless, auto-scaling)
    |               |
    | (2. Store mapping)              | (3. Generate short code)
    v               v
[Database]      [Key Generation Service]
    |
    | (4. Client gets short_url)
    |
    v
[Client uses short_url]
    |
    | (5. GET sh.ort/abc123)
    v
[Application Servers]
    |
    | (6. Lookup long_url)
    v
[Database (Cache Miss)] ---> [Cache (e.g., Redis)] <--- (7. Cache the mapping)
    |
    | (8. Return long_url)
    v
[Application Servers] -- (9. HTTP 301 Redirect) --> [Client]

---

#### **6. Deep Dive: Core Algorithms & Components**

This is where the interview gets interesting.

**A. Generating the Short Code (The Heart of the System)**

- **Option 1: Hashing (e.g., MD5, SHA-1)**
    
    - Hash the long URL and take the first 7 characters.
        
    - **Problem:** **Hash Collisions.** Two different long URLs can produce the same short code.
        
    - **Solution:** On collision, append a sequence number and retry. This can be inefficient.
        
- **Option 2: Base62 Encoding**
    
    - **Idea:** Generate a unique number (e.g., from a database auto-incrementing ID) and convert it to a base62 string `[a-zA-Z0-9]`.
        
    - **Pros:** Simple, predictable, no collisions.
        
    - **Cons:**
        
        1. Exposes a predictable sequence (easy to guess other URLs).
            
        2. The single database (with a single auto-increment key) becomes a **bottleneck** for writes.
            
- **Option 3: Pre-Generated Keys (Offline Key Generation Service - KGS)**
    
    - **Idea:** A separate service pre-generates a massive pool of random, unique 6-8 character strings and stores them in a database (`keys_db`).
        
    - When an Application Server needs a new short code, it requests one from the KGS.
        
    - **Pros:** Extremely fast, no collisions, non-sequential.
        
    - **Cons:** Adds operational complexity; need to ensure the KGS is highly available and the key pool never runs out.
        
- **Recommended Solution:** **Use a combination.**
    
    - Use a **distributed ID generator** (like Twitter's Snowflake) to create a globally unique 64-bit integer.
        
    - Encode this integer using **Base62** to create the short code.
        
    - This avoids the single-DB bottleneck and is highly scalable.
        

**B. Low-Latency Redirection**

- **Use a Cache:** A distributed cache like **Redis** or **Memcached** is mandatory.
    
- **Strategy:** Look up the short code in the cache first. On a cache miss, read from the database and populate the cache with a TTL (Time-To-Live).
    
- **HTTP Redirection:**
    
    - **301 Moved Permanently:** Browser caches the redirect. Good for server load, as future requests may not hit our servers.
        
    - **302 Found:** Temporary redirect. The browser will always hit our servers first. Good for analytics and tracking clicks.
        
    - **Trade-off:** Discuss both. For most use cases, **302** is better for the business.
        

---

#### **7. Additional Features & Considerations**

A strong candidate will discuss these without prompting.

- **How to handle custom aliases?**
    
    - Check the database for existence. If available, create the entry. This is a simple read-before-write.
        
- **How to clean up expired links?**
    
    - **Lazy Deletion:** On a read request, if the link is expired, return a 404 and delete the record. Simple and effective.
        
    - **Separate Cleanup Service:** A periodic cron job that scans and deletes expired records. More thorough but resource-intensive.
        
- **Security:**
    
    - **Preventing Abuse:** Rate limiting on the API.
        
    - **Malicious Links:** A service to check and block URLs against known phishing/malware databases.
        
- **Analytics (if required):**
    
    - How many times was a link clicked? Geographic data, etc.
        
    - This can be implemented by logging every redirect event and processing the logs asynchronously (e.g., using a stream processing framework like Apache Kafka and Apache Spark).
        

---

#### **8. Final Summary**

The candidate should be able to summarize the design:

"We designed a scalable URL shortener by first estimating our needs at ~40k redirects/sec. The core components are stateless Application Servers, a distributed NoSQL database for persistence, and a Redis cache for low-latency reads. The key challenge of generating unique short codes is solved using a distributed ID generator and Base62 encoding. We ensure links expire via lazy deletion and protect the system with rate limiting."