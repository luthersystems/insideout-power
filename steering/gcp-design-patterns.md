# GCP Infrastructure Design Patterns with InsideOut

This guide covers common Google Cloud Platform infrastructure patterns and how to design them with InsideOut.

## Web Application Patterns

### Simple Web App (Starter)
**Use Case:** Small web application, low traffic, cost-conscious

**Tell Riley:**
```
"I'm building a simple web application with user authentication. 
I expect about 100-500 concurrent users. I need a database for 
user data and file storage for uploads. Budget is under $200/month."
```

**Expected Stack:**
- **Compute:** Cloud Run (serverless containers)
- **Database:** Cloud SQL PostgreSQL (db-f1-micro)
- **Storage:** Cloud Storage
- **Load Balancer:** Cloud Load Balancing
- **CDN:** Cloud CDN (optional)

**Monthly Cost:** ~$120-180

### Scalable Web App (Growth)
**Use Case:** Growing application, variable traffic, need auto-scaling

**Tell Riley:**
```
"I have a web application that needs to handle traffic spikes. 
Currently 1000 users but expecting 10k+ users. I need auto-scaling, 
caching, and high availability. Budget up to $800/month."
```

**Expected Stack:**
- **Compute:** GKE (Google Kubernetes Engine)
- **Database:** Cloud SQL PostgreSQL (HA)
- **Caching:** Memorystore Redis
- **Storage:** Cloud Storage + Cloud CDN
- **Load Balancer:** Global Load Balancer with Cloud Armor

**Monthly Cost:** ~$500-700

### Enterprise Web App (Production)
**Use Case:** Mission-critical application, high availability, compliance

**Tell Riley:**
```
"I need an enterprise-grade web platform with 99.9% uptime. 
Multi-region deployment, compliance requirements, disaster recovery. 
Expecting 50k+ concurrent users. Budget up to $2000/month."
```

**Expected Stack:**
- **Compute:** Multi-regional GKE
- **Database:** Cloud SQL PostgreSQL (HA + Read Replicas)
- **Caching:** Memorystore Redis (HA)
- **Storage:** Cloud Storage (Multi-region)
- **Security:** Cloud Armor, Cloud KMS, Secret Manager
- **Monitoring:** Cloud Monitoring + Logging

**Monthly Cost:** ~$1200-1800

## API & Microservices Patterns

### Serverless API
**Use Case:** Event-driven API, variable load, pay-per-use

**Tell Riley:**
```
"I'm building a REST API with serverless functions. Traffic is 
unpredictable - sometimes zero, sometimes high spikes. 
I need a NoSQL database and want to minimize costs."
```

**Expected Stack:**
- **Compute:** Cloud Functions
- **API:** API Gateway
- **Database:** Firestore
- **Storage:** Cloud Storage
- **Auth:** Identity Platform (if needed)

**Monthly Cost:** ~$30-200 (highly variable)

### Microservices Platform
**Use Case:** Multiple services, container orchestration, service mesh

**Tell Riley:**
```
"I need a microservices platform with Kubernetes. About 10-15 
services, need service discovery, load balancing, and monitoring. 
Each service needs its own database access."
```

**Expected Stack:**
- **Compute:** GKE with Autopilot or Standard
- **Database:** Cloud SQL PostgreSQL + Memorystore
- **Storage:** Cloud Storage + Persistent Disks
- **Networking:** VPC with private clusters
- **Monitoring:** Cloud Monitoring + Logging

**Monthly Cost:** ~$600-1000

## Data & Analytics Patterns

### Data Lake
**Use Case:** Large-scale data storage and analytics

**Tell Riley:**
```
"I need to store and analyze large datasets. Daily ingestion 
of 100GB+ files, need to run SQL queries and ML workloads. 
Data retention for 7 years."
```

**Expected Stack:**
- **Storage:** Cloud Storage (multiple storage classes)
- **Analytics:** BigQuery + Dataflow
- **ML:** Vertex AI (optional)
- **Security:** Cloud KMS encryption
- **Backup:** Cross-region replication

**Monthly Cost:** ~$400-1200

### Real-time Analytics
**Use Case:** Streaming data processing, real-time dashboards

**Tell Riley:**
```
"I need to process streaming data in real-time. About 10k 
events per second, need to store in a time-series database 
and create real-time dashboards."
```

**Expected Stack:**
- **Streaming:** Pub/Sub
- **Processing:** Dataflow or Cloud Run
- **Database:** BigQuery + Bigtable
- **Monitoring:** Cloud Monitoring dashboards

**Monthly Cost:** ~$600-1000

## E-commerce Patterns

### Online Store
**Use Case:** E-commerce platform with inventory, payments, orders

**Tell Riley:**
```
"I'm building an e-commerce platform. Need to handle product 
catalog, user sessions, order processing, and payment integration. 
Expecting holiday traffic spikes."
```

**Expected Stack:**
- **Compute:** GKE (auto-scaling)
- **Database:** Cloud SQL PostgreSQL + Memorystore
- **Storage:** Cloud Storage + Cloud CDN
- **Queues:** Pub/Sub for order processing
- **Security:** Cloud Armor + Cloud KMS

**Monthly Cost:** ~$800-1200

## Security-First Patterns

### Healthcare Compliance
**Use Case:** Healthcare application with sensitive data

**Tell Riley:**
```
"I need a healthcare application that handles patient data. 
All data must be encrypted at rest and in transit. Need audit 
logging and access controls for HIPAA compliance."
```

**Expected Stack:**
- **Compute:** GKE in private clusters
- **Database:** Cloud SQL with encryption
- **Storage:** Cloud Storage with KMS encryption
- **Security:** Cloud Armor, Secret Manager, Cloud Audit Logs
- **Networking:** Private VPC, no public IPs

**Monthly Cost:** ~$600-1000

### Financial Services
**Use Case:** Fintech application with regulatory compliance

**Tell Riley:**
```
"I'm building a fintech application that processes payments. 
Need regulatory compliance, fraud detection, and real-time 
transaction processing."
```

**Expected Stack:**
- **Compute:** GKE in private clusters
- **Database:** Cloud SQL PostgreSQL (encrypted)
- **Caching:** Memorystore (encrypted)
- **Security:** Cloud Armor, Cloud KMS, Secret Manager
- **Monitoring:** Cloud Logging + Audit Logs

**Monthly Cost:** ~$1000-1500

## Development & Testing Patterns

### CI/CD Pipeline
**Use Case:** Automated testing and deployment

**Tell Riley:**
```
"I need a CI/CD pipeline for my application. Want automated 
testing, staging environment, and blue-green deployments."
```

**Expected Stack:**
- **CI/CD:** Cloud Build + Cloud Deploy
- **Compute:** GKE or Cloud Run
- **Database:** Cloud SQL (with staging replica)
- **Storage:** Cloud Storage for artifacts

**Monthly Cost:** ~$300-500

## Machine Learning Patterns

### ML Training Pipeline
**Use Case:** Model training and deployment

**Tell Riley:**
```
"I need to train machine learning models on large datasets 
and deploy them for real-time inference. Need GPU support 
for training and auto-scaling for inference."
```

**Expected Stack:**
- **Training:** Vertex AI Training (with GPUs)
- **Inference:** Vertex AI Endpoints or Cloud Run
- **Data:** Cloud Storage + BigQuery
- **Monitoring:** Vertex AI Model Monitoring

**Monthly Cost:** ~$800-2000 (varies with GPU usage)

## Cost Optimization Tips

### Development Environment
- Use smaller machine types (e2-micro, e2-small)
- Single-zone deployments for non-production
- Preemptible instances for batch workloads
- Scheduled shutdown for dev resources

### Production Optimization
- Committed Use Discounts for predictable workloads
- Preemptible VMs for fault-tolerant workloads
- Cloud Storage lifecycle policies
- Cloud CDN for global content delivery

### Monitoring Costs
- Set up billing alerts and budgets
- Use Cloud Billing reports for analysis
- Label resources for cost allocation
- Regular right-sizing with Recommender

## Common Configuration Choices

### Database Sizing
- **Small (< 10GB):** db-f1-micro or db-g1-small
- **Medium (10-100GB):** db-n1-standard-1 or db-n1-standard-2
- **Large (100GB+):** db-n1-standard-4 or higher

### GKE Node Pools
- **Development:** e2-medium (1 vCPU, 4GB RAM)
- **Production:** n1-standard-2 (2 vCPU, 7.5GB RAM)
- **High Performance:** c2-standard-4 (4 vCPU, 16GB RAM)

### Cloud Storage Classes
- **Frequent Access:** Standard
- **Infrequent Access:** Nearline
- **Archive:** Coldline or Archive

## Best Practices

### Security
- Use private GKE clusters
- Enable encryption at rest and in transit
- Use Secret Manager for credentials
- Implement IAM with least privilege

### High Availability
- Multi-zone deployments for production
- Regional persistent disks
- Read replicas for databases
- Cross-region backups

### Monitoring
- Cloud Monitoring alerts for key metrics
- Structured logging with Cloud Logging
- Application Performance Monitoring
- Cost monitoring and budgets

### Backup & Recovery
- Automated daily backups
- Cross-region backup replication
- Test restore procedures
- Document recovery processes

## GCP-Specific Features

### Serverless Options
- **Cloud Run:** Containerized applications
- **Cloud Functions:** Event-driven functions
- **App Engine:** Platform-as-a-Service

### Data Services
- **BigQuery:** Data warehouse and analytics
- **Firestore:** NoSQL document database
- **Bigtable:** Wide-column NoSQL database
- **Spanner:** Globally distributed SQL database

### AI/ML Services
- **Vertex AI:** Unified ML platform
- **AutoML:** No-code ML model training
- **AI Platform:** Custom ML workflows
- **Pre-trained APIs:** Vision, Language, Translation

### Networking
- **Global Load Balancing:** Anycast IP addresses
- **Cloud CDN:** Global content delivery
- **Cloud Armor:** DDoS protection and WAF
- **VPC:** Software-defined networking

## Troubleshooting Common Issues

### "Too expensive"
- Ask Riley about Committed Use Discounts
- Consider preemptible instances
- Use appropriate storage classes
- Implement auto-scaling to reduce idle costs

### "Need better performance"
- Discuss caching with Memorystore
- Consider Cloud CDN for static content
- Upgrade machine types or add more nodes
- Implement read replicas for databases

### "Security concerns"
- Ask about Cloud Armor configuration
- Implement private GKE clusters
- Use Cloud KMS for encryption
- Enable Cloud Audit Logs

### "Compliance requirements"
- Discuss data residency options
- Implement proper IAM policies
- Enable audit logging
- Use encryption for data at rest and in transit

## Regional Considerations

### Multi-Region Deployments
- Use regional resources for high availability
- Consider data residency requirements
- Plan for disaster recovery
- Monitor cross-region latency

### Global Applications
- Leverage Google's global network
- Use Cloud CDN for content delivery
- Consider Anycast IP addresses
- Plan for regional failover

Remember: Riley understands GCP's unique strengths like global networking, serverless options, and integrated AI/ML services. Be specific about your requirements and let her guide you to the best GCP services for your use case.