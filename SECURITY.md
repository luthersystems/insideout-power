# Security

## Data Flow

InsideOut connects to a hosted MCP server at `https://app.luthersystems.com/v1/insideout-mcp`. Here is what data flows where and why.

### What is sent to the server

Riley is a cloud infrastructure advisor. To recommend the right architecture, she needs general tech stack details — the same information you'd share in the first few minutes of a conversation with a solutions architect. No credentials, secrets, source code, or PII are ever sent.

| Data | When | What it contains | Why Riley needs it |
|------|------|------------------|-------------------|
| **Project context** (optional) | Session start (`convoopen`) | General tech stack summary: language, framework, cloud provider, container/K8s usage, CI/CD platform | Determines compute type, avoids conflicting with existing infrastructure, integrates with existing CI/CD |
| **User messages** | Each conversation turn (`convoreply`) | The user's own words, forwarded as-is | The user is talking to Riley — messages drive the design conversation |
| **Source identifier** | Session start | IDE name ("Kiro") | Included automatically to identify the client platform |

### What is NOT sent

- **Credentials** — AWS/GCP credentials are never sent through the power or MCP server. Cloud authentication uses browser-based OAuth handled directly by the cloud provider.
- **Source code** — The power does not transmit source code. Project context contains only metadata summaries (e.g., "Node.js 20, Next.js 15"), never file contents.
- **Secrets / API keys** — No `.env` files, credential files, or secret store values are read or transmitted.
- **PII** — No personally identifiable information is collected or transmitted.

### Project context

The agent may provide a short project context summary when starting a session. This exists so Riley can make informed architecture decisions (e.g., recommending ECS for a containerized Node.js app, or Cloud Run for a Go service already on GCP) without asking the user a dozen discovery questions.

1. **Confirmed with the user** — The agent shows the summary and asks the user to confirm before sending it.
2. **General metadata only** — Language, framework, cloud provider, container usage, CI/CD platform. A few lines of plain text like "Node.js 20, TypeScript, Next.js 15, AWS, GitHub Actions."
3. **No sensitive content** — Never includes credentials, secrets, PII, source code, or internal URLs.
4. **Optional** — If the user declines or the agent doesn't have enough context, Riley asks discovery questions instead. Project context is a convenience, not a requirement.

### Cloud credentials

When deploying infrastructure, InsideOut uses browser-based authentication:

1. Riley provides a URL for the user to open in their browser.
2. The user authenticates directly with AWS or GCP through their browser.
3. Credentials are managed by the InsideOut platform — they are never passed through the agent or stored by the power.

## Security Audit Notes

Automated security scanners (Snyk, Socket) may flag this power because it:

- Connects to a remote MCP server
- Provides project context to that server
- Can trigger real-world cloud actions (deploy, destroy)

These are fundamental to how InsideOut works — it is a remote infrastructure management tool. The same pattern applies to all remote MCP integrations (Linear, Figma, Sentry, etc.). The flags reflect the trust model of remote MCP integrations generally, not a vulnerability specific to InsideOut.

## Reporting Vulnerabilities

To report a security issue, email security@luthersystems.com.
