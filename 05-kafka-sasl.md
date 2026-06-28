# What is SASL in Kafka? – Simple Explanation

## SASL = Simple Authentication and Security Layer

**SASL** is a framework that Kafka uses for **authentication** — i.e., verifying **who you are** before allowing access to the cluster.

It answers the question: "Are you really who you claim to be?"

## Why Do We Need SASL?

- Without authentication, anyone who can reach the Kafka port can connect.
- SASL + ACLs together give proper security (Authentication + Authorization).

## Simple Analogy

- **SASL** = Showing your **ID card** at the building entrance.
- **ACLs** = After showing ID, the guard checks the permission list to see which rooms you can enter.

You need both for good security.

## Common SASL Mechanisms in Kafka

1. **SASL/PLAIN** (Simplest)
   - Username + Password sent in plain text (usually over SSL)
   - Good for quick setups

2. **SASL/SCRAM** (Recommended)
   - Username + Password, but more secure (salted, challenge-response)
   - SCRAM-SHA-256 or SCRAM-SHA-512

3. **SASL/GSSAPI** (Kerberos)
   - Enterprise-grade, uses tickets
   - Common in big companies with Active Directory

4. **SASL/OAUTHBEARER**
   - Modern, token-based (e.g., with OAuth2)

## How SASL Works in Kafka

1. Client wants to connect.
2. Client and broker negotiate a SASL mechanism.
3. Client proves its identity (username/password, ticket, token, etc.).
4. If successful → Broker authenticates the user.
5. Then ACLs are checked for authorization.

## Typical Production Setup

- Use **SASL_SSL** or **SSL + SASL_SCRAM**
- Store credentials securely (not hardcoded)
- Configure in `server.properties` and client properties

**Example Client Config**:
```properties
security.protocol=SASL_SSL
sasl.mechanism=SCRAM-SHA-256
sasl.jaas.config=org.apache.kafka.common.security.scram.ScramLoginModule required \
  username="user" password="password";
```

## Key Takeaway

**SASL** = Authentication (Who are you?)  
**ACLs** = Authorization (What are you allowed to do?)

They work together to secure Kafka.

---

Part of the Kafka security series.
