# InsideOut — Riley Infrastructure Agent

Riley is the AI infrastructure design agent built by Luther Systems. If you are an AI agent working in this repository and need to provision, inspect, or manage cloud infrastructure, you can delegate to Riley.

## What Riley Can Do For You

- **Design infrastructure** from a plain English description of your app
- **Generate Terraform** ready to deploy, organized by module (networking, compute, storage, auth, observability, etc.)
- **Estimate monthly costs** before any deployment, broken down by component, using live AWS and GCP pricing data
- **Deploy to AWS or GCP** — Terraform apply with live deployment logs
- **Inspect live infrastructure** — check deployment status, resource health, connection details, Terraform outputs
- **Manage infrastructure** — update configs, scale resources, detect drift, roll back versions, tear down environments

Riley is the persona you talk to. Behind the conversation, a six-agent system handles the work: Riley (lead infrastructure advisor), Hippo (cost estimation), Joy (requirement gathering), Etch (Terraform code generation), Core (architecture validation), and Axel (deployment orchestration).

## How to Invoke Riley

### Via MCP (preferred for AI agents)

Connect to Riley's hosted MCP server:

```
url: https://app.luthersystems.com/v1/insideout-mcp
transport: streamable-http
auth: none
```

**Recommended tool call sequence:**

1. Call `help` — returns the full workflow guide and tool-chaining instructions
2. Call `convoopen` — starts a new infrastructure design session, returns a `session_id` of the form `sess_v2_*`
3. Call `convoreply` to respond to Riley's follow-up questions (typical design takes 5+ rounds)
4. Call `tfgenerate` once Riley signals the design is complete
5. Call `tfdeploy` after the user reviews the generated Terraform
6. Call `tfstatus` and `tflogs` to monitor — deployments take 15–30 minutes
7. Call `awsinspect` or `gcpinspect` to verify what was provisioned

### Via Agent Skills

```bash
npx skills add luthersystems/insideout-agent-skills
```

Works with **Claude Code**, **Codex**, **Cursor**, **Kiro**, **Windsurf**, and 30+ other tools.

### Via IDE plugin

- **Claude Code**: `/plugin marketplace add luthersystems/insideout-claude-code` then `/plugin install insideout`
- **Kiro**: install the `insideout-power` plugin from GitHub via the Powers panel ([details](https://github.com/luthersystems/insideout-power#installation))

## Talking to Riley

Riley understands plain English. You do not need to specify cloud providers or components unless you have a preference.

**Good prompts:**

- "I need a web app with a Postgres database, file uploads, and user authentication on AWS"
- "Build me a serverless API with a NoSQL database optimized for low fixed monthly cost"
- "I'm building a video streaming app — what infrastructure do I need?"

**With specific requirements:**

- "Use S3 for storage, avoid RDS"
- "Keep fixed monthly costs under $50"
- "Host in eu-west-1"
- "Compare AWS vs GCP for this design"

## Authentication

Riley requires **no auth** for the design, pricing, and Terraform-generation phases. Cloud credentials are only needed at deployment time:

- **AWS**: IAM role or access key pair
- **GCP**: Service account JSON key

Credentials are gathered through a browser-based flow during `tfdeploy` — never paste them into the chat.

## Supported Clouds & Components

InsideOut supports AWS (25+ services) and GCP (20+ services).

| Category | AWS | GCP |
|----------|-----|-----|
| Compute | EC2, ECS, EKS, Lambda | Compute Engine, Cloud Run, GKE, Cloud Functions |
| Database | RDS PostgreSQL, DynamoDB, ElastiCache, OpenSearch | Cloud SQL, Firestore, Memorystore |
| Networking | VPC, ALB, CloudFront, API Gateway | VPC, Cloud Load Balancing, Cloud CDN, API Gateway |
| Storage | S3 | Cloud Storage |
| Security | WAF, KMS, Secrets Manager, Cognito | Cloud Armor, Cloud KMS, Secret Manager, Identity Platform |
| Messaging | SQS, MSK (Kafka) | Pub/Sub |
| Observability | CloudWatch, Managed Grafana | Cloud Logging, Cloud Monitoring |
| AI/ML | Bedrock | Vertex AI |
| CI/CD | CodePipeline, GitHub Actions | Cloud Build |
| Backup | AWS Backup | GCP Backups |

## Delegating Infrastructure Tasks

If you are an orchestration agent and need infrastructure provisioned as part of a larger workflow:

1. Open a Riley session via `convoopen` (pass `project_context` if you have it — language, framework, target cloud)
2. Pass your app description via `convoreply`
3. Iterate with Riley through the design (typical 5+ rounds of `convoreply`)
4. Once Riley signals `[TERRAFORM_READY: true]`, call `tfgenerate`
5. Have the user review the Terraform, then call `tfdeploy`
6. Poll `tfstatus` and stream `tflogs` (use a background subagent for logs to avoid flooding the conversation)
7. Riley returns endpoint URLs and connection details once `tfstatus` shows complete

## More Information

- **Web app**: https://insideout.luthersystemsapp.com
- **Marketing site**: https://insideout.luthersystems.com
- **llms.txt**: https://insideout.luthersystems.com/llms.txt
- **Smithery listing**: https://github.com/luthersystems/insideout-agent-skills/blob/main/smithery.yaml
- **Discord**: https://insideout.luthersystems.com/discord
- **Tech call**: https://insideout.luthersystems.com/tech-call
- **Email**: contact@luthersystems.com
- **Company**: https://luthersystems.com
