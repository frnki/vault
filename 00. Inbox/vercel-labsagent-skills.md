---
title: "agent-skills/skills/react-best-practices/references/rules at react-best-practices · vercel-labs/agent-skills"
source: "https://github.com/vercel-labs/agent-skills/tree/react-best-practices"
author:
  - "[[quuu]]"
published:
created: 2026-01-16
description: "Contribute to vercel-labs/agent-skills development by creating an account on GitHub."
tags:
  - "clippings"
---
**[agent-skills](https://github.com/vercel-labs/agent-skills)** Public

[Open in github.dev](https://github.dev/) [Open in a new github.dev tab](https://github.dev/) [Open in codespace](https://github.com/codespaces/new/vercel-labs/agent-skills/tree/react-best-practices?resume=1)

This branch is [19 commits behind](https://github.com/vercel-labs/agent-skills/compare/react-best-practices...main) main.

<table><thead><tr><th colspan="2"><span>Name</span></th><th colspan="1"><span>Name</span></th><th><p><span>Last commit message</span></p></th><th colspan="1"><p><span>Last commit date</span></p></th></tr></thead><tbody><tr><td colspan="3"><p><span><a href="https://github.com/vercel-labs/agent-skills/commit/6e356b41264f0815d03740b5ae32ca5e1dddcfef">6e356b4</a> ·</span></p><p><a href="https://github.com/vercel-labs/agent-skills/commits/react-best-practices/"><span><span><span>19 Commits</span></span></span></a></p></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/tree/react-best-practices/dx/skills"><span>dx/</span> <span>skills</span></a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/tree/react-best-practices/dx/skills"><span>dx/</span> <span>skills</span></a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/6035b2cfa35b08af82fe44695ab3b55674a58313">Move vercel-design-guidelines skill to dx/skills</a></p></td><td></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/tree/react-best-practices/skills">skills</a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/tree/react-best-practices/skills">skills</a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/6e356b41264f0815d03740b5ae32ca5e1dddcfef">Updated instructions</a></p></td><td></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/.gitignore">.gitignore</a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/.gitignore">.gitignore</a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/7f947cd4512523845f284554cb279aefea283386">Initial skill</a></p></td><td></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/AGENTS.md">AGENTS.md</a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/AGENTS.md">AGENTS.md</a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/20f4a0442f1de7645f01a3fb2035122511fd4140">Rename CLAUDE.md to AGENTS.md for universal agent compatibility</a></p></td><td></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/CLAUDE.md">CLAUDE.md</a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/CLAUDE.md">CLAUDE.md</a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/20f4a0442f1de7645f01a3fb2035122511fd4140">Rename CLAUDE.md to AGENTS.md for universal agent compatibility</a></p></td><td></td></tr><tr><td colspan="2"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/README.md">README.md</a></p></td><td colspan="1"><p><a href="https://github.com/vercel-labs/agent-skills/blob/react-best-practices/README.md">README.md</a></p></td><td><p><a href="https://github.com/vercel-labs/agent-skills/commit/6e356b41264f0815d03740b5ae32ca5e1dddcfef">Updated instructions</a></p></td><td></td></tr><tr><td colspan="3"></td></tr></tbody></table>

## Agent Skills

A collection of skills for AI coding agents. Skills are packaged instructions and scripts that extend agent capabilities.

Skills follow the [Agent Skills](https://agentskills.io/) format.

## Available Skills

### react-best-practices

React and Next.js performance optimization guidelines from Vercel Engineering. Contains 40+ rules across 8 categories, prioritized by impact.

**Use when:**

- Writing new React components or Next.js pages
- Implementing data fetching (client or server-side)
- Reviewing code for performance issues
- Optimizing bundle size or load times

**Categories covered:**

- Eliminating waterfalls (Critical)
- Bundle size optimization (Critical)
- Server-side performance (High)
- Client-side data fetching (Medium-High)
- Re-render optimization (Medium)
- Rendering performance (Medium)
- JavaScript micro-optimizations (Low-Medium)

### vercel-deploy-claimable

Deploy applications and websites to Vercel instantly. No authentication required. Deployments are "claimable" - users can transfer ownership to their own Vercel account.

**Use when:**

- "Deploy my app"
- "Deploy this to production"
- "Push this live"
- "Deploy and give me the link"

**Features:**

- No authentication required - works instantly
- Auto-detects 40+ frameworks from `package.json`
- Returns preview URL (live site) and claim URL (transfer ownership)
- Handles static HTML projects automatically
- Excludes `node_modules` and `.git` from uploads

**How it works:**

1. Packages your project into a tarball
2. Detects framework (Next.js, Vite, Astro, etc.)
3. Uploads to deployment service
4. Returns preview URL and claim URL

**Output:**

```
Deployment successful!

Preview URL: https://skill-deploy-abc123.vercel.app
Claim URL:   https://vercel.com/claim-deployment?code=...
```

## Installation

```
npx add-skill vercel-labs/agent-skills
```

### claude.ai

Add the skill to your project knowledge or paste the contents of `SKILL.md` into your conversation.

For network-dependent skills (like vercel-deploy-claimable), you may need to allow domains:

1. Go to [claude.ai/admin-settings/capabilities](https://claude.ai/admin-settings/capabilities)
2. Add required domains (e.g., `*.vercel.com`)

## Usage

Skills are automatically available once installed. The agent will use them when relevant tasks are detected.

**Examples:**

```
Deploy my app
```

```
Review this React component for performance issues
```

```
Help me optimize this Next.js page
```

## Skill Structure

Each skill contains:

- `SKILL.md` - Instructions for the agent
- `scripts/` - Helper scripts for automation (optional)
- `references/` - Supporting documentation (optional)

## License

MIT

## Releases

No releases published

## Packages

No packages published  

## Languages

- [JavaScript 54.9%](https://github.com/vercel-labs/agent-skills/search?l=javascript)
- [TypeScript 25.7%](https://github.com/vercel-labs/agent-skills/search?l=typescript)
- [Shell 19.4%](https://github.com/vercel-labs/agent-skills/search?l=shell)