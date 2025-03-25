
# YouTube System Design (AWS Architecture)

![](https://i.postimg.cc/Zq29qRHx/01-Architecture-Flow.png)
*A scalable cloud-native design for video streaming platforms using AWS services.*

## 📌 Overview
This repository documents a high-level system design for a YouTube-like platform, focusing on:
- **Functional requirements**: Video streaming, uploads, search, and user interactions.
- **Non-functional requirements**: Scalability (500M+ DAU), high availability (99.9% uptime), and low-latency global streaming.
- **AWS-Centric Solutions**: Leveraging S3, CloudFront, DynamoDB, and other AWS services.

---

## 🏗️ Key Components

### Core AWS Services
| Component               | AWS Service              | Purpose                                  |
|-------------------------|--------------------------|------------------------------------------|
| **Video Storage**       | S3 + EFS                 | Durable blob storage for raw/encoded videos. |
| **CDN**                 | CloudFront               | Low-latency video delivery via edge caching. |
| **Metadata DB**         | DynamoDB                 | NoSQL storage for video titles, likes, etc. |
| **User Data**           | Aurora MySQL             | ACID-compliant relational data.          |
| **Encoding**            | MediaConvert             | Transcoding videos to multiple formats.  |
| **Real-Time Streaming** | IVS (Interactive Video)  | Live streaming support.                  |

### Design Highlights
- **Upload Pipeline**: S3 → SNS → Lambda → MediaConvert → CloudFront.
- **Scalability**: Auto Scaling (EC2), DynamoDB partitions, and read replicas (Aurora).
- **Cost Optimization**: S3 Intelligent-Tiering + Lifecycle Policies.

---

## 🚀 Quick Start
1. **Estimate Storage Needs**:
   ```python
   # Calculate storage for 500 hours/min uploads
   storage_per_min = 30  # MB
   total_storage = 500 * 60 * storage_per_min  # => 900 GB/min
   ```

2. **Deploy a Proof-of-Concept**:
   ```bash
   # Example: Launch CloudFront + S3 bucket
   aws cloudfront create-distribution \
     --origin-domain-name my-bucket.s3.amazonaws.com
   ```

3. **Adapt for Your Use Case**:
   - Replace IVS with **Kinesis Video Streams** for custom analytics.
   - Use **Rekognition** for video moderation.

---

## 🔍 Key Takeaways
- **Decoupling**: Separates user data (Aurora) from video metadata (DynamoDB).
- **Global Reach**: CloudFront + Local Zones reduce latency.
- **Cost Control**: S3 Lifecycle Policies archive old videos to Glacier.

---

## 📚 Resources
- [AWS Well-Architected Framework](https://aws.amazon.com/architecture/well-architected/)
- [DynamoDB Best Practices](https://docs.aws.amazon.com/amazondynamodb/latest/developerguide/best-practices.html)

---
