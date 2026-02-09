---
name: "insideout"
displayName: "InsideOut Agentic Infrastructure Builder & Manager"
description: "Agentic Cloud infrastructure builder & manager, describe your goal, discuss requirements, estimate cost, generate Terraform, deploy, operate and manage in production."
keywords: ["infrastructure", "cloud", "aws", "gcp", "terraform", "deployment", "devops", "database", "kubernetes", "serverless", "vpc", "networking", "docker", "containers", "ecs", "eks", "rds", "s3", "lambda", "compute", "iac"]
author: "Luther Systems"
---

# Onboarding

## Step 1: Verify MCP server connectivity

The InsideOut MCP server is a remote HTTP server. **No authentication, API keys, or local installation is required.** The server is publicly accessible.

**IMPORTANT:** MCP support must be enabled in Kiro for the server to connect:
1. Open Settings: `Cmd + ,` (Mac) or `Ctrl + ,` (Windows/Linux)
2. Search for "MCP"
3. Enable the MCP support setting
4. Return to this chat — the server should now be connected

To verify connectivity, call the `help` tool. If it returns a workflow guide, the server is ready. If it fails, check that MCP support is enabled in Settings (step above).

**Do NOT** attempt to:
- Look for local binaries or install anything
- Configure API keys or authentication
- Read or modify `.kiro/settings/mcp.json` — Kiro manages this automatically when the power is installed
- Troubleshoot network or auth issues — the server is public and requires no credentials

## Step 2: Start a design session

Once connectivity is verified, use `convoopen` to start a new session. The tool returns a `session_id` (format: `sess_v2_*`). **Store this session_id** — every subsequent tool call requires it.

## Step 3: Understand the conversation flow

InsideOut uses a multi-turn conversational approach:

1. **Open** — `convoopen` starts a session. Riley (the AI infrastructure advisor) introduces herself and asks what you're building.
2. **Design** — Use `convoreply` to respond to Riley's questions. She will recommend cloud components, ask clarifying questions about scale/security/compliance, and show cost estimates. This typically takes 5+ rounds.
3. **Generate** — When Riley shows final pricing and components, call `tfgenerate` to produce Terraform code.
4. **Deploy** — Call `tfdeploy` to deploy. Monitor with `tfstatus` and `tflogs`.
5. **Inspect** — After deployment, use `awsinspect` or `gcpinspect` to verify resources.

**CRITICAL: Do not answer Riley's questions on behalf of the user.** Riley asks about the user's application, scale requirements, security needs, and preferences. These questions MUST be shown to the user for them to answer. Pass the user's responses to `convoreply`.

# Overview

InsideOut is an AI-powered cloud infrastructure design system built by Luther Systems. It transforms the complex process of infrastructure provisioning into a natural conversation. Describe what you want to build, and Riley guides you through selecting services, configuring them, estimating costs, generating Terraform, and deploying — all within your IDE.

**Supported cloud providers:** AWS (25+ services) and GCP (20+ services)

**Key capabilities:**
- **Conversational Design**: Describe your app in plain language, get expert infrastructure recommendations
- **Real-Time Cost Estimation**: See monthly cost estimates as components are added
- **Terraform Generation**: Production-ready, modular Terraform code with security best practices
- **One-Command Deployment**: Deploy directly to AWS or GCP from the conversation
- **Post-Deployment Inspection**: Verify deployed resources and configurations
- **Multi-Agent AI**: Six specialized AI agents (Riley, Hippo, Joy, Etch, Core, Axel) collaborate on design decisions

## Available MCP Servers

### insideout

**Connection:** Remote HTTP server at `https://app.platform.luthersystemsapp.com/insideout-mcp`
**Authentication:** None required — publicly accessible
**Transport:** Streamable HTTP (MCP over HTTP)

**Tools:**

1. **convoopen** — Start a new infrastructure design session
   - Parameters: none
   - Returns: Session metadata including `session_id` (format: `sess_v2_*`)
   - Use once per session

2. **convoreply** — Send a message to Riley during the design conversation
   - Required: `session_id` (string) — from `convoopen`
   - Required: `message` (string) — your response to Riley
   - Returns: Riley's next message with recommendations, questions, or status

3. **convoawait** — Wait for long-running operations to complete
   - Required: `session_id` (string)
   - Returns: Updated session state
   - Use when Riley is processing a complex request

4. **convostatus** — View current design state (components, config, pricing)
   - Required: `session_id` (string)
   - Returns: Current stack summary, pricing, and phase indicators

5. **tfgenerate** — Generate production-ready Terraform files
   - Required: `session_id` (string)
   - Returns: Generated Terraform code
   - Use when conversation shows final pricing and `[TERRAFORM_READY: true]`

6. **tfdeploy** — Deploy generated Terraform to the cloud
   - Required: `session_id` (string)
   - Returns: Deployment job status
   - Duration: 15+ minutes for full deployments

7. **tfstatus** — Check deployment progress
   - Required: `session_id` (string)
   - Returns: Status (running/done/error)

8. **tflogs** — Stream real-time deployment logs
   - Required: `session_id` (string)
   - Returns: Live log output

9. **awsinspect** — Inspect deployed AWS resources
   - Required: `session_id` (string)
   - Returns: Deployed resource details and configurations

10. **gcpinspect** — Inspect deployed GCP resources
    - Required: `session_id` (string)
    - Returns: Deployed resource details and configurations

11. **help** — Get workflow guidance and tool documentation
    - Parameters: none
    - Returns: Complete workflow guide

## When to Load Steering Files

- Getting started or first-time setup → `getting-started.md`
- Designing AWS infrastructure or choosing AWS services → `aws-design-patterns.md`
- Designing GCP infrastructure or choosing GCP services → `gcp-design-patterns.md`
- Troubleshooting errors, failed deployments, or unexpected behavior → `troubleshooting-guide.md`

## Common Workflows

### Workflow 1: Design and Deploy a Web Application

```
# Step 1: Start session
convoopen
→ Riley: "Tell me about the app you're building"

# Step 2: Describe requirements (show Riley's questions to the user)
convoreply: "I need a web app with a PostgreSQL database, Redis caching, and a load balancer for about 10,000 users on AWS"

# Step 3: Answer Riley's follow-up questions (5+ rounds typical)
convoreply: "US East region, no compliance requirements, standard backup policy"

# Step 4: Check current state
convostatus
→ Shows selected components and estimated monthly cost

# Step 5: Generate Terraform when design is complete
tfgenerate

# Step 6: Deploy
tfdeploy

# Step 7: Monitor
tfstatus
tflogs

# Step 8: Verify
awsinspect
```

### Workflow 2: Compare Cloud Providers

```
# Start a session and describe requirements
convoopen
convoreply: "I need a containerized microservices platform. What would this look like on AWS vs GCP?"

# Riley will compare options (EKS vs GKE, RDS vs Cloud SQL, etc.)
# and help you choose based on your constraints
```

### Workflow 3: Cost-Optimized Infrastructure

```
convoopen
convoreply: "I need infrastructure for a startup MVP. Budget is under $200/month on AWS."

# Riley will recommend cost-effective options:
# - ECS Fargate instead of EKS for lower overhead
# - Single-AZ RDS for dev/staging
# - Smaller instance types
```

## Phase Transitions

When the user says "continue", "next", or "proceed", determine the current phase:

| Current Phase | Signal | Next Action |
|---|---|---|
| Design (before pricing) | Riley asking questions | Continue with `convoreply` |
| Design complete | `[TERRAFORM_READY: true]` in response | Suggest `tfgenerate` |
| Terraform generated | Files returned | Suggest `tfdeploy` |
| Deployment started | Job running | Use `tfstatus` or `tflogs` |
| Deployment complete | Status shows done | Use `awsinspect` or `gcpinspect` |

## Best Practices

### Do:

- **Show Riley's messages to the user verbatim** — Riley's questions are meant for the human
- **Use `convostatus`** to check progress at any time during design
- **Wait for `[TERRAFORM_READY: true]`** before calling `tfgenerate`
- **Store the `session_id`** from `convoopen` — all tools need it
- **Let users review Terraform** before calling `tfdeploy`
- **Be specific about requirements** — mention traffic, compliance, regions, budget
- **Monitor deployments** — deployments take 15+ minutes; use `tfstatus` and `tflogs`

### Don't:

- **Don't answer Riley's questions yourself** — always forward to the user
- **Don't call `convoopen` more than once** — use `convoreply` for follow-ups
- **Don't call `tfgenerate` before design is complete** — wait for pricing/components
- **Don't call `tfdeploy` before user reviews** the generated Terraform
- **Don't fabricate session IDs** — always use the one from `convoopen`
- **Don't use `convoreply` when user asks for Terraform** — use `tfgenerate` instead

## Troubleshooting

### MCP server shows "not connected"

**Cause:** MCP support is not enabled in Kiro settings
**Solution:**
1. Open Settings (`Cmd + ,` or `Ctrl + ,`)
2. Search for "MCP"
3. Enable the MCP support setting
4. The server should connect automatically — no restart needed

This is the most common issue. The server requires no authentication or API keys.

### Tool calls return no response

**Cause:** MCP server connection dropped
**Solution:**
1. Open the Kiro panel → MCP servers tab
2. Find `power-insideout-insideout`
3. Click reconnect
4. Retry the tool call

### "Invalid session_id" error

**Cause:** Using a wrong or expired session ID
**Solution:**
1. Session IDs must start with `sess_v2_` (e.g., `sess_v2_0oezGjrn9xEz`)
2. Get a fresh session ID by calling `convoopen`
3. Never fabricate or guess session IDs

### `tfgenerate` returns empty or fails

**Cause:** Design conversation not complete — Riley hasn't finished recommending components
**Solution:**
1. Call `convostatus` to check if `terraform_ready` is true
2. If not, continue the conversation with `convoreply`
3. Wait until Riley shows final components and pricing before generating

### Deployment takes too long

**Cause:** Terraform deployments are long-running (15-30 minutes is normal)
**Solution:**
1. Use `tfstatus` for a quick check (running/done/error)
2. Use `tflogs` to stream real-time logs
3. Large stacks (EKS, multi-region) can take 30+ minutes

### "No deployment found" error

**Cause:** `tfdeploy` was not called, or deployment hasn't started yet
**Solution:**
1. Call `tfdeploy` first to start the deployment
2. Then use `tfstatus` or `tflogs` to monitor

## Configuration

**Authentication:** None required
**Network:** HTTPS connection to `https://app.platform.luthersystemsapp.com/insideout-mcp`
**Prerequisites:** Kiro IDE with MCP support enabled

**MCP Configuration** (automatically set by power installation):

```json
{
  "mcpServers": {
    "insideout": {
      "url": "https://app.platform.luthersystemsapp.com/insideout-mcp"
    }
  }
}
```

For cloud deployments, you will need:
- **AWS**: IAM credentials with appropriate permissions (provided during the `tfdeploy` conversation)
- **GCP**: Service account with appropriate roles (provided during the `tfdeploy` conversation)

Riley will guide you through credential setup during the deployment phase.

## Tips

1. **Be descriptive** — "I need a scalable e-commerce platform with HIPAA compliance on AWS" gets better results than "set up some servers"
2. **Mention your budget** — Riley optimizes recommendations based on cost constraints
3. **Specify your cloud provider early** — saves rounds of clarifying questions
4. **Use `convostatus` liberally** — it's a free check on your current design state
5. **Review costs before deploying** — Riley shows estimates but real costs may vary
6. **Start with a simple stack** — you can always add components in a follow-up session
7. **Check deployment logs** — `tflogs` shows exactly what Terraform is doing
8. **Inspect after deployment** — `awsinspect`/`gcpinspect` confirms what was actually provisioned

---

**Author:** Luther Systems
**License:** Apache 2.0
**Source:** [github.com/luthersystems/insideout-power](https://github.com/luthersystems/insideout-power)
