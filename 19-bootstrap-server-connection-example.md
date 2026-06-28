# Bootstrap Server Connection - What Actually Happens

## Python Example to See Metadata Response

```python
from confluent_kafka.admin import AdminClient

def inspect_cluster():
    conf = {'bootstrap.servers': 'localhost:9092'}
    admin = AdminClient(conf)
    
    # Get cluster metadata
    cluster_metadata = admin.list_topics(timeout=10)
    
    print('=== Cluster Metadata from Bootstrap Server ===')
    print(f'Cluster ID: {cluster_metadata.cluster_id}')
    print(f'Controller ID: {cluster_metadata.controller_id}')
    
    print('\n=== Brokers ===')
    for broker_id, broker in cluster_metadata.brokers.items():
        print(f'Broker {broker_id}: {broker.host}:{broker.port}')
    
    print('\n=== Topics ===')
    for topic_name, topic in cluster_metadata.topics.items():
        print(f'Topic: {topic_name} | Partitions: {len(topic.partitions)}')
        for part_id, partition in topic.partitions.items():
            print(f'  Partition {part_id} → Leader: {partition.leader}')

if __name__ == "__main__":
    inspect_cluster()
```

## Sample Real Response (Example)

```json
{
  "cluster_id": "some-unique-id",
  "controller_id": 1,
  "brokers": {
    "1": {"host": "kafka1", "port": 9092},
    "2": {"host": "kafka2", "port": 9092}
  },
  "topics": {
    "orders": {
      "partitions": {
        "0": {"leader": 1, "replicas": [1,2,3]},
        "1": {"leader": 2, "replicas": [2,3,1]}
      }
    }
  }
}
```

## Do Producer and Consumer connect differently?

**No** — Both connect to bootstrap servers in the **same way** initially.

The only difference is:
- Producer asks for metadata to know where to **send** messages.
- Consumer asks for metadata to know where to **read** messages from.

Both use the same bootstrap mechanism.

---

Bootstrap connection explained with code.
