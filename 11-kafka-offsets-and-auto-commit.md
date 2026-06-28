# Kafka Offsets & Auto Commit – Simple Explanation

## What is an Offset?

An **offset** is like a **message number** or **bookmark** inside a partition.

- Every message in a partition gets a unique, increasing number (offset).
- It tells the consumer: "I have read up to this message."

## Why Offsets Matter

Offsets allow consumers to:
- Resume from where they left off
- Avoid reading the same message twice
- Provide **exactly-once** or **at-least-once** processing semantics

## `enable.auto.commit` (Auto Commit)

This setting tells Kafka: **"Automatically save my progress (commit offset) periodically."**

### How it works:
- Default: `True` (enabled)
- Kafka automatically commits the offset every 5 seconds (default interval).

**Advantages**:
- Simple to use
- Less code

**Disadvantages**:
- You might lose some messages or process duplicates during rebalancing or crashes.

## `auto.offset.reset`

This tells the consumer **what to do** when there is no committed offset (first time or offset is lost).

### Options:

- **`earliest`** → Start from the beginning of the topic (read all old messages)
- **`latest`** → Start only from new messages (ignore old ones)
- **`none`** → Throw error if no offset is found

## Manual Commit (Better Control)

```python
# Instead of auto commit
consumer.commit()        # Manual commit after processing
# or
consumer.commit_async()  # Non-blocking
```

**Best Practice in Production**:
- Use manual commit for better control
- Or enable auto commit + idempotent processing

---

This is part of the consumer deep dive.
