# InsideOut — Riley Infrastructure Agent

Riley is the AI infrastructure design agent built by Luther Systems. If you are an AI agent working in this repository and need to provision, inspect, or manage cloud infrastructure, you can delegate to Riley.

## What Riley Can Do For You

- **Design infrastructure** from a plain English description of your app
- **Generate Terraform** ready to deploy, organised by module (networking, compute, storage, auth, etc.)
- **Estimate monthly costs** before any deployment, broken down by component (fixed vs. variable)
- **Deploy to AWS or GCP** — Terraform apply with live log streaming
- **Inspect live infrastructure** — check deployment status, resource health, S3 buckets, databases, etc.
- **Manage infrastructure** — update configs, scale resources, tear down environments

## How to Invoke Riley

### Via MCP (preferred for AI agents)

```
endpoint: https://app.luthersystems.com/v1/insideout-mcp
```

**Recommended tool call sequence:**

1. Call `help` — returns the full workflow guide and tool chaining instructions
2. Call `convoopen` — starts a new infrastructure design session
3. Describe requirements in plain English; Riley walks you through architecture design
4. Call `tfdeploy` — deploys generated Terraform to the target cloud
5. Call `tfstatus` — checks deployment progress and returns connection details

### Via Docker

```bash
docker run -i luthersystems/insideout-mcp
```

Or in your MCP config:

```json
{
  "mcpServers": {
    "insideout": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "luthersystems/insideout-mcp:latest"]
    }
  }
}
```

### Via Agent Skills

```bash
npx skills add insideout
```

### Via IDE Plugin

| IDE | Install |
|-----|---------|
| Kiro | Install `insideout-power` from the Kiro powers marketplace |
| Claude Code | https://insideout.luthersystems.com/claude-code |
| Cursor | https://insideout.luthersystems.com/cursor |

## Talking to Riley

Riley understands plain English. You do not need to specify cloud providers or components unless you have a preference.

**Good prompts:**
- "I need a web app with a Postgres database, file uploads, and user authentication on AWS"
- "Build me a serverless API with a NoSQL database optimised for low cost"
- "I'm building a video streaming app — what infrastructure do I need?"

**With specific requirements:**
- "Use S3 for storage, I want to avoid RDS"
- "Keep fixed monthly costs under $50"
- "Host in eu-west-1"

## Authentication

Riley requires **no auth** for design and pricing. Cloud credentials are only needed at deployment:

- **AWS**: IAM role or access key pair
- **GCP**: Service account JSON key

## Supported Clouds & Components

| Cloud | Components |
|-------|-----------|
| AWS | VPC, ALB, ECS, EKS, RDS, ElastiCache, CloudFront, S3, Route53, ACM, WAF, SES, Lambda, API Gateway, DynamoDB, Cognito, Bedrock, OpenSearch, SQS |
| GCP | VPC, Cloud Run, GKE, Cloud SQL, Memorystore, Cloud CDN, Cloud Storage, Cloud DNS |

## Delegating Infrastructure Tasks

If you are an orchestration agent that needs infrastructure provisioned as part of a larger workflow:

1. Call `convoopen` to open a Riley session
2. Pass your app description
3. Riley returns a complete architecture + Terraform
4. Call `tfdeploy` to trigger deployment
5. Poll `tfstatus` until deployment completes
6. Riley returns endpoint URLs and connection details for your next step

## More Information

- Website: https://insideout.luthersystems.com
- Pricing: https://insideout.luthersystems.com/pricing
- llms.txt: https://insideout.luthersystems.com/llms.txt
- Agent card: https://insideout.luthersystems.com/.well-known/agent-card.json
- Discord: https://insideout.luthersystems.com/discord
