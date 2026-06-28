# Kafka ACLs (Access Control Lists) – Simple Explanation

## What are ACLs?

**ACLs** in Kafka are like **security permissions** or **locks** on doors. They control **who can do what** in your Kafka cluster.

Without ACLs, anyone who can connect to Kafka can read or write to any topic — which is dangerous in production.

## Simple Real-World Analogy

Think of Kafka topics as rooms in a big office building:

- **Producer** = Someone who wants to put documents in a room.
- **Consumer** = Someone who wants to read documents from a room.
- **ACL** = A security guard with a list saying "Only Team A can enter Room Finance, Team B can only read from Room Logs."

## How ACLs Work

Kafka ACLs are based on four main things:

1. **Principal** → Who? (User or service account)
2. **Permission** → What action? (Read, Write, Describe, etc.)
3. **Resource** → On what? (Topic, Consumer Group, Cluster, etc.)
4. **Host** → From where? (IP or hostname — often `*` for anywhere)

### Common Permissions (Operations)

- **READ** — Consume messages
- **WRITE** — Produce messages
- **DESCRIBE** — View information about topic
- **CREATE** — Create new topics
- **DELETE** — Delete topics
- **ALTER** — Change configuration
- **ALL** — Full access

### Example ACL Rules

- Allow user `payment-service` to **write** to topic `payments`
- Allow user `analytics-app` to **read** from topic `user-events`
- Deny everyone else

## How to Manage ACLs

Kafka has a tool called `kafka-acls.sh` (or you can use AdminClient in code).

**Example Command** (Add ACL):
```bash
kafka-acls.sh --bootstrap-server localhost:9092 \
  --add \
  --allow-principal User:payment-service \
  --operation Write \
  --topic payments
```

## Important Concepts

- **Authorizer**: Kafka component that checks ACLs (SimpleAclAuthorizer by default).
- **Super Users**: Special users who have full access regardless of ACLs (usually admins).
- **Default Behavior**: If no ACLs are set and `allow.everyone.if.no.acl.found=true`, everyone has access (common in dev).

## Best Practices

- Turn off `allow.everyone.if.no.acl.found` in production.
- Use **service accounts** (one per application) instead of shared users.
- Combine with **SSL/SASL** authentication (ACLs work on top of authentication).
- Start with restrictive rules and add permissions as needed.

---

This is part of the Kafka learning notes series.
