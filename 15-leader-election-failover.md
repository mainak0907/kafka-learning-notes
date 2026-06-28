# Leader Election & Failover in Kafka – How Followers Take Over

## Question: How does a follower know it needs to become the new leader?

Kafka uses a mechanism called **Leader Election**.

## Simple Explanation

When a leader broker fails or becomes unavailable:

1. **Detection**:
   - Followers continuously send **fetch requests** to the leader.
   - If the leader doesn't respond within a timeout (controlled by `replica.lag.time.max.ms`), it is considered dead.

2. **Controller Broker**:
   - One special broker acts as the **Cluster Controller**.
   - The controller detects the failure and starts the leader election process.

3. **Election Process**:
   - Among the in-sync replicas (ISR - In Sync Replicas), Kafka chooses a new leader.
   - Usually, the first follower in the ISR list becomes the new leader.

4. **Notification**:
   - The controller updates the metadata.
   - All brokers and clients are informed about the new leader for that partition.

## Key Concepts

- **ISR (In-Sync Replicas)**: Set of replicas that are fully caught up with the leader.
- Only replicas in ISR are eligible to become leader.
- This ensures no data loss.

## KRaft Mode (Modern Kafka)

- In newer Kafka versions (3.0+), Kafka uses **KRaft** (Kafka Raft) instead of ZooKeeper.
- Leader election becomes faster and more reliable.

## Summary

Followers don't decide themselves. The **Cluster Controller** detects failure and triggers a new leader election from the healthy in-sync replicas.

This whole process is usually very fast (few seconds) and mostly automatic.

---

Kafka high availability explained.
