---
layout: post
title: "distributed database overview"
description: "A high level technical overview of distributed databases"
category: tech
tags: [coyote,tech,cap,database,glossary]
---
{% include JB/setup %}



# Distributed DB Overview

## Document Scope

This document is intended to be a very rough and high level technical overview of recent generation Distributed Database system and is to be used as an introduction to key concepts and features.  More reading in links.

## Distributed Data store common features

- [Distributed data store](https://en.wikipedia.org/wiki/Distributed_data_store)

  - Network of nodes where data is stored.
  - Usually more than one node

- [Distributed database](https://en.wikipedia.org/wiki/Distributed_database)

  - Often but not always a [non-relational (e.g. NoSQL)](https://en.wikipedia.org/wiki/NoSQL) datastore

  - generally designed for access over a large number of nodes
  - consists of loosely coupled nodes that share no physical components (vs. more tightly integrated [parallel systems](https://en.wikipedia.org/wiki/Parallel_computing))
  - Provide a single working interface to the user (i.e. looks like a stand alone database)
  - Can easily be horizontally scaled by adding more nodes

See also [Distributed Hash Table](https://en.wikipedia.org/wiki/Distributed_hash_table)

### Distributed DB vs. Centralized DB

Some very broad comparisons.

- Centralized DB
  - Easy initial setup
  - One database file on one node
  - Single point of failure by nature
  - Managing, updating, backup of data is easier
  - More contention for user data stored on system
  - Limit to scalability when everything is on one system.  
    - Scaling is more complex and expensive
      - Add IOPs (by adding raid disks)
      - Add CPUs and cores
      - And that's about it.
- Distributed DB
  - Database files in multiple locations on the network or even different networks
  - Increased system complexity
  - Less contention for user data stored in the DB
  - Scale horizontally by adding nodes
  - Can still have performance issues when updates are focused on a single record (hot record/hot key). 
    - Need to be aware when designing data schema.

## CAP Theorem 

[CAP Theorem](https://en.wikipedia.org/wiki/CAP_theorem) (a.k.a. Brewer's Theorem) states that any distributed data store can only provide 2 of the following three guarantees

- Consistency
  - Every read receives the most recent write or an error.
    - Important when every user needs to see the same data (e.g. financial transaction)
- Availability
  - Every request receives a (non-error) response which may or may not contain the most recent write
    - Important when uptime is key and an out of date response is acceptable
- Partition tolerance
  - The system continues to operate despite an arbitrary number of messages being dropped (or delayed) by the network between nodes.

When a network failure occurs the distributed data store will need to:

- Ensure availability by proceeding with the database operation.  This is referred to as Available and Partition-tolerant (AP)
- Ensure consistency by canceling the operation.  This is Consistent and Partition-tolerant (CP)

## Comparison of example DDBMSes

| DB                                     | CAP Type | Notes                                                        |
| -------------------------------------- | -------- | ------------------------------------------------------------ |
| Mongo / Redis / Memcache               | CP       | Node will shut down rather than serve old or out of date data |
| Cassandra / ScyllaDB / Couch DB / RIAK | AP       | Node will stay up and serve old/out of date data and update once network partition is resolved |
| Aerospike                              | AP or CP | Database can be configured as *Available and Partition-tolerant* (AP) by default or *Consistent and Partition-tolerant* (CP) (e.g. Aerospike [strong consistency mode](https://docs.aerospike.com/server/architecture/consistency)). |



## Consensus protocols

Most common protocols used include:

- Paxos - by Leslie Lamport - Widely used along with Raft - Crash tolerant
- Raft - variant of Paxos with simplified log handling

### Consensus Protocols

[Paxos](https://en.wikipedia.org/wiki/Paxos_(computer_science)) is a protocol family used for solving [consensus](https://en.wikipedia.org/wiki/Consensus_(computer_science)) (finding an agreement on a result) between nodes of a distributed system.  Solving consensus allows a distributed system to continue working in a predictable way during network partitioning or process/server failure. 

Consensus is generally defined as agreement among processes on a single value.  It is assumed that some processes can fail or become unreliable so the protocols must be fault-tolerant.  This is generally done by having the various processes communicate the value to other processes and agree on a single value.  

Paxos is used when durability is required for handling large amounts of data.  Paxos will attempt to make progress during episodes when replicas are unavailable.

[Raft vs. Paxos](https://arxiv.org/pdf/2004.05074) - A major difference between Raft and Paxos is that Raft only allows servers with updated logs to become leader while in Paxos, any server can become the leader so long as the logs are updated subsequently. This is done to simplify the leader election process and make it more efficient. 

