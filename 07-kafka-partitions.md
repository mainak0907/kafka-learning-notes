# Kafka Topics and Partitions – Simple Explanation

## What is a Topic?

A **Topic** is like a **category or folder** for messages. 

Examples:
- `user-signups`
- `payment-events`
- `logs`

All messages related to one business event go into the same topic.

## What are Partitions?

A **Partition** is like a **separate file or log** inside that folder.

- Each topic is split into **one or more partitions**.
- Partitions are the unit of **scalability and parallelism** in Kafka.

## Simple Analogy

Think of a Topic as a **big filing cabinet**:

- The cabinet = Topic
- Each drawer = One Partition

You can have multiple drawers (partitions) so many people can work at the same time without blocking each other.

## Why Partitions Matter

1. **Scalability** — More partitions = more parallelism.
2. **Throughput** — Multiple producers/consumers can work on different partitions simultaneously.
3. **Ordering** — Messages are **ordered only within a single partition** (not across partitions).

## How Partitions Work

- When you produce a message, Kafka decides which partition it goes to (based on key or round-robin).
- If you use a **message key** (e.g., user ID), all messages with the same key go to the **same partition** → guarantees order for that user.
- Each partition has an **offset** (like a message number).
- Partitions can be on different brokers for load distribution.

## Important Points

- You decide the number of partitions when creating a topic (can be increased later, but not easily decreased).
- Number of consumers in a consumer group cannot be more than the number of partitions.
- More partitions = more overhead (files, memory, etc.).

## Best Practices

- Start with 6–12 partitions for most topics.
- Use more partitions for high-volume topics.
- Choose partition count based on expected throughput and number of consumers.

---

Part of the core Kafka concepts series.
