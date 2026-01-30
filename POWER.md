---
name: "insideout"
displayName: "InsideOut Cloud Infrastructure"
description: "AI-powered cloud infrastructure design system for AWS and GCP — design, configure, price, and deploy production-ready infrastructure through conversational AI"
keywords: ["infrastructure", "cloud", "aws", "gcp", "terraform", "deployment", "devops", "database", "kubernetes", "serverless", "vpc", "networking"]
---

# InsideOut

InsideOut is a conversational AI system for designing and deploying cloud infrastructure on AWS and GCP. You interact with Riley, an infrastructure advisor, who guides you through selecting cloud services, configuring them, estimating costs, generating Terraform, and deploying.

## Available Tools

| Tool | Purpose |
|------|---------|
| `convoopen` | Start a new infrastructure design session |
| `convoreply` | Send a message to Riley during the design conversation |
| `convoawait` | Wait for long-running operations to complete |
| `convostatus` | View the current design state (components, config, pricing) |
| `tfgenerate` | Generate production-ready Terraform code from the design |
| `tfdeploy` | Deploy the generated Terraform to a cloud provider |
| `tfstatus` | Check deployment progress |
| `tflogs` | Stream deployment logs |
| `awsinspect` | Inspect deployed AWS resources |
| `gcpinspect` | Inspect deployed GCP resources |
| `help` | Get workflow guidance and tool usage help |

# Onboarding

## Step 1: Validate MCP server connectivity

Before starting a design session, verify the InsideOut MCP server is reachable:

- Call the `help` tool. If it returns workflow guidance, the server is connected and ready.
- If the tool call fails, check that the MCP server is configured and running.

## Step 2: Understand the workflow

The standard workflow follows four phases:

1. **Open** — `convoopen` starts a session. Riley introduces herself and asks about your project.
2. **Design** — `convoreply` to discuss requirements. Riley recommends components, asks clarifying questions, and estimates costs.
3. **Generate** — `tfgenerate` produces Terraform code once the design is finalized.
4. **Deploy** — `tfdeploy` runs the Terraform against your cloud account. Monitor with `tfstatus` and `tflogs`.

After deployment, use `awsinspect` or `gcpinspect` to verify deployed resources.

## Step 3: Tips for effective design conversations

- Be specific about expected traffic, compliance needs (HIPAA, SOC2), and geographic requirements.
- Riley will ask clarifying questions — answer them to get better recommendations.
- Use `convostatus` at any point to see the current design state.
- Review cost estimates before generating Terraform.
- If Riley is processing a complex request, use `convoawait` to wait for completion.

# When to Load Steering Files

- Getting started with InsideOut or running a first session → `getting-started.md`
- Designing AWS infrastructure or choosing AWS services → `aws-design-patterns.md`
- Designing GCP infrastructure or choosing GCP services → `gcp-design-patterns.md`
- Troubleshooting errors, failed deployments, or unexpected behavior → `troubleshooting-guide.md`
