# Python Kafka Consumer Group Examples

## 1. Basic Consumer Group Example (Using `confluent-kafka`)

**Recommended library**: `confluent-kafka` (fast and reliable)

```python
# consumer.py
from confluent_kafka import Consumer, KafkaError
import time

def create_consumer(group_id):
    conf = {
        'bootstrap.servers': 'localhost:9092',
        'group.id': group_id,                    # Same group.id = same group
        'auto.offset.reset': 'earliest',         # or 'latest'
        'enable.auto.commit': True,
    }
    
    consumer = Consumer(conf)
    consumer.subscribe(['orders'])               # Topic name
    
    print(f'Consumer in group {group_id} started...')
    
    try:
        while True:
            msg = consumer.poll(1.0)  # Wait 1 second
            if msg is None:
                continue
            if msg.error():
                if msg.error().code() == KafkaError._PARTITION_EOF:
                    continue
                print(f'Error: {msg.error()}')
                continue
            
            print(f'Group {group_id} | Partition: {msg.partition()} | Offset: {msg.offset()} | Value: {msg.value().decode("utf-8")}')
    
    except KeyboardInterrupt:
        pass
    finally:
        consumer.close()

if __name__ == "__main__":
    create_consumer('order-processors')   # Change group.id for different groups
```

## 2. Multiple Consumers in Same Group (Scale Out)

Run multiple terminals with the **same** `group.id`:

```bash
# Terminal 1
python consumer.py

# Terminal 2
python consumer.py

# Terminal 3
python consumer.py
```

They will automatically share the partitions.

## 3. Multiple Independent Consumer Groups (Different Teams)

```python
# analytics_consumer.py
create_consumer('analytics-group')

# fraud_consumer.py  
create_consumer('fraud-detection-group')

# notification_consumer.py
create_consumer('notification-group')
```

All of them can read from the **same topic** (`orders`) independently.

## Key Points

- Same `group.id` → Consumers share load (one team)
- Different `group.id` → Each group reads all messages independently
- `auto.offset.reset`: 'earliest' (from beginning) or 'latest' (new messages only)

---

You can install with: `pip install confluent-kafka`

This is part of the practical examples in the Kafka learning repo.
