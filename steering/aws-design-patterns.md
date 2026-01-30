# AWS Infrastructure Design Patterns with InsideOut

This guide covers common AWS infrastructure patterns and how to design them with InsideOut.

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
- **Compute:** ECS Fargate (cost-effective containers)
- **Database:** RDS PostgreSQL (db.t3.micro)
- **Storage:** S3 (file uploads)
- **Load Balancer:** ALB
- **CDN:** CloudFront (optional)

**Monthly Cost:** ~$150-200

### Scalable Web App (Growth)
**Use Case:** Growing application, variable traffic, need auto-scaling

**Tell Riley:**
```
"I have a web application that needs to handle traffic spikes. 
Currently 1000 users but expecting 10k+ users. I need auto-scaling, 
caching, and high availability. Budget up to $800/month."
```

**Expected Stack:**
- **Compute:** EKS (Kubernetes for scaling)
- **Database:** RDS PostgreSQL (Multi-AZ)
- **Caching:** ElastiCache Redis
- **Storage:** S3 + CloudFront CDN
- **Load Balancer:** ALB with WAF

**Monthly Cost:** ~$600-800

### Enterprise Web App (Production)
**Use Case:** Mission-critical application, high availability, compliance

**Tell Riley:**
```
"I need an enterprise-grade web platform with 99.9% uptime. 
Multi-region deployment, SOC2 compliance, disaster recovery. 
Expecting 50k+ concurrent users. Budget up to $2000/month."
```

**Expected Stack:**
- **Compute:** Multi-region EKS
- **Database:** RDS PostgreSQL (Multi-AZ + Read Replicas)
- **Caching:** ElastiCache Redis (Multi-AZ)
- **Storage:** S3 (Cross-region replication)
- **Security:** WAF, KMS, Secrets Manager
- **Monitoring:** CloudWatch + Grafana

**Monthly Cost:** ~$1500-2000

## API & Microservices Patterns

### Serverless API
**Use Case:** Event-driven API, variable load, pay-per-use

**Tell Riley:**
```
"I'm building a REST API with Lambda functions. Traffic is 
unpredictable - sometimes zero, sometimes high spikes. 
I need a NoSQL database and want to minimize costs."
```

**Expected Stack:**
- **Compute:** Lambda functions
- **API:** API Gateway
- **Database:** DynamoDB
- **Storage:** S3
- **Security:** Cognito (if auth needed)

**Monthly Cost:** ~$50-300 (highly variable)

### Microservices Platform
**Use Case:** Multiple services, container orchestration, service mesh

**Tell Riley:**
```
"I need a microservices platform with Kubernetes. About 10-15 
services, need service discovery, load balancing, and monitoring. 
Each service needs its own database access."
```

**Expected Stack:**
- **Compute:** EKS with multiple node groups
- **Database:** RDS PostgreSQL + ElastiCache
- **Storage:** S3 + EBS
- **Networking:** VPC with private subnets
- **Monitoring:** CloudWatch + Prometheus

**Monthly Cost:** ~$800-1200

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
- **Storage:** S3 (multiple storage classes)
- **Analytics:** Athena + Glue
- **ML:** SageMaker (optional)
- **Security:** KMS encryption
- **Backup:** Cross-region replication

**Monthly Cost:** ~$500-1500

### Real-time Analytics
**Use Case:** Streaming data processing, real-time dashboards

**Tell Riley:**
```
"I need to process streaming data in real-time. About 10k 
events per second, need to store in a time-series database 
and create real-time dashboards."
```

**Expected Stack:**
- **Streaming:** Kinesis Data Streams
- **Processing:** Lambda or ECS
- **Database:** OpenSearch + RDS
- **Monitoring:** CloudWatch dashboards

**Monthly Cost:** ~$800-1200

## E-commerce Patterns

### Online Store
**Use Case:** E-commerce platform with inventory, payments, orders

**Tell Riley:**
```
"I'm building an e-commerce platform. Need to handle product 
catalog, user sessions, order processing, and payment integration. 
Expecting Black Friday traffic spikes."
```

**Expected Stack:**
- **Compute:** EKS (auto-scaling)
- **Database:** RDS PostgreSQL + ElastiCache
- **Storage:** S3 + CloudFront
- **Queues:** SQS for order processing
- **Security:** WAF + KMS

**Monthly Cost:** ~$1000-1500

## Security-First Patterns

### HIPAA Compliant
**Use Case:** Healthcare application with PHI data

**Tell Riley:**
```
"I need a HIPAA-compliant web application for healthcare data. 
All data must be encrypted at rest and in transit. Need audit 
logging and access controls."
```

**Expected Stack:**
- **Compute:** ECS in private subnets
- **Database:** RDS with encryption
- **Storage:** S3 with KMS encryption
- **Security:** WAF, Secrets Manager, CloudTrail
- **Networking:** Private VPC, no public access

**Monthly Cost:** ~$800-1200

### Financial Services
**Use Case:** Fintech application with PCI compliance

**Tell Riley:**
```
"I'm building a fintech application that processes payments. 
Need PCI DSS compliance, fraud detection, and real-time 
transaction processing."
```

**Expected Stack:**
- **Compute:** EKS in private subnets
- **Database:** RDS PostgreSQL (encrypted)
- **Caching:** ElastiCache (encrypted)
- **Security:** WAF, KMS, Secrets Manager
- **Monitoring:** CloudWatch + CloudTrail

**Monthly Cost:** ~$1200-1800

## Development & Testing Patterns

### CI/CD Pipeline
**Use Case:** Automated testing and deployment

**Tell Riley:**
```
"I need a CI/CD pipeline for my application. Want automated 
testing, staging environment, and blue-green deployments."
```

**Expected Stack:**
- **CI/CD:** CodePipeline + CodeBuild
- **Compute:** ECS or EKS
- **Database:** RDS (with staging replica)
- **Storage:** S3 for artifacts

**Monthly Cost:** ~$400-600

## Cost Optimization Tips

### Development Environment
- Use smaller instance types (t3.micro, t3.small)
- Single-AZ deployments for non-production
- Scheduled shutdown for dev resources

### Production Optimization
- Reserved Instances for predictable workloads
- Spot Instances for batch processing
- S3 Intelligent Tiering for storage
- CloudFront for global content delivery

### Monitoring Costs
- Set up billing alerts
- Use Cost Explorer for analysis
- Tag resources for cost allocation
- Regular right-sizing reviews

## Common Configuration Choices

### Database Sizing
- **Small (< 10GB):** db.t3.micro
- **Medium (10-100GB):** db.t3.small or db.m5.large
- **Large (100GB+):** db.m5.xlarge or higher

### EKS Node Groups
- **Development:** t3.medium (2 vCPU, 4GB RAM)
- **Production:** m5.large (2 vCPU, 8GB RAM)
- **High Performance:** c5.xlarge (4 vCPU, 8GB RAM)

### S3 Storage Classes
- **Frequent Access:** Standard
- **Infrequent Access:** Standard-IA
- **Archive:** Glacier or Deep Archive

## Best Practices

### Security
- Always use private subnets for databases
- Enable encryption at rest and in transit
- Use Secrets Manager for credentials
- Implement least-privilege access

### High Availability
- Multi-AZ deployments for production
- Auto-scaling groups for compute
- Read replicas for databases
- Cross-region backups

### Monitoring
- CloudWatch alarms for key metrics
- Log aggregation and analysis
- Performance monitoring
- Cost monitoring and alerts

### Backup & Recovery
- Automated daily backups
- Cross-region backup replication
- Test restore procedures
- Document recovery processes

## Troubleshooting Common Issues

### "Too expensive"
- Ask Riley about cost optimization options
- Consider smaller instance types
- Use Spot Instances where appropriate
- Implement auto-scaling to reduce idle costs

### "Need better performance"
- Discuss caching strategies (ElastiCache)
- Consider CDN for static content
- Upgrade database instance types
- Implement read replicas

### "Security concerns"
- Ask about WAF configuration
- Implement VPC with private subnets
- Use KMS for encryption
- Enable CloudTrail for audit logging

Remember: Riley is your expert guide. Be specific about your requirements, ask questions, and let her recommend the best AWS services for your use case.