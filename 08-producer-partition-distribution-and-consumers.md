# Producer Message Distribution + Consumer Reading – Simple Explanation

## 1. Producer Side: How 100 messages get distributed to 6 partitions?

Kafka has two main strategies:

### A. When you **don't provide a key** (most common for even distribution)
- Kafka uses **round-robin** distribution.
- Messages are sent one by one to partitions in order.

**Example with 100 messages & 6 partitions:**
- Message 1  → Partition 0
- Message 2  → Partition 1
- Message 3  → Partition 2
- ...
- It cycles through all 6 partitions evenly.

Result: Roughly **16–17 messages per partition** (100 ÷ 6).

### B. When you **provide a key** (e.g. userId, orderId)
- Kafka uses a **hash function** on the key.
- All messages with the **same key** always go to the **same partition**.
- This guarantees ordering for that specific key.

Example:
- All messages with key=`user123` → always Partition 3
- Different users → distributed across partitions

## 2. Consumer Side: How do consumers know which partition to read from?

### Consumer Groups

- Consumers usually work in a **Consumer Group**.
- Kafka automatically assigns partitions to consumers in the group.

**Key Rule**: Each partition is consumed by **only one consumer** in the group at a time.

### Example

- Topic has 6 partitions.
- You run 3 consumers in the same group:
  - Consumer A → gets 2 partitions
  - Consumer B → gets 2 partitions
  - Consumer C → gets 2 partitions

- If you run 6 consumers → each gets 1 partition.
- If you run 7 consumers → one will be idle (only 6 partitions available).

### How Assignment Happens

1. Consumer joins the group.
2. One consumer becomes the **Group Coordinator**.
3. Kafka does **partition assignment** (Range or RoundRobin strategy).
4. Each consumer knows exactly which partitions it is responsible for.
5. Consumers regularly send **heartbeats** to stay in the group.

## Summary

- **Producer**: Decides partition using key (hash) or round-robin.
- **Consumer**: Doesn't choose — Kafka **assigns** partitions via Consumer Group.

This design gives both high throughput and correct ordering where needed.

---

Continuing the Kafka learning series.
