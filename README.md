<p align="center">
  <img src="assets/banner.svg" alt="InsideOut — AI-Powered Cloud Infrastructure Design" width="100%">
</p>

<p align="center">
  <strong>Design, price, and deploy production-ready AWS & GCP infrastructure through conversational AI</strong>
</p>

<p align="center">
  <a href="https://kiro.dev/powers/">Kiro Power</a> &bull;
  <a href="https://insideout.luthersystems.com">InsideOut</a> &bull;
  <a href="https://luthersystems.com">Luther Systems</a>
</p>

---

## What is InsideOut?

InsideOut is a [Kiro IDE](https://kiro.dev) power that brings AI-powered cloud infrastructure design directly into your editor. Describe what you want to build in plain language, and Riley — your AI infrastructure advisor — guides you through selecting services, configuring them, estimating costs, generating Terraform, and deploying to AWS or GCP.

**No authentication or API keys required.** Install the power and start designing.

### What You Can Do

- **Design infrastructure conversationally** — describe your app, get expert recommendations
- **Get real-time cost estimates** — see monthly costs as components are added
- **Generate Terraform** — production-ready, modular code with security best practices
- **Deploy with one command** — deploy directly to AWS or GCP from the conversation
- **Inspect deployments** — verify what was actually provisioned in your cloud account
- **Compare providers** — evaluate AWS vs GCP options side-by-side

### Supported Services

| AWS | GCP |
|-----|-----|
| ECS, EKS, Lambda, Fargate | Cloud Run, GKE, Cloud Functions |
| RDS, DynamoDB, ElastiCache | Cloud SQL, Firestore, Memorystore |
| ALB, CloudFront, Route 53 | Cloud Load Balancing, Cloud CDN |
| S3, EFS | Cloud Storage, Filestore |
| VPC, Security Groups | VPC, Firewall Rules |
| CloudWatch, SNS, SQS | Cloud Monitoring, Pub/Sub |
| And 15+ more | And 10+ more |

## Installation

### From GitHub (recommended)

1. Open Kiro IDE
2. Open the Powers panel
3. Click **Add power from GitHub**
4. Enter: `luthersystems/insideout-power`

### From Local Path (for development)

1. Clone this repo: `git clone https://github.com/luthersystems/insideout-power.git`
2. Open Kiro IDE
3. Open the Powers panel
4. Click **Add power from Local Path**
5. Select the cloned directory

### After Installation

MCP support must be enabled in Kiro:

1. Open Settings (`Cmd + ,` / `Ctrl + ,`)
2. Search for "MCP"
3. Enable the MCP support setting

That's it. No API keys, no local binaries, no additional setup.

## Quick Start

Once installed, mention anything about infrastructure, cloud, AWS, GCP, Terraform, or deployment in a Kiro chat. The power activates automatically.

```
You: "I need to set up cloud infrastructure for a web app"

Kiro: [Activates InsideOut power, calls convoopen]

Riley: "Hi! I'm Riley, your infrastructure advisor. Tell me about the app
        you're building — what does it do, who uses it, and what scale
        are you planning for?"

You: "It's an e-commerce platform expecting 50k monthly users on AWS"

Riley: "Great! I'd recommend ECS Fargate for your containers, RDS PostgreSQL
        for your database, ElastiCache Redis for sessions, and an ALB.
        Estimated cost: ~$350/month. Want me to adjust anything?"

You: "Looks good, generate the Terraform"

Kiro: [Calls tfgenerate — downloads production-ready Terraform files]

You: "Deploy it"

Kiro: [Calls tfdeploy — deploys to AWS, streams logs via tflogs]
```

## Available Tools

| Tool | Description |
|------|-------------|
| `convoopen` | Start a new infrastructure design session |
| `convoreply` | Continue the design conversation with Riley |
| `convoawait` | Wait for long-running operations |
| `convostatus` | View current components, config, and pricing |
| `tfgenerate` | Generate production-ready Terraform files |
| `tfdeploy` | Deploy generated Terraform to AWS or GCP |
| `tfstatus` | Check deployment progress |
| `tflogs` | Stream real-time deployment logs |
| `awsinspect` | Inspect deployed AWS resources |
| `gcpinspect` | Inspect deployed GCP resources |
| `help` | Get workflow guidance |

## Directory Structure

```
insideout-power/
├── POWER.md                    # Power metadata, onboarding, and agent instructions
├── mcp.json                    # MCP server configuration (remote HTTP)
├── README.md                   # This file
├── LICENSE                     # Apache 2.0
├── assets/
│   ├── banner.svg              # GitHub banner
│   └── logo.svg                # InsideOut logo
└── steering/                   # Workflow-specific guidance for the agent
    ├── getting-started.md      # First-time setup walkthrough
    ├── aws-design-patterns.md  # AWS architecture patterns and prompts
    ├── gcp-design-patterns.md  # GCP architecture patterns and prompts
    └── troubleshooting-guide.md
```

## How It Works

InsideOut uses a multi-agent AI system behind a single MCP server:

| Agent | Role |
|-------|------|
| **Riley** | Infrastructure advisor — leads the design conversation |
| **Hippo** | Cost estimation and pricing optimization |
| **Joy** | User experience and requirement gathering |
| **Etch** | Terraform code generation |
| **Core** | Architecture validation and best practices |
| **Axel** | Deployment orchestration |

The conversation flows through these agents automatically. From Kiro's perspective, you're talking to Riley — the other agents work behind the scenes.

## Contributing

1. Fork this repository
2. Create a feature branch: `git checkout -b feature/my-improvement`
3. Make your changes
4. Test locally: Install the power from local path in Kiro
5. Submit a pull request

### Development Tips

- Test POWER.md changes by reinstalling the power from local path
- Verify MCP connectivity with the `help` tool after changes
- Steering files are loaded on-demand — test each workflow independently

## License

[Apache License 2.0](LICENSE)

## Links

- [InsideOut Platform](https://insideout.luthersystems.com)
- [Luther Systems](https://luthersystems.com)
- [Kiro IDE](https://kiro.dev)
- [Kiro Powers Documentation](https://kiro.dev/docs/powers/)
- [MCP Protocol](https://modelcontextprotocol.io)
