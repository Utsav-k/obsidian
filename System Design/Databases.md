## Replication
https://claude.ai/share/2bbb407f-2e31-43ff-9be0-5fae8a4c211f
#### Overview

1. **Database Replication Fundamentals**
    - What is replication and why we need it
    - Replication strategies (synchronous vs asynchronous)
    - Single-leader, multi-leader, and leaderless replication

2. **Consistency Models**
    - Strong consistency
    - Eventual consistency
    - Causal consistency
    - Read-your-writes consistency

3. **Consensus Algorithms Overview**
    - What is distributed consensus
    - The CAP theorem
    - Byzantine vs non-Byzantine systems

4. **Raft Consensus Algorithm**
    - Leader election
    - Log replication
    - Safety guarantees

5. **Paxos Algorithm**
    - Basic Paxos
    - Multi-Paxos
    - Comparison with Raft

6. **ZooKeeper & ZAB Protocol**
    - ZooKeeper architecture
    - Zab (ZooKeeper Atomic Broadcast)
    - Use cases

7. **Quorum-Based Replication**
    - Read and write quorums
    - Sloppy quorums and hinted handoff

8. **Conflict Resolution**
    - Last-write-wins
    - Version vectors
    - CRDTs (Conflict-free Replicated Data Types)


 ### Database Replication Fundamentals
#### What is Replication?

Replication means keeping a copy of the same data on multiple machines connected via a network. This provides:

- **High availability**: System remains operational even if some nodes fail
- **Reduced latency**: Data can be served from geographically closer replicas
- **Increased throughput**: More machines can serve read queries
#### Replication Strategies

**Synchronous Replication:**

- Leader waits for confirmation from followers before acknowledging write
- ✅ Guarantees data is up-to-date on followers
- ❌ Slower writes, system blocks if follower is unavailable
- Used when data consistency is critical

**Asynchronous Replication:**
- Leader sends data to followers but doesn't wait for confirmation
- ✅ Faster writes, system remains available even if followers lag
- ❌ Risk of data loss if leader fails before replication completes
- Most common in practice

**Semi-synchronous:**
- Wait for acknowledgment from at least one follower
- Balances consistency and availability
#### Replication Architectures

**Single-Leader (Primary-Replica):**

```
Client → Leader (accepts writes) → Followers (read-only replicas)
```

- All writes go through one leader
- Followers replicate the leader's write log
- Simple and widely used (PostgreSQL, MySQL, MongoDB)

**Multi-Leader:**

```
Client ↔ Leader1 ←→ Leader2 ←→ Leader3
```

- Multiple nodes accept writes
- Each leader replicates changes to others
- Better for multi-datacenter setups
- Complex conflict resolution needed

**Leaderless (Dynamo-style):**

```
Client → Multiple Nodes (quorum-based)
```

- No designated leader, client writes to multiple nodes
- Uses quorum reads/writes
- Used by Cassandra, DynamoDB, Riak

### Consistency Models

#### Strong Consistency (Linearizability)

- After a write completes, all subsequent reads return the new value
- Appears as if there's only one copy of data
- Example: Reading your own write immediately after posting it
- **Trade-off**: Requires coordination, slower, may sacrifice availability

#### Eventual Consistency

- If no new updates, eventually all replicas converge to same value
- Temporary inconsistencies are acceptable
- Example: DNS, social media like counts
- **Trade-off**: Fast, highly available, but stale reads possible

#### Causal Consistency

- Operations that are causally related are seen in the same order by all nodes
- Example: If comment B replies to comment A, all users see A before B
- Weaker than strong consistency, stronger than eventual

#### Read-Your-Writes Consistency

- Users always see their own updates
- Other users may have eventual consistency
- Common in user profile systems

### Consensus Algorithms Overview

#### What is Distributed Consensus?

Getting multiple nodes to agree on a single value despite failures. Essential for:

- Leader election
- Atomic commit in distributed transactions
- Maintaining consistent state across replicas
 
#### The CAP Theorem

In a distributed system with network partitions, you can only have 2 of 3:

- **Consistency**: All nodes see the same data
- **Availability**: Every request gets a response
- **Partition Tolerance**: System works despite network failures

Most systems choose between CP (consistent but not always available) or AP (available but eventually consistent).

#### Byzantine vs Non-Byzantine

**Non-Byzantine (Crash-Fault Tolerant):**

- Nodes either work correctly or stop (crash)
- Raft, Paxos, Zab assume this
- Simpler algorithms

**Byzantine (Byzantine Fault Tolerant):**

- Nodes may behave arbitrarily or maliciously
- Blockchain consensus (PBFT, Tendermint)
- More complex, requires 3f+1 nodes to tolerate f failures

### Raft Consensus Algorithm

Raft is designed to be understandable. It's used by etcd, Consul, and many distributed systems.

#### Key Concepts

**Server States:**

- **Follower**: Passive, responds to requests
- **Candidate**: Seeks to become leader
- **Leader**: Handles all client requests

**Terms:**

- Time divided into terms (monotonically increasing)
- Each term has at most one leader
- Used to detect stale information

#### Leader Election

1. Follower waits for heartbeat from leader
2. If timeout expires, becomes **Candidate** and starts election:
    - Increments term
    - Votes for itself
    - Requests votes from other nodes
3. Wins if receives majority votes
4. If term is outdated, reverts to follower

```
Timeout → Candidate → Request Votes → Majority → Leader
                ↓ Timeout/Split Vote
             Start New Election
```

**Split Vote Prevention:**

- Randomized election timeouts (150-300ms typically)
- Reduces likelihood of multiple candidates simultaneously

#### Log Replication

1. Client sends command to leader
2. Leader appends to its log
3. Leader sends AppendEntries RPC to followers
4. Once majority acknowledge, leader **commits** entry
5. Leader executes command, returns result
6. Leader notifies followers to commit in next heartbeat

```
[Term|Index|Command]
Leader:   [1,1,x=5] [1,2,y=3] [2,3,z=7]
Follower: [1,1,x=5] [1,2,y=3] [2,3,z=7]
```

#### Safety Properties

**Election Safety**: At most one leader per term

**Leader Append-Only**: Leader never overwrites/deletes its log

**Log Matching**: If two logs contain entry with same index and term, all preceding entries are identical

**Leader Completeness**: If entry is committed in term T, it will be present in leaders of all higher terms

**State Machine Safety**: If server applies log entry at index, no other server applies different entry for that index

