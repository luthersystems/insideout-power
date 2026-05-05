# InsideOut — AI Infrastructure Design Agent

**Design, price, and deploy production-ready cloud infrastructure through conversational AI.**

Riley, InsideOut's AI infrastructure advisor, takes a plain English description of your app and designs a complete AWS or GCP architecture, generates Terraform, estimates monthly costs, and deploys — all without leaving your IDE.

> 🎬 **[Watch the demo →](https://insideout.luthersystems.com)** <!-- TODO: add direct video link from Dario -->

---

## How do I deploy cloud infrastructure with AI?

Describe your app to Riley in plain English. Riley handles everything else:

1. **Design** — Riley suggests the right components for each feature of your app
2. **Review** — walk through the architecture, ask questions, request changes
3. **Price** — get a monthly cost estimate broken down by component before committing
4. **Configure** — tweak scale, region, and cost strategy
5. **Generate** — Riley produces modular, production-ready Terraform files
6. **Deploy** — connect your cloud credentials and Riley applies the Terraform (10–30 min)
7. **Manage** — inspect resources, check deployment status, update or tear down — all conversationally

No Terraform knowledge required. No DevOps expertise required.

---

## Install

### Kiro IDE

Install the `insideout-power` plugin from the Kiro powers marketplace, or add directly from this repo (`luthersystems/insideout-power`) via Kiro's Powers panel. MCP must be enabled in settings. No API keys required.

### Cursor

Full instructions: **https://insideout.luthersystems.com/cursor**

Add to your Cursor MCP config:

```json
{
  "mcpServers": {
    "insideout": {
      "url": "https://app.luthersystems.com/v1/insideout-mcp"
    }
  }
}
```

### Claude Code

Full instructions: **https://insideout.luthersystems.com/claude-code**

```bash
# Add the marketplace
/plugin marketplace add luthersystems/insideout-claude-code

# Install the plugin
/plugin install insideout

# Start building
/insideout
```

### Agent Skills (Codex, Windsurf, Antigravity, and 30+ tools)

```bash
npx skills add insideout
```

### Any MCP-compatible agent

Connect directly to the MCP server:

```
https://app.luthersystems.com/v1/insideout-mcp
```

### Docker

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

---

## What can Riley design?

Riley supports 50+ services across AWS and GCP:

| Cloud | Components |
|-------|-----------|
| **AWS** | VPC, ALB, ECS, EKS, RDS, ElastiCache, CloudFront, S3, Route53, ACM, WAF, SES, Lambda, API Gateway, DynamoDB, Cognito, Bedrock, OpenSearch, SQS |
| **GCP** | VPC, Cloud Run, GKE, Cloud SQL, Memorystore, Cloud CDN, Cloud Storage, Cloud DNS |

No regional limitations.

---

## Example conversations

**"I'm building an AI-powered note-taking app on AWS"**
→ Riley designs: S3 + DynamoDB (storage), Lambda + API Gateway (compute), Cognito (auth), Bedrock + OpenSearch + SQS (AI layer), CloudFront (delivery). Monthly estimate: ~$130.

**"I need an indie film streaming app with S3 for storage"**
→ Riley designs: S3 + CloudFront (video delivery), DynamoDB + Lambda (metadata), API Gateway, Cognito (auth), WAF (security). Monthly estimate: ~$120.

**"Build me a serverless API with a NoSQL database, keep fixed costs low"**
→ Riley picks on-demand DynamoDB, Lambda, API Gateway. No idle cost. Configures for your traffic estimate.

---

## Frequently asked questions

**Does InsideOut work with Cursor, Claude Code, and Kiro?**
Yes. InsideOut has native plugins for Kiro, Claude Code, and Cursor. It also works with any MCP-compatible agent via the MCP server endpoint, and with 30+ agentic tools via Agent Skills.

**Do I need to know Terraform to use InsideOut?**
No. Riley generates all Terraform for you. You can review the files before deploying, but no Terraform knowledge is required.

**How does InsideOut estimate costs?**
Riley calculates a monthly cost estimate broken down by component and fixed/variable split before any deployment. You can adjust scale, region, and configuration to hit your budget.

**What cloud credentials do I need?**
Design, review, and pricing require no credentials at all. You only need to connect your AWS IAM role or GCP service account at deployment time.

**Can another AI agent call Riley?**
Yes. Any MCP-compatible agent can connect to `https://app.luthersystems.com/v1/insideout-mcp`. Riley supports both multi-turn conversational sessions and single-call transactional mode for orchestration agents that don't maintain long-running sessions.

**How long does deployment take?**
Typically 10–30 minutes depending on the stack.

**What if I want to change my infrastructure after deployment?**
Riley can inspect your live resources, update configuration, and re-deploy. You can also tear down specific resources or the full stack conversationally.

**Is InsideOut open source?**
The IDE plugins and Agent Skills packages are open source under Apache 2.0. The InsideOut backend is proprietary.

---

## For AI agents

InsideOut is designed to be called by other agents. See [AGENTS.md](./AGENTS.md) for the full integration guide.

**Quick start for agents:**

```
MCP endpoint: https://app.luthersystems.com/v1/insideout-mcp

1. help        → get workflow guide
2. convoopen   → start infrastructure design session
3. tfdeploy    → deploy to cloud
4. tfstatus    → check progress, get connection details
```

Agent card: `https://insideout.luthersystems.com/.well-known/agent-card.json`
llms.txt: `https://insideout.luthersystems.com/llms.txt`

---

## Repos

| Repo | What it is |
|------|-----------|
| [luthersystems/insideout-power](https://github.com/luthersystems/insideout-power) | Kiro IDE "power" plugin — surfaces InsideOut inside Kiro via MCP |
| [luthersystems/insideout-claude-code](https://github.com/luthersystems/insideout-claude-code) | Claude Code plugin — MCP tools and `/insideout` slash commands |
| [luthersystems/insideout-agent-skills](https://github.com/luthersystems/insideout-agent-skills) | Agent Skills package — brings InsideOut to Codex, Cursor, Windsurf, Antigravity, and 30+ tools |
| [luthersystems/insideout-terraform-presets](https://github.com/luthersystems/insideout-terraform-presets) | Standard AWS/GCP Terraform module library used by the InsideOut backend |
| [luthersystems/insideout-examples](https://github.com/luthersystems/insideout-examples) | Sample app stacks (cargofit, edubot, guestbook, videostreaming, and more) |

---

## Pricing

InsideOut has unit/tier-based pricing with no usage limits. See **https://insideout.luthersystems.com/pricing**.

---

## Community & support

- 💬 Discord: **https://insideout.luthersystems.com/discord**
- 🌐 Website: **https://insideout.luthersystems.com**
- 🏢 Luther Systems: **https://luthersystems.com**

---

## License

The plugins in this repository are open source under the [Apache 2.0 License](./LICENSE).
