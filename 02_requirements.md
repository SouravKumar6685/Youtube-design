# YouTube-like System Design on AWS

## Requirements

### Functional Requirements
- **Stream videos**  
- **Upload videos**  
- **Search videos** (by titles)  
- **Like/dislike videos**  
- **Add comments**  
- **View thumbnails**  

```mermaid
pie
    title Functional Requirements
    "Stream videos" : 25
    "Upload videos" : 20
    "Search videos" : 15
    "Like/dislike" : 15
    "Comments" : 15
    "Thumbnails" : 10
```

### Non-Functional Requirements
- **High Availability**: 99%+ uptime.  
- **Scalability**: Handle growth in storage, bandwidth, and concurrent requests.  
- **Performance**: Smooth streaming experience.  
- **Reliability**: No data loss (eventual consistency acceptable).  

```mermaid
graph LR
    A[HA] --> B[99%+ uptime]
    C[Scalability] --> D[Petabyte storage]
    E[Performance] --> F[<2s latency]
    G[Reliability] --> H[11 9's durability]
```

---

## Resource Estimation

### Assumptions
- **Total users**: 1.5 billion  
- **Daily active users (DAU)**: 500 million  
- **Avg video length**: 5 minutes  
- **Video size (pre-encoding)**: 600 MB → **Post-encoding**: 30 MB  

```mermaid
pie
    title User Distribution
    "Total Users" : 1500
    "DAU" : 500
    "Content Creators" : 50
```

### Storage
- **Content uploaded/minute**: 500 hours = 30,000 minutes  
- **Storage/minute**: 6 MB (for 1 video format) → **5 formats**: 30 MB/min  
- **Total storage/minute**:  
  `500 hrs × 60 min/hr × 30 MB/min = 900 GB/min`  

```mermaid
flowchart LR
    A[500 hrs/min] --> B[x60 min]
    B --> C[x30 MB]
    C --> D[=900GB/min]
```

### Bandwidth
- **Upload-to-view ratio**: 1:300  
- **Upload bandwidth**:  
  `500 hrs × 60 min/hr × 120 MB/min × 8 bits/Byte ÷ 60 sec = 480 Gbps`  

```mermaid
flowchart TB
    Upload[480 Gbps upload] --> Process[Encoding]
    Process --> Delivery[14.4 Tbps delivery]
```

### Servers
- **Concurrent requests**: 500M DAU  
-  **Requests/server**: Youtube server handles 8,000/sec → **Servers needed**: `500M / 8k = 62,500`  

```mermaid
pie
    title Server Distribution
    "Web Servers" : 40000
    "Encoding Nodes" : 15000
    "Cache Servers" : 7500
```

---

## AWS Components Mapping

| Component          | AWS Service                     | Notes                                  |
|--------------------|---------------------------------|----------------------------------------|
| **CDN**            | Amazon CloudFront               |Low-latency video delivery.            |
| **Web Servers**    | EC2 (Auto Scaling Groups)       | Host application logic.                |
| **Upload Storage** | S3                              |Store raw/encoded videos.              |
| **Video Metadata** | DynamoDB                        |Titles, likes, comments (NoSQL).       |
| **User Data**      | Cognito + DynamoDB              |Authentication + user profiles.        |
| **Load Balancer**  | ALB/ELB                         |Distribute traffic to EC2 instances.   |
| **Encoders**       | AWS Elemental MediaConvert      |Transcode videos to multiple formats.  |
| **Streaming**      | Amazon IVS or Kinesis Video     |Real-time video streaming.             |
| **Thumbnails**     | S3 + CloudFront                 |Fast delivery of thumbnails.           |
| **Comments**       | DynamoDB/ElastiCache (Redis)    |Low-latency read/write.                |

```mermaid
graph TD
    A[User] --> B[CloudFront CDN]
    B --> C[ALB/ELB]
    C --> D[EC2 Auto Scaling]
    D --> E[S3 Storage]
    D --> F[DynamoDB]
    D --> G[Elemental MediaConvert]
    E --> H[IVS/Kinesis Video]
    F --> I[ElastiCache]
```

---

## Key Design Choices
1. **Eventual Consistency**: Notifications for new videos may delay (non-critical).  
2. **Scalability**:  
   - **Horizontal Scaling**: EC2 Auto Scaling + DynamoDB sharding.  
   - **Storage**: S3 (unlimited scalability).  
3. **Performance**:  
   - **Caching**: CloudFront + ElastiCache for metadata/comments.  
   - **Parallel Processing**: Lambda for encoding/transcoding jobs.  


```mermaid
graph TB
    A[Scalability] --> A1[EC2 Auto Scaling]
    A --> A2[DynamoDB Sharding]
    B[Performance] --> B1[CloudFront]
    B --> B2[ElastiCache]
    C[Reliability] --> C1[Multi-AZ]
    C --> C2[S3 Versioning]
```

## Data Flow

```mermaid
sequenceDiagram
    User->>+EC2: Upload Video
    EC2->>+S3: Store Raw
    S3->>+MediaConvert: Trigger Encoding
    MediaConvert->>+S3: Store Encoded
    S3->>+CloudFront: Distribute
    CloudFront->>+User: Deliver Stream
```

---

## Formulas
- **Storage/min**:  
  `Total Upload (min) × Storage per min (MB)`  
- **Bandwidth (Gbps)**:  
  `(Hours/min × 60 × MB/min × 8) ÷ 60`  
- **Servers**:  
  `Active users / Queries per server`  



```mermaid
flowchart LR
    A[Storage] --> B[Uploads × Size]
    C[Bandwidth] --> D[MB × 8 ÷ 60]
    E[Servers] --> F[Users ÷ QPS]
```

## Failure Handling

```mermaid
graph TD
    A[EC2 Failure] --> B[Auto Scaling]
    C[S3 Outage] --> D[Cross-Region Replication]
    E[DynamoDB Slow] --> F[DAX Accelerator]
```
