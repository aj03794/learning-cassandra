# Data Modeling in Cassandra

## Basic Rules in Cassandra Data Modeling

- https://www.datastax.com/blog/basic-rules-cassandra-data-modeling

- Writes are cheap, if you can perform extra writes to improve efficency of reads, you should do it
    - Reads tend to be more expensive
- Denormalization and duplication of data is normal in Cassandra
    - Disk space is generally the cheapest resource

<br>

- 2 high level goals for your data model
    - Spread data evenly around the cluster
    - minimize the number of partition reads

### Rule 1: Spread Data Evenly Around the Cluster

- You want every node in the cluster to have roughly the same amount of data
- Rows are spread around the cluster based on a hash of the partition key (first element of the `PRIMARY KEY`)
- Key to this is picking a good primary key

### Rule 2: Minimize the Number of Partitions Read

- Partitions are groups of rows that share the same partition key
    - cluster keys determine row location within a parition
- When you issue a read query, you want to read rows from as few paritions as possible
    - Each paritition may reside on a different node
    - The coordinator will generally need to issue separate commands to separate nodes for each partition you request
        - This adds a lot of overhead and increases the variation in latency

#### Conflicting Rules?

- Rule 1 and 2 can butt heads, they'll need to be balanced

### Model Around Your Queries

- Way to minimize partition reads is to model your data to fix your queries
- **Don't model around relations**
- **Don't odel around objects**

#### Step 1: Determine What Queries to Support

- Try to determine exactly what queries you need to support

#### Step 2: Try to create a table where you can satisfy your query by reading (roughly) one partition

- In practice, this generally means you will use roughly one table per query pattern
- If you need to support multiple query patterns, you usually need more than on table
- **Each table should pre-build the "answer" to a high-level query that you need to support**
    - If you need different types of answers, you usually need different tables
        - Data duplication is okay


# Cassandra Partitions

- https://medium.com/@herryg91/in-depth-why-modeling-in-cassandra-is-important-ba25081dafd5