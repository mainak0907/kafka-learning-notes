# Kafka Producer – Complete Explanation with Python

## What is a Kafka Producer?

A **Producer** is a client application that **sends (publishes)** messages to a Kafka topic.

## Simple Python Producer Example

```python
from confluent_kafka import Producer
import json
import time

def delivery_report(err, msg):
    if err is not None:
        print(f'Message delivery failed: {err}')
    else:
        print(f'Message delivered to {msg.topic()} [{msg.partition()}] at offset {msg.offset()}')

def create_producer():
    conf = {
        'bootstrap.servers': 'localhost:9092',
        'client.id': 'my-producer',
        'acks': 'all',                    # Wait for all replicas
        'retries': 3,
    }
    
    return Producer(conf)

producer = create_producer()

topic = 'orders'

for i in range(10):
    order = {
        'order_id': f'ORD-{i}',
        'user_id': 'user123',
        'amount': 99.99,
        'timestamp': time.time()
    }
    
    # Send message (async)
    producer.produce(
        topic=topic,
        key=order['user_id'],           # Important for partitioning
        value=json.dumps(order).encode('utf-8'),
        callback=delivery_report
    )
    
    producer.poll(0)  # Trigger delivery reports
    time.sleep(0.1)

producer.flush()  # Wait for all messages to be sent
print('All messages sent!')
```

## Reading from Database and Pushing to Kafka

```python
import psycopg2
from confluent_kafka import Producer
import json

def get_db_connection():
    return psycopg2.connect("dbname=mydb user=postgres")

def produce_from_db():
    producer = Producer({'bootstrap.servers': 'localhost:9092'})
    conn = get_db_connection()
    cur = conn.cursor()
    
    cur.execute("SELECT * FROM orders WHERE processed = FALSE LIMIT 1000")
    rows = cur.fetchall()
    
    for row in rows:
        message = {
            'order_id': row[0],
            'user_id': row[1],
            'amount': row[2]
        }
        
        producer.produce(
            topic='orders',
            key=str(row[1]),
            value=json.dumps(message).encode('utf-8')
        )
        
        # Mark as processed in DB (optional)
        cur.execute("UPDATE orders SET processed=TRUE WHERE id=%s", (row[0],))
    
    conn.commit()
    producer.flush()

produce_from_db()
```

## Where is the Message Actually Stored?

1. Producer sends message to **Leader Broker** of the partition.
2. Leader writes it to disk (append-only log).
3. Leader replicates it to **Follower Brokers**.
4. Once enough replicas acknowledge → message is considered committed.

## Data Replication

- Yes, replication happens automatically.
- Configured using **`replication.factor`** (usually 3).
- Each partition has 1 leader + (replication.factor - 1) followers.
- This provides **fault tolerance** — if leader fails, a follower becomes leader.

**acks='all'** means wait for all replicas before confirming delivery.

---

Practical Producer guide added.
