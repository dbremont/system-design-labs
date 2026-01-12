# URL Shortener

> A URL shortener is an infrastructure component that maps long, potentially unstable URLs to compact, stable identifiers. Despite its apparent simplicity, it is a non-trivial systems problem involving distributed storage, low-latency lookup, durability, abuse resistance, and long-term identifier stability. This document frames URL shortening as a design space of strategies and evaluation criteria rather than a single canonical architecture.

## Mechanism

> How to generate short urls?

Add an evaluation clumn, and a properties column

| Category      | Mechanism              | Description                                                                          | Constitutive Technique(s)                                        | Properties                                                                           | Evaluation                                                                   |
| ------------- | ---------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------- | ------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------- |
| Deterministic | URL hashing            | Compute a hash of the original URL and derive the short ID from it.                  | Cryptographic hash functions, truncation, Base62/Base64 encoding | Deterministic, stateless, collision-prone under truncation, non-reversible           | Collision probability, hash length vs space, lookup amplification            |
| Deterministic | Canonicalized hashing  | Normalize the URL before hashing to ensure semantic equivalence maps to the same ID. | URL normalization, canonical forms, hashing                      | Deterministic, idempotent under normalization, sensitive to canonicalization quality | Canonicalization correctness, false equivalence risk, operational complexity |
| Sequential    | Central counter        | Increment a global counter to generate unique numeric IDs.                           | Atomic counters, transactional storage                           | Strong ordering, injective, predictable, centralized                                 | Throughput bottleneck, single point of failure, contention                   |
| Sequential    | Distributed counter    | Generate IDs using coordinated counters across nodes.                                | Sharded counters, consensus protocols                            | Globally unique, ordered (weak/strong), coordination-heavy                           | Consensus overhead, latency, fault tolerance                                 |
| Sequential    | Range-based allocation | Pre-allocate ID ranges to nodes for local generation.                                | Range partitioning, local counters                               | Locally monotonic, weak global ordering, reduced coordination                        | Range exhaustion, rebalancing cost, skew                                     |
| Randomized    | Uniform random token   | Generate a random token with sufficient entropy.                                     | Secure RNG, fixed-length token space                             | Stateless, non-predictable, probabilistic uniqueness                                 | Collision probability, entropy size, abuse resistance                        |
| Hybrid        | Time-based ID          | Encode time as part of the identifier to ensure monotonicity.                        | Epoch timestamps, bit packing                                    | Time-ordered, partially predictable, low coordination                                | Clock skew, rollover, ordering guarantees                                    |
| Hybrid        | Time + randomness      | Combine timestamp and random bits to avoid collisions.                               | Epoch clocks, RNG, encoding                                      | Mostly ordered, low collision risk, scalable                                         | Clock correctness, entropy sufficiency                                       |
| Hybrid        | Hash + secret salt     | Hash the URL together with a secret salt to reduce predictability.                   | Salted hashing, truncation                                       | Deterministic (per salt), non-enumerable, collision-prone                            | Salt management, rotation impact, reversibility constraints                  |
| User-driven   | Explicit alias         | Accept a user-provided short ID.                                                     | Validation, uniqueness checks                                    | Human-meaningful, sparse namespace, adversarial input                                | Abuse handling, namespace squatting, moderation cost                         |
| External      | External ID generator  | Obtain an identifier from a dedicated ID service.                                    | UUID/Snowflake generators, RPC                                   | Decoupled generation, globally unique, opaque                                        | Dependency latency, availability, integration complexity                     |

## Technique

- Type of Techniques
- Technique Space

## Evaluation

| Dimension                         | Description                                                                                                 | Metric(s)                                               |
| --------------------------------- | ----------------------------------------------------------------------------------------------------------- | ------------------------------------------------------- |
| Latency                           | Time to resolve a short URL under typical and peak load, including DNS, application, and datastore latency. | p50 / p95 / p99 response time, cache hit latency        |
| Throughput                        | Maximum sustainable resolution and creation requests per second.                                            | Requests per second (RPS), saturation threshold         |
| Scalability                       | Ability to scale horizontally without re-encoding or breaking existing links.                               | Scaling efficiency, rebalancing cost, shard growth rate |
| Durability                        | Probability that a short URL remains resolvable over long time horizons (years or decades).                 | Data loss rate, backup success rate, RTO/RPO            |
| Identifier Stability              | Resistance to reuse, reassignment, or collision over time.                                                  | Collision probability, ID reuse events                  |
| Predictability and Security       | Susceptibility to enumeration, scraping, or targeted abuse.                                                 | Identifier entropy, enumeration success rate            |
| Operational Complexity            | Cost and difficulty of deployment, monitoring, and maintenance.                                             | Component count, operational incidents, on-call load    |
| Storage Efficiency                | Memory and disk cost per stored URL, including metadata.                                                    | Bytes per URL, index overhead                           |
| Abuse Resistance                  | Effectiveness in preventing spam, phishing, and malware distribution.                                       | Abuse reports, takedown latency                         |
| Observability                     | Availability of logs, metrics, and traces for operational and analytical purposes.                          | Metrics coverage, trace completeness                    |
| Semantic Correctness              | Proper use of HTTP semantics (redirect codes, caching behavior).                                            | Redirect code accuracy, cache header correctness        |
| Governance and Policy Flexibility | Ability to enforce ownership, takedowns, expiration, and compliance requirements.                           | Policy coverage, enforcement latency                    |

## QA

### How can the original URL be recovered from a shortened URL?

> In general, the original URL cannot be recovered algorithmically from the shortened URL alone. A URL shortener is not a reversible encoding scheme but a lookup-based indirection mechanism.

## References

- [URL Shortening](https://en.wikipedia.org/wiki/URL_shortening)
- [awesome-url-shortener](https://github.com/738/awesome-url-shortener)
- [bitly](https://bitly.com/)
