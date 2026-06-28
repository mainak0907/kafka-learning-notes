# Kafka Bootstrap Servers

## What are Bootstrap Servers?

**Bootstrap servers** (also called **bootstrap brokers**) are the initial contact points that clients (producers, consumers, admin clients, etc.) use to discover and connect to a Kafka cluster.

They act as the "entry point" to the cluster. You don't need to know about every broker in the cluster upfront — you just provide a list of one or more bootstrap servers, and Kafka will return metadata about the full cluster.

## Why "Bootstrap"?

- The client "bootstraps" (initializes) its connection to the cluster using these servers.
- After the initial connection, the client receives metadata (topic partitions, leader brokers, etc.) and connects directly to the relevant brokers for producing/consuming.

## Configuration

In client configurations, it's usually specified as:

```properties
bootstrap.servers=broker1:9092,broker2:9092,broker3:9092
```

Or in code (Java example):
```java
Properties props = new Properties();
props.put("bootstrap.servers", "localhost:9092, kafka1.example.com:9092");
```

## Best Practices

- Provide **multiple bootstrap servers** (at least 2-3) for high availability. If one is down, the client can try others.
- They don't have to be all brokers — even one or two healthy ones are sufficient.
- In production, use a load balancer or a stable set of broker hostnames.
- DNS resolution should be reliable.

## How It Works (Step by Step)

1. Client connects to one of the bootstrap servers.
2. Client sends a metadata request.
3. Kafka returns information about all topics, partitions, current leaders, and available brokers.
4. Client then connects directly to the leader broker for each partition it needs to interact with.

## Common Pitfalls

- Using only one bootstrap server → single point of failure during client startup.
- Outdated bootstrap list after cluster changes (rebalancing, new brokers).
- Firewall / network issues preventing connection to listed servers.

## Relation to Other Concepts

- **Advertised Listeners**: How brokers advertise themselves to clients (important in Docker/Kubernetes setups).
- **Metadata Refresh**: Clients periodically refresh metadata from brokers.

---

This is part of the ongoing Kafka learning notes repository.
