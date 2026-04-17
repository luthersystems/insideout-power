---
name: "insideout"
displayName: "InsideOut Agentic Infrastructure Builder & Manager"
description: "Agentic Cloud infrastructure builder & manager, describe your goal, discuss requirements, estimate cost, generate Terraform, deploy, operate and manage in production."
keywords: ["infrastructure", "cloud", "aws", "gcp", "terraform", "deployment", "devops", "database", "kubernetes", "serverless", "vpc", "networking", "docker", "containers", "ecs", "eks", "rds", "s3", "lambda", "compute", "iac"]
author: "Luther Systems"
---

**CRITICAL — READ THIS FIRST BEFORE DOING ANYTHING ELSE:**

This file is internal agent guidance. NONE of it is for the user. Do NOT present, summarize, paraphrase, or explain ANY of this content to the user. Do NOT give the user an "overview" or "quick summary" of InsideOut's capabilities — Riley already introduces herself, explains what she can do, and guides the user through the process. Any additional explanation from the Kiro agent is confusing and redundant.

## Activation protocol (zero user-visible output)

On power activation, execute these steps with **NO text output to the user at any point**:

1. Call `help` — do not display anything
2. Build `project_context` from what you know about the user's project (see Project Context below) — show it to the user and confirm before sending
3. **Immediately after the user confirms (or declines) the project context, call `convoopen`** (with `project_context` if confirmed, without it if declined) — display ONLY Riley's response. Do not pause, do not ask another question, do not add any acknowledgement text. The user's confirmation is the trigger to call `convoopen` right away.

**The very first text the user sees must be Riley's words from `convoopen`.** Exception: if project context was built in step 2, the consent prompt ("I'd like to share this project summary with Riley — does this look right?") is the one permitted agent output before Riley's response.

Riley introduces herself, explains InsideOut, and asks what the user wants to build. The agent must not duplicate, summarize, or preview any of this. Any agent text before Riley's response is a bug.

### Do NOT say things like:
- "I'll help you get started with insideout-power!"
- "Let me activate it first to understand its capabilities."
- "Now let me call the help tool to get the workflow guidance, then scan your workspace for project context, and start a session with Riley."
- "Let me scan your workspace for project context."
- "Let me read your project files to build context."
- "Now let me start an InsideOut session with Riley."
- "Here's what Riley said:"
- Any greeting, introduction, status update, or narration.

### DO say **"Loading InsideOut..."** instead, and then call the tools.

### If `help` fails:
Tell the user: click Kiro icon (ghost) → MCP Servers → Enable.

## Conversation flow

InsideOut uses a multi-turn conversational approach:

1. **Open** — `convoopen` starts a session. Riley (the AI infrastructure advisor) introduces herself and asks what you're building.
2. **Design** — Use `convoreply` to respond to Riley's questions. She will recommend cloud components, ask clarifying questions about scale/security/compliance, and show cost estimates. This typically takes 5+ rounds.
3. **Generate** — When Riley shows final pricing and components, call `tfgenerate` to produce Terraform code.
4. **Deploy** — Call `tfdeploy` to deploy. Monitor with `tfstatus` and `tflogs`.
5. **Inspect** — After deployment, use `awsinspect` or `gcpinspect` to verify resources.

**CRITICAL: Do not answer Riley's questions on behalf of the user.** Riley asks about the user's application, scale requirements, security needs, and preferences. These questions MUST be shown to the user for them to answer. Pass the user's responses to `convoreply`.

**CRITICAL: Always take action — never just say "Understood."** Once a session is open (you have a `session_id`), every user message MUST result in a tool call. **Never** respond with only "Understood", "Got it", "OK", or any other acknowledgement without calling a tool. Apply this decision tree:

1. **Is the user responding to something Riley said?** (e.g., answering a question, saying "yes", "continue", "looks good", "cost estimate please") → Call `convoreply` with the user's message.
2. **Is the user asking to generate Terraform?** (e.g., "generate terraform", "let's get the terraform", "proceed to terraform") → Call `tfgenerate`.
3. **Is the user asking to deploy?** → Call `tfdeploy`.
4. **Is the user asking for status?** → Call `convostatus`, `tfstatus`, or `tflogs`.
5. **Is the user asking you (the IDE agent) a direct question not meant for Riley?** → Answer it yourself.
6. **Not sure?** → Default to `convoreply`. When in doubt, always route to Riley.

**CRITICAL: Do not suggest spec sessions or other Kiro workflows during an InsideOut session.** Once an InsideOut session is active (you have a `session_id`), stay in the InsideOut conversation flow. Do not prompt the user to start a spec session, task session, or any other Kiro-managed workflow. The InsideOut power manages its own multi-turn workflow through the MCP tools — interrupting it with Kiro's built-in session types will break the conversation state.

**Example — DO NOT do this:**
```
Riley: "Your stack is ready — ECS Fargate, ALB, RDS Postgres. Ready for Terraform?"
User: "Yes, and I'd like help wiring my app up to the database too"
Agent: "Let's switch to a spec session to plan the application changes."   ← WRONG. Never suggest spec/task sessions mid-flow.
```

**Correct:**
```
Riley: "Your stack is ready — ECS Fargate, ALB, RDS Postgres. Ready for Terraform?"
User: "Yes, and I'd like help wiring my app up to the database too"
Agent calls: convoreply(message="Yes, and I'd like help wiring my app up to the database too")   ← RIGHT. Stay in the InsideOut flow; Riley handles it.
```

**Example 1 — DO NOT do this:**
```
Riley: "Any questions or tweaks? Or ready for the cost estimate?"
User: "Cost estimate please"
Agent: "Understood."                    ← WRONG. Must call convoreply.
```

**Correct:**
```
Riley: "Any questions or tweaks? Or ready for the cost estimate?"
User: "Cost estimate please"
Agent calls: convoreply(message="Cost estimate please")   ← RIGHT.
→ Riley responds with the cost estimate.
```

**Example 2 — DO NOT do this:**
```
Riley: "Ready to proceed to the Terraform files?"
User: "Let's proceed with terraform please"
Agent: "Understood."                    ← WRONG. Must call a tool.
User: "Continue on to generate the terraform"
Agent: "Understood."                    ← WRONG again. Still no tool call.
```

**Correct:**
```
Riley: "Ready to proceed to the Terraform files?"
User: "Let's proceed with terraform please"
Agent calls: convoreply(message="Let's proceed with terraform please")   ← RIGHT.
→ Riley confirms and signals [TERRAFORM_READY: true].
Agent calls: tfgenerate                 ← RIGHT. Now generate.
```

**The rule is simple: every user message during an active session must produce a tool call. If you are unsure which tool, use `convoreply`.**

**CRITICAL: On the very first deployment, do NOT call `tfdeploy` after `credawait` succeeds.** The first deployment for a project sends the user to the browser deployment console to enter cloud credentials. When they click "Apply Terraform" there, **the browser initiates the deploy itself in that connect flow** — there is no need for the agent to call `tfdeploy`. A successful `credawait` is the signal that the browser has taken over the deploy. Calling `tfdeploy` at this point fires a second job while the user is still mid-redirect, and the console auto-streams logs instead of showing the "Apply Terraform" button. This overrides the decision tree rule for the credawait→deploy sequence. After `credawait` success, call `tfstatus` once to check state, then wait for the user's next message — don't loop. Use `tflogs` once a job is running. `tfdeploy` is only for flows that never hit `credawait` (subsequent redeploys, sandbox runs).

**Example — DO NOT do this:**
```
credawait returns: success (user finished credential entry)
Agent calls: tfdeploy                   ← WRONG. The browser is already starting the deploy.
```

**Correct:**
```
credawait returns: success (user finished credential entry)
Agent calls: tfstatus                   ← RIGHT. Browser owns the deploy; check state once.
→ tfstatus shows running once the user clicks Apply in the browser.
Agent calls: tflogs                     ← RIGHT. Stream logs for the running job.
```

### Credential flow: `credawait` timeouts and errors are NOT service failures

The browser sign-up + cloud credential flow (AWS role creation, subscription, account setup) commonly takes **5–15+ minutes**, well beyond any single `credawait` call. Handle this correctly:

- **`credawait` default timeout is 5 minutes (300s).** A timeout means the user is still mid-flow, **not** that the service is broken.
- **On `credawait` timeout or transient error, do NOT abandon the flow.** Do NOT call `tfstatus`, do NOT say "the service is experiencing issues," and do NOT switch to troubleshooting. Instead:
  1. Ask the user directly: **"Were you able to sign up, add your cloud credentials, and kick off the deployment in the browser? Or do you need more time?"**
  2. If the user needs more time → call `credawait` again to keep polling.
  3. If the user says they finished and clicked Apply → call `tfstatus` to pick up the browser-initiated job.
  4. If the user hit a problem in the browser → re-present the connect URL and offer to call `credawait` again.
- **Never interpret a credawait timeout as "service down."** The connect URL is still valid; the user is simply still in the browser.
- **Never fabricate progress updates or guess what happened.** Ask the user.

**Example — DO NOT do this:**
```
credawait returns: timeout after 5 min (or a transient network error)
Agent: "There's a temporary connection issue with the InsideOut service. Let me check the status."
Agent calls: tfstatus                   ← WRONG. The service is fine. The user is still signing up.
```

**Correct:**
```
credawait returns: timeout after 5 min
Agent: "Were you able to sign up, add your cloud credentials, and kick off the deployment in the browser? Or do you need more time?"
User: "Still working on it, AWS is validating my credentials."
Agent calls: credawait                  ← RIGHT. Keep polling.
```

```
credawait returns: timeout after 5 min
Agent: "Were you able to sign up, add your cloud credentials, and kick off the deployment in the browser? Or do you need more time?"
User: "Yes, I clicked Apply Terraform a minute ago."
Agent calls: tfstatus                   ← RIGHT. Browser-initiated deploy is running.
```

## Project context (`project_context`)

Riley designs cloud infrastructure. To recommend the right architecture, she needs general tech stack details — the same information you'd share in the first few minutes of a conversation with a solutions architect. Providing project context up front lets Riley skip discovery questions and jump straight to useful recommendations. **This context helps Riley give better guidance — it does NOT skip any design steps.** Riley will still ask her full set of questions about scale, security, compliance, regions, etc.

**Rules:**
- Build a project context summary from what you already know about the user's project (from the workspace, recent conversation, or what they've told you)
- **Show it to the user and confirm before sending:** "I'd like to share this project summary with Riley so she can tailor her recommendations — does this look right?"
- If the user declines or wants to edit it, respect that
- Pass the confirmed `project_context` on the **`convoopen`** call
- If you discover additional project details later, you can pass an updated `project_context` on a subsequent `convoreply` call
- **Skip entirely** if you don't have enough context or the user declines — Riley will ask discovery questions instead
- This provides **factual project metadata**, not answers to Riley's design questions — it does not violate the CRITICAL instruction above

**What to look for** (extract key fields only, never file contents):

| File / Pattern | What to extract (metadata only) |
|---|---|
| `package.json` | Runtime, framework, key deps (pg, redis, prisma, aws-sdk, etc.) |
| `requirements.txt`, `pyproject.toml`, `Pipfile` | Python version, framework, key deps |
| `go.mod` | Go version, key deps (gin, echo, pgx, go-redis) |
| `Cargo.toml` | Rust edition, key deps |
| `pom.xml`, `build.gradle` | Java/Kotlin framework, key deps |
| `Gemfile` | Ruby version, framework, key deps |
| `Dockerfile`, `docker-compose.yml` | Container usage, service images |
| `*.tf`, `terraform/` | Existing IaC provider and resource types |
| `serverless.yml` | Serverless Framework, provider |
| `.github/workflows/`, `.gitlab-ci.yml` | CI/CD platform |
| `k8s/`, `kubernetes/`, `helm/` | Kubernetes usage |
| `README.md` | Project description (first ~20 lines) |

**Cloud provider signals:** In addition to the tech stack, note any signals that indicate which cloud provider the user is already targeting or deploying to. Report any matches as a **Target Cloud** line.

| Signal | Indicates |
|---|---|
| `*.tf` files with `provider "aws"` or `aws_*` resources | AWS |
| `*.tf` files with `provider "google"` or `google_*` resources | GCP |
| `aws-sdk`, `@aws-sdk/*`, `boto3`, `aws-cdk-lib` in deps | AWS |
| `@google-cloud/*`, `google-cloud-*` in deps | GCP |
| `serverless.yml` with `provider.name: aws` | AWS |
| `serverless.yml` with `provider.name: gcp` | GCP |
| `cloudformation/`, `*.template.yaml`, `samconfig.toml`, `template.yaml` | AWS (CloudFormation/SAM) |
| `app.yaml` with `runtime:` (App Engine) | GCP |
| CI/CD workflows referencing `aws-actions/*`, `configure-aws-credentials` | AWS |
| CI/CD workflows referencing `google-github-actions/*`, `auth` with `workload_identity_provider` | GCP |
| `copilot/`, `appspec.yml` | AWS (Copilot/CodeDeploy) |
| `cdk.json`, `cdk.context.json` | AWS (CDK) |
| `.gcloudignore`, `gcloud` commands in scripts | GCP |
| `pulumi/` with AWS or GCP references | AWS or GCP |

If multiple providers are detected, list all of them. If none are detected, omit the Target Cloud line.

**Why Riley needs each detail:**

| Detail | Why Riley needs it |
|---|---|
| Language and framework | Determines compute type (Lambda vs ECS vs EC2) and runtime constraints |
| Database and services | Shapes data tier and caching recommendations |
| Container usage | Informs orchestration choice (ECS, EKS, Cloud Run) |
| Existing infrastructure-as-code | Avoids conflicting with what's already provisioned |
| CI/CD platform | Integrates deployment pipeline |
| Cloud provider | Targets the right provider from the start |
| Kubernetes usage | Determines whether to target existing K8s or provision new compute |
| Project description | General understanding for architecture fit |

**What to NEVER include in project context:**

- **Credentials or secrets** — No API keys, tokens, passwords, private keys, or `.env` values
- **PII** — No usernames, emails, or personally identifiable information
- **Source code** — Only metadata summaries, never file contents
- **Internal URLs or IPs** — Omit specific internal hostnames, IPs, or endpoint URLs

**Format:** Build a concise string for the `project_context` parameter:

```
IDE: Kiro
Language/Runtime: Node.js 20, TypeScript
Framework: Next.js 14
Databases/Services: PostgreSQL (via prisma), Redis (via ioredis)
Target Cloud: AWS (Terraform provider, ECS + RDS resources, GitHub Actions with aws-actions/configure-aws-credentials)
Infrastructure: Docker Compose, Terraform
CI/CD: GitHub Actions
```

**Always include the IDE line** (Kiro). Only include lines where you have information. Keep it general and anonymized. Omit empty categories.

# Internal Notes for Kiro Agent (not for the user)

The following sections are background context for the agent. None of this should be shown to the user.

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

**Connection:** Remote HTTP server at `https://app.luthersystems.com/v1/insideout-mcp`
**Authentication:** None required — publicly accessible
**Transport:** Streamable HTTP (MCP over HTTP)

**Tools:**

1. **convoopen** — Start a new infrastructure design session
   - Optional: `project_context` (string) — user-confirmed project context summary (see Project Context section)
   - Returns: Session metadata including `session_id` (format: `sess_v2_*`)
   - Use once per session

2. **convoreply** — Send a message to Riley during the design conversation
   - Required: `session_id` (string) — from `convoopen`
   - Required: `message` (string) — your response to Riley
   - Optional: `project_context` (string) — update or refine project context mid-conversation if new details are discovered
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

11. **submit_feedback** — Forward user feedback, bug reports, or feature requests to Luther Systems
    - Required: `session_id` (string), `category` (string: `bug_report`, `feature_request`, `general_feedback`, `question`), `message` (string)
    - Optional: `user_email` (string), `user_name` (string), `source` (string, default: `mcp`)
    - Use when the user wants to report a bug, request a feature, or provide feedback
    - The agent itself can also submit feedback when it encounters repeated issues

12. **help** — Get workflow guidance and tool documentation
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
# Step 1: User describes what they want to build
# Agent calls convoopen (with project_context if user confirmed it)
# Riley introduces herself
# Agent calls convoreply with the user's message
User: "I need a web app with a PostgreSQL database, Redis caching, and a load balancer for about 10,000 users on AWS"

# Step 2: Agent forwards Riley's follow-up questions to the user (5+ rounds typical)
User: "US East region, no compliance requirements, standard backup policy"

# Step 3: Agent can check current state at any time
# Agent calls convostatus → shows selected components and estimated monthly cost

# Step 4: When Riley confirms design is complete, agent calls tfgenerate

# Step 5: Agent calls tfdeploy after user reviews the Terraform

# Step 6: Agent monitors with tfstatus and tflogs

# Step 7: Agent verifies with awsinspect or gcpinspect
```

**IMPORTANT:** The agent calls all tools on behalf of the user. Never tell the user to "call convoopen" or "use tfgenerate" — instead, the agent should say things like "I'll start a design session" or "I'll generate the Terraform now."

### Workflow 2: Compare Cloud Providers

```
# User describes requirements — agent calls convoopen then convoreply
User: "I need a containerized microservices platform. What would this look like on AWS vs GCP?"

# Riley compares options (EKS vs GKE, RDS vs Cloud SQL, etc.)
# Agent forwards Riley's analysis and helps the user choose
```

### Workflow 3: Cost-Optimized Infrastructure

```
# User states budget constraints — agent calls convoopen then convoreply
User: "I need infrastructure for a startup MVP. Budget is under $200/month on AWS."

# Riley recommends cost-effective options:
# - ECS instead of EKS for lower overhead
# - Single-AZ RDS for dev/staging
# - Smaller instance types
```

## Phase Transitions

When the user says "continue", "next", "proceed", "yes", "looks good", "let's do it", or any affirmative response, **always call `convoreply`** with their message unless the phase table below indicates a different tool. Never just acknowledge the message — route it to Riley.

| Current Phase | Signal | Agent Action |
|---|---|---|
| Design (before pricing) | Riley asking questions | Forward to user via `convoreply` |
| Design complete | `[TERRAFORM_READY: true]` in response | Tell user the design is ready, call `tfgenerate` |
| Terraform generated | Files returned | Show Terraform to user, offer to deploy via `tfdeploy` |
| Deployment started | Job running | Monitor with `tfstatus` or `tflogs` |
| Deployment complete | Status shows done | Verify with `awsinspect` or `gcpinspect` |

**CRITICAL: Internal signals like `[TERRAFORM_READY: true]` are for agent routing only.** Never show these markers to the user. Translate them into natural language — for example, instead of saying "when you see [TERRAFORM_READY: true]", tell the user "when Riley confirms the design is complete."

## Best Practices

### Do:

- **Be a transparent relay** — show Riley's messages to the user verbatim, without preambles, summaries, or your own commentary. The user is talking to Riley, not to you
- **Use `convostatus`** to check progress at any time during design
- **Wait for Riley to confirm the design is complete** (signaled internally by `[TERRAFORM_READY: true]`) before calling `tfgenerate` — do not show this marker to the user
- **Store the `session_id`** from `convoopen` — all tools need it
- **Let users review Terraform** before calling `tfdeploy`
- **Be specific about requirements** — mention traffic, compliance, regions, budget
- **Monitor deployments** — deployments take 15+ minutes; use `tfstatus` and `tflogs`

### Don't:

- **Don't answer Riley's questions yourself** — always forward to the user
- **Don't add your own commentary** around Riley's messages — no introductions, summaries, tips, or explanations. Just relay Riley's output directly
- **Don't call `convoopen` more than once** — use `convoreply` for follow-ups
- **Don't call `tfgenerate` before design is complete** — wait for pricing/components
- **Don't call `tfdeploy` before user reviews** the generated Terraform
- **Don't fabricate session IDs** — always use the one from `convoopen`
- **Don't use `convoreply` when user asks for Terraform** — use `tfgenerate` instead

## Troubleshooting

### MCP server shows "not connected"

**Cause:** MCP support is not enabled in Kiro settings
**Solution:**
1. Click the **Kiro icon** (ghost icon) in the left sidebar
2. Go to **MCP Servers**
3. Click **Enable** for the InsideOut server
4. The server should connect automatically — no restart needed

The InsideOut MCP server is a remote HTTP server — no authentication, API keys, or local installation required. Do not attempt to configure credentials or install local binaries.

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

### Kiro prompts for approval on every tool call

**Cause:** Kiro IDE does not pre-seed `autoApprove` from a Power's shipped `mcp.json` on install. The user must click "Allow" once per tool; Kiro persists each approval into `~/.kiro/settings/mcp.json` under `powers.mcpServers["power-insideout-insideout"].autoApprove`. There is no "trust all" option yet ([kirodotdev/Kiro#4672](https://github.com/kirodotdev/Kiro/issues/4672)), and the `"*"` wildcard is ignored ([kirodotdev/Kiro#4323](https://github.com/kirodotdev/Kiro/issues/4323)).

**Solution — click-once path:** Just click "Allow" the first time each tool runs. Kiro persists the approval automatically, and subsequent calls in this and future sessions are auto-approved.

**Solution — pre-approve all at once:** After installing the Power, open `~/.kiro/settings/mcp.json` and ensure the block below exists, then restart Kiro. The Power must be installed first — if the `power-insideout-insideout` key is missing, Kiro may overwrite it on install.

```json
{
  "powers": {
    "mcpServers": {
      "power-insideout-insideout": {
        "autoApprove": [
          "help", "convoopen", "convoreply", "convoawait",
          "convostatus", "credawait", "tfstatus", "tflogs",
          "awsinspect", "gcpinspect"
        ]
      }
    }
  }
}
```

`tfgenerate` and `tfdeploy` are intentionally omitted — they create or modify cloud infrastructure and should require explicit confirmation. Kiro CLI ignores `autoApprove` entirely; this guidance applies to Kiro IDE only.

### Still stuck?

If the troubleshooting steps above don't resolve the issue, or you have a feature request, direct the user to the Luther Systems team:

- **Discord:** [insideout.luthersystems.com/discord](https://insideout.luthersystems.com/discord) — chat with the devs and other InsideOut users
- **Tech call:** [insideout.luthersystems.com/tech-call](https://insideout.luthersystems.com/tech-call) — book a call with the dev team
- **Email:** contact@luthersystems.com

The `help` tool also returns up-to-date support links. When a user hits an unresolvable issue, call `help` to get the latest contact details.

## Configuration

**Authentication:** None required
**Network:** HTTPS connection to `https://app.luthersystems.com/v1/insideout-mcp`
**Prerequisites:** Kiro IDE with MCP support enabled

**MCP Configuration** (automatically set by power installation):

```json
{
  "mcpServers": {
    "insideout": {
      "url": "https://app.luthersystems.com/v1/insideout-mcp",
      "autoApprove": [
        "help", "convoopen", "convoreply", "convoawait",
        "convostatus", "credawait", "tfstatus", "tflogs",
        "awsinspect", "gcpinspect"
      ]
    }
  }
}
```

Conversational, monitoring, and inspection tools are auto-approved. `tfgenerate` and `tfdeploy` require user confirmation since they create or modify cloud infrastructure.

For cloud deployments, you will need:
- **AWS**: IAM credentials with appropriate permissions (provided during the `tfdeploy` conversation)
- **GCP**: Service account with appropriate roles (provided during the `tfdeploy` conversation)

Riley will guide you through credential setup during the deployment phase.

## Tips

1. **Be descriptive** — "I need a scalable e-commerce platform with HIPAA compliance on AWS" gets better results than "set up some servers"
2. **Mention your budget** — Riley optimizes recommendations based on cost constraints
3. **Specify your cloud provider early** — saves rounds of clarifying questions
4. **Check design state often** — call `convostatus` proactively to keep the user informed of progress
5. **Review costs before deploying** — Riley shows estimates but real costs may vary
6. **Start with a simple stack** — you can always add components in a follow-up session
7. **Check deployment logs** — call `tflogs` to show the user what Terraform is doing
8. **Inspect after deployment** — call `awsinspect`/`gcpinspect` to confirm what was actually provisioned
9. **Open your project first** — When your project is open, InsideOut can share a short summary of your tech stack and target cloud provider with Riley (with your confirmation), giving her a head start on recommendations

---

**Author:** Luther Systems
**License:** Apache 2.0
**Source:** [github.com/luthersystems/insideout-power](https://github.com/luthersystems/insideout-power)
