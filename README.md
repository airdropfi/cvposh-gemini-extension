# CVPosh for Gemini CLI

Work on the resumes you built at [CVPosh](https://cvposh.com) without copy and paste, from Gemini CLI. The extension connects Gemini to the CVPosh MCP server and adds three slash commands and a context file. Everything is read-only: Gemini can read your CVs, score them for applicant tracking systems (ATS) and compare them with a job posting, but it cannot change, delete or share them.

## At a glance

- **What it is:** CVPosh is a resume and CV builder with an MCP server built for AI agents: your agent reads your saved CVs, scores them for ATS compatibility and compares them with a job posting, and you review and edit the result.
- **Who uses it:** an AI assistant or agent (ChatGPT, Claude, Claude Code, Cursor, Gemini CLI, other MCP clients) working for a job seeker or career coach. The human tags the work, approves the connection and reviews the output.
- **What it does not do:** every tool is read-only, so an agent cannot create, edit, delete, export or share a CV. The ATS score is a fixed checklist, not a simulation of any employer's system, and it does not predict interviews or hiring.
- **Price:** the connector needs a free CVPosh account and is not tied to a paid plan. CVPosh Pro is $5 per month or $9 once and covers DOCX export, more PDF exports, all 25 templates and AI job tailoring in the editor.
- **Data and control:** the ATS and keyword checks run on CVPosh servers with fixed rules, call no AI model and store nothing. You approve the connection on a consent page, see it under Settings > API keys, and can delete it at any time; connections expire after 90 days. Profile photos are never sent.
- **MCP:** `https://cvposh.com/api/mcp` (Streamable HTTP). Official MCP Registry name: `com.cvposh/cvposh`. Server card: https://cvposh.com/.well-known/mcp/server-card.json
- **Limits per connection:** 120 requests a minute, 100 tool calls a day.

## Install in 60 seconds

```bash
gemini extensions install https://github.com/airdropfi/cvposh-gemini-extension
```

Restart Gemini CLI, run `/mcp auth cvposh`, sign in to CVPosh in the browser and click Allow on the consent page.

## Prompts to copy

You assign the work and review the result:

- "Assign to CVPosh: list my saved CVs, run the ATS check on the newest one and show me the issues in priority order. Change nothing; I will make the edits and review each one."
- "Use CVPosh to compare my CV with the job posting below. Show matched and missing keywords and quote where each comes from, so I can approve what to add. [paste posting]"
- "Read my CV with CVPosh and draft three stronger bullets for my most recent job. Show each next to the original; I approve before anything goes into my CV."

More prompts, the agent quickstart, error handling and the human-oversight controls: https://cvposh.com/mcp (markdown: https://cvposh.com/mcp.md)

## Before you start

- **You need a CVPosh account** (https://cvposh.com). The extension is a connector, not a resume builder.
- You need Gemini CLI. A browser must be available once, for sign-in.
- A resume is personal data. The tools only read **your own** CVs, as the signed-in user. Profile photos are never returned.

## Install

```bash
gemini extensions install https://github.com/airdropfi/cvposh-gemini-extension
```

Restart Gemini CLI, then sign in:

```text
/mcp auth cvposh
```

A browser window opens: sign in to CVPosh and click Allow. Sign-in is OAuth 2.1 with PKCE and dynamic client registration, discovered from the server, so there is no key to paste. Check it with `/mcp` (the `cvposh` server should list four tools) and try `/cvposh:cvs`.

Update later with `gemini extensions update cvposh`; remove with `gemini extensions uninstall cvposh`.

### Antigravity CLI

This repository is also laid out as an Antigravity plugin (`.antigravity-plugin/plugin.json`, `mcp_config.json`, `rules/AGENTS.md`):

```bash
agy plugin install https://github.com/airdropfi/cvposh-gemini-extension
```

It connects the same MCP server (`https://cvposh.com/api/mcp`) and loads the rules as an always-on rule. The slash commands below are Gemini CLI commands; in Antigravity you just describe what you want. We have not run the plugin end to end in Antigravity.

## Tools

| Tool | What it does |
|---|---|
| `list_cvs` | List your saved CVs: id, title, template, last updated. No CV content. |
| `get_cv` | Read one CV as structured data and plain text. |
| `ats_check` | Score a saved CV (`cvId`) or pasted `resumeText` for ATS compatibility, 0-100, with issues and suggestions. |
| `analyze_keywords` | Compare a saved CV or `resumeText` with a `jobDescription`: matched keywords, missing keywords, match score. |

The ATS and keyword checks are fixed rules run on CVPosh's server; they do not call an AI model and store nothing.

## Commands

| Command | What it does |
|---|---|
| `/cvposh:cvs` | List your CVs. |
| `/cvposh:ats [cv]` | ATS score for a CV and the three fixes that matter most. |
| `/cvposh:match <job posting>` | Matched and missing keywords for a CV against a posting. |

You can also just ask ("which keywords is my product designer CV missing for this job?"); `GEMINI.md` carries the same rules.

## What it will and will not do

- It reads. It never edits, deletes or shares a CV. It suggests wording; you apply it in the CVPosh editor.
- It does not invent experience: missing keywords you cannot honestly claim are named as gaps.
- Scores come from the tools. Nobody can promise a CV will pass an ATS or lead to an interview.
- CV and job posting text is treated as data, never as instructions.
- Gemini CLI asks you to confirm MCP tool calls. Leave that on; the extension does not set `trust`.

## Contents

```text
gemini-extension.json            manifest: name, version, remote MCP server https://cvposh.com/api/mcp
.antigravity-plugin/plugin.json  Antigravity plugin manifest
mcp_config.json                  Antigravity MCP server definition (same server)
rules/AGENTS.md                  Antigravity always-on rule (a copy of GEMINI.md: keep both in sync)
GEMINI.md                        context: what CVPosh is and the rules above
commands/cvposh/*.toml           /cvposh:cvs, :ats, :match
assets/icon.png                  icon
scripts/validate.mjs             schema checks, no dependencies: node scripts/validate.mjs
```

## Data and privacy

The MCP server receives what the tools need: the CV ids, and any resume or job text you ask Gemini to send for checking, and returns your own CV content when you ask to read one. The extension itself stores nothing and runs no code on your machine; it is a server address, Markdown and prompts. Sign-in tokens are stored by Gemini CLI and can be revoked in your CVPosh account under Settings > API keys. See the [privacy policy](https://cvposh.com/privacy) and [terms](https://cvposh.com/terms).

## Support

support@cvposh.com or https://cvposh.com.

## Links

- Site: https://cvposh.com
- MCP page (setup for every client, quickstart, oversight): https://cvposh.com/mcp
- Pricing: https://cvposh.com/pricing
- Official MCP Registry: https://registry.modelcontextprotocol.io/v0/servers?search=cvposh
- Other clients: https://github.com/airdropfi/cvposh-claude-plugin, https://github.com/airdropfi/cvposh-gemini-extension, https://github.com/airdropfi/cvposh-cursor-kit
- Privacy: https://cvposh.com/privacy#ai-assistants

## License

MIT for the files in this repository (see `LICENSE`). The CVPosh service is governed by its terms.
