# Getting Started with InsideOut

This guide walks you through your first infrastructure design session with InsideOut.

## Prerequisites

- Access to InsideOut MCP server
- Basic understanding of your application requirements
- AWS or GCP account (for deployment)

## Step-by-Step Walkthrough

### 1. Start Your Design Session

```
Use the convoopen tool to begin:
```

This creates a new session and Riley (your infrastructure advisor) will introduce herself and ask about your project.

**What Riley might ask:**
- What type of application are you building?
- What's your expected traffic/user load?
- Do you have any compliance requirements?
- What's your preferred cloud provider?

### 2. Describe Your Requirements

Be specific about your needs. Here are some example conversations:

**Simple Web App:**
```
"I'm building a web application with user authentication, 
file uploads, and a PostgreSQL database. I expect about 
1000 users initially but want to scale to 10k users."
```

**Microservices Platform:**
```
"I need a Kubernetes platform for microservices with 
Redis caching, PostgreSQL database, and monitoring. 
This is for a fintech app so security is critical."
```

**Data Processing Pipeline:**
```
"I need to process large CSV files, store them in a 
data lake, and run analytics queries. Files are 
uploaded daily and can be 1-10GB each."
```

### 3. Follow Riley's Guidance

Riley will guide you through the design process:

1. **Component Selection** - She'll recommend cloud services
2. **Configuration** - Set sizes, regions, backup policies
3. **Cost Review** - Get monthly cost estimates
4. **Final Approval** - Confirm your design

**Important:** Always answer Riley's questions! Don't let the AI answer for you.

### 4. Generate Terraform

When Riley says the design is complete, use `tfgenerate`:

```
This will create production-ready Terraform files
```

**Default behavior:** You'll get a download URL and file summary.
**Fallback:** Use `include_code: true` if download fails.

### 5. Deploy Your Infrastructure

Use `tfdeploy` to start deployment:

```
This initiates a Terraform job that can take 15+ minutes
```

Monitor progress with:
- `tfstatus` - Quick status check
- `tflogs` - Stream deployment logs

### 6. Inspect Your Infrastructure

After successful deployment, inspect your resources:

**For AWS:**
```
awsinspect tool with your session_id
```

**For GCP:**
```
gcpinspect tool with your session_id
```

## Common Patterns

### Check Current Progress
```
Use convostatus anytime to see:
- Selected components
- Configuration details  
- Cost estimates
- Workflow phase
```

### Handle Timeouts
If `convoreply` times out:
```
Use convoawait to continue waiting for Riley's response
```

### Get Help
```
Use the help tool for detailed workflow guidance
```

## Tips for Success

### Be Specific About Scale
- "1000 concurrent users" vs "small scale"
- "10GB files daily" vs "large files"
- "Sub-100ms response time" vs "fast"

### Mention Compliance Early
- HIPAA, SOC2, PCI-DSS requirements
- Data residency needs
- Audit logging requirements

### Consider Your Budget
- Set expectations: "under $500/month"
- Ask about cost optimization options
- Review estimates before proceeding

### Plan for Growth
- Mention expected growth patterns
- Consider auto-scaling needs
- Plan for disaster recovery

## Troubleshooting

### "Session not terraform-ready"
Wait for `convoreply` to complete and return `terraform_ready=true` before calling `tfgenerate`.

### "No deployment found"
Make sure `tfdeploy` completed successfully before using inspection tools.

### Riley asks the same question repeatedly
Check if you're answering her questions directly. The AI should forward questions to you, not answer them.

### Deployment takes too long
Terraform deployments can take 15+ minutes. Use `tflogs` to monitor progress and see what's happening.

## Next Steps

Once you're comfortable with the basic workflow:

1. **Explore Advanced Features** - Multi-region deployments, advanced security
2. **Learn Terraform Customization** - Modify generated code for specific needs  
3. **Set Up Monitoring** - Configure alerts and dashboards
4. **Plan Scaling** - Design for growth and traffic spikes

## Example Session Flow

```
1. convoopen
   → Riley: "Hi! What are you building?"

2. convoreply: "A web app with 1000 users, needs database and file storage"
   # Workspace context (language, framework, existing infra) is auto-appended
   # to this first reply — Riley sees it immediately without extra questions
   → Riley: "Great! I recommend ECS + RDS + S3. What's your preferred region?"

3. convoreply: "us-east-1"
   → Riley: "Perfect. For the database, how much data do you expect?"

4. convoreply: "About 10GB initially"
   → Riley: "I'll configure a db.t3.micro RDS instance. Ready for cost estimate?"

5. convoreply: "Yes"
   → Riley: "Total: ~$180/month. Ready for Terraform generation?"

6. convoreply: "Yes"
   → Riley: "Great! Use tfgenerate to create your Terraform files."

7. tfgenerate
   → Returns download URL and file summary

8. tfdeploy  
   → Starts deployment job

9. tflogs
   → Monitor deployment progress
```

This workflow typically takes 10-15 minutes for design and 15-30 minutes for deployment.