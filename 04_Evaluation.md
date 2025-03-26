# YouTube-like System Design: Evaluation (AWS)

## Fulfilling Requirements

```mermaid
graph TD
    A[Requirements] --> B[Low Latency]
    A --> C[Scalability]
    A --> D[Availability]
    A --> E[Reliability]
    B --> F[CloudFront]
    C --> G[Auto Scaling]
    D --> H[Multi-AZ]
    E --> I[S3 Durability]
```

| Requirement          | AWS Strategy                                                                 | Techniques                                                                 |
|----------------------|------------------------------------------------------------------------------|----------------------------------------------------------------------------|
| **Low Latency**      | CloudFront (Global CDN) + Local Zones                                        | - Edge caching for popular content.<br>- S3 + CloudFront for thumbnails.   |
| **Scalability**      | Auto Scaling (EC2), DynamoDB, S3                                             | - Horizontal scaling of web/app servers.<br>- DynamoDB auto-partitioning.  |
| **Availability**     | Multi-AZ Deployments + Global Tables (DynamoDB)                              | - Data replication across AZs.<br>- ELB health checks for dead servers.    |
| **Reliability**      | S3 (11 nines durability) + Backup (AWS Backup)                               | - Data partitioning (user vs. video metadata).<br>- Heartbeat monitoring.  |


```mermaid
flowchart LR
    subgraph AWS_Strategies
        A[Low Latency] --> A1[CloudFront]
        A --> A2[Local Zones]
        B[Scalability] --> B1[EC2 Auto Scaling]
        B --> B2[DynamoDB]
        C[Availability] --> C1[Multi-AZ]
        C --> C2[Global Tables]
    end
```

---

## Trade-offs & AWS Solutions

### 1. **Consistency**
   - **Choice**: Eventual consistency (over strong consistency).  
   - **Why**: High availability prioritized (CAP theorem).  
   - **AWS Service**: DynamoDB (tunable consistency for user data).  


```mermaid
pie
    title Consistency Choices
    "Eventual" : 70
    "Strong" : 30
```

### 2. **Distributed Cache**
   - **Choice**: Amazon ElastiCache (Redis/Memcached) over centralized cache.  
   - **Why**: Fault tolerance + LRU eviction for long-tailed access patterns.  

```mermaid
graph LR
    A[Centralized Cache] --> B[Single Point of Failure]
    C[Distributed Cache] --> D[Fault Tolerant]
    D --> E[ElastiCache Redis]
```

### 3. **Bigtable vs. MySQL**
   | Aspect           | AWS Bigtable Alternative (DynamoDB)       | AWS MySQL Alternative (Aurora)          |
   |------------------|------------------------------------------|-----------------------------------------|
   | **Use Case**     | Thumbnails, video metadata (high scale)  | User data (structured, ACID-compliant). |
   | **Scalability**  | Auto-partitioning (NoSQL).               | Read replicas (limited writes).         |

```mermaid
flowchart TB
    subgraph NoSQL
        A[DynamoDB] --> A1[Auto-partitioning]
        A --> A2[Unlimited Scale]
    end
    subgraph SQL
        B[Aurora] --> B1[Read Replicas]
        B --> B2[ACID Compliance]
    end
```

### 4. **Public vs. Private CDN**
   - **Public CDN**: CloudFront (cost-effective for low-traffic regions).  
   - **Private CDN**: CloudFront + Local Zones (high-traffic optimization).  

```mermaid
pie
    title CDN Distribution
    "Public CloudFront" : 80
    "Private Local Zones" : 20
```

### 5. **Duplicate Videos**
   - **Problem**: 9.5 PB/year wasted (50 duplicate hours/minute).  
   - **AWS Solution**:  
     - **AI/ML**: Amazon Rekognition (content matching).  
     - **Storage**: S3 Lifecycle Policies to archive duplicates.  

```mermaid
sequenceDiagram
    Upload->>+S3: Store Video
    S3->>+Rekognition: Check Duplicates
    Rekognition-->>-S3: Match Found
    S3->>Lifecycle: Archive Duplicate
```

---

## Future Scaling with AWS

### Challenges & Solutions
| Scaling Need          | AWS Service/Strategy                        | Benefit                                      |
|-----------------------|--------------------------------------------|----------------------------------------------|
| **Web Servers**       | EC2 Auto Scaling + ALB                      | Handle traffic spikes.                       |
| **Datastores**        | DynamoDB (NoSQL) + Aurora (SQL)             | Avoid MySQL choke points.                    |
| **Disaster Recovery** | AWS Backup + Cross-Region Replication       | RTO/RPO compliance.                          |
| **Vitess Alternative**| Amazon Aurora Global Database               | MySQL-compatible + NoSQL-like scaling.       |

```mermaid
gantt
    title Scaling Timeline
    dateFormat  YYYY-MM
    section Infrastructure
    EC2 Scaling       :active, 2023-01, 2023-12
    Database Sharding :2023-06, 2024-06
    section Optimization
    CDN Expansion    :2023-03, 2023-09
    Cost Analysis    :2023-09, 2024-03
```

### Custom Web Server
- **AWS Option**: Custom EC2 AMI (optimized for video serving).  
- **Why**: General-purpose servers (e.g., Lighttpd) may not suffice at scale.  

## Cost Optimization Framework
```mermaid
pie
    title Cost Distribution
    "Compute" : 40
    "Storage" : 35
    "Data Transfer" : 25
```

---



# YouTube-like System Design: Evaluation (AWS)



## Fulfilling Requirements


| Requirement          | AWS Strategy                                                                 |
|----------------------|------------------------------------------------------------------------------|
| **Low Latency**      | CloudFront (Global CDN) + Local Zones                                        |
| **Scalability**      | Auto Scaling (EC2), DynamoDB, S3                                             |
| **Availability**     | Multi-AZ Deployments + Global Tables (DynamoDB)                              |

---






### Scaling Solutions Matrix
```mermaid
graph TD
    A[Scaling Need] --> B[Web Servers]
    A --> C[Datastores]
    A --> D[Disaster Recovery]
    B --> B1[EC2 Auto Scaling]
    C --> C1[DynamoDB+Aurora]
    D --> D1[Cross-Region Replication]
```

---



## Failure Handling Architecture
```mermaid
flowchart TD
    A[EC2 Failure] --> B[Auto Scaling Replacement]
    C[AZ Outage] --> D[Multi-AZ Failover]
    E[Region Outage] --> F[Global Database]
```



## Key Takeaways

```mermaid
mindmap
  root((Key Takeaways))
    Eventual Consistency
      DynamoDB Streams
      Async Updates
    Cost Performance
      Public CDN
      Private Zones
    Storage
      S3 Intelligent-Tiering
      Rekognition
    Scaling
      Horizontal
      Vertical
```

1. **Eventual Consistency**: Acceptable for video metadata (DynamoDB Streams for async updates).  
2. **Cost vs. Performance**:  
   - Public CDN (CloudFront) for most regions.  
   - Private CDN (Local Zones) for high-density areas.  
3. **Storage Optimization**:  
   - S3 Intelligent-Tiering for videos.  
   - Rekognition to detect duplicates.  
4. **Scalability**:  
   - **Horizontal**: EC2 + DynamoDB.  
   - **Vertical**: Aurora (SQL) + ElastiCache (Redis).  

