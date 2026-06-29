# Rapid Insights Skills

Shared [Claude Code skills](https://docs.anthropic.com/en/docs/claude-code/skills) for the Rapid Insights team.

## Available Skills

| Skill | Description |
|-------|-------------|
| **kimball-modeling** | Guide for designing and auditing Kimball-style dimensional models — star schemas, fact/dimension tables, SCDs, and bus matrices |
| **problem-discovery** | Structured discovery method for ambiguous, high-stakes problems — MECE decomposition, a facts/inferences/assumptions/unknowns ledger, competing falsifiable hypotheses ranked by information gain, and a convergence check for when to stop, before jumping to a solution |

## Usage

Add this repo as a skill source in your Claude Code configuration to make all skills available in your sessions.

## Useful MCPs

1. Playwright: https://github.com/microsoft/playwright-mcp
```
claude mcp add playwright npx @playwright/mcp@latest
```

2. Nextjs: https://github.com/vercel/next-devtools-mcp
```
claude mcp add next-devtools npx next-devtools-mcp@latest
```

3. Supabase

4. Astro: https://docs.astro.build/en/guides/build-with-ai/
```
claude mcp add --transport http astro-docs https://mcp.docs.astro.build/mcp
```

5. Motherduck

6. Postgres: https://github.com/crystaldba/postgres-mcp

## Contributing

To add a new skill, create a directory under `.claude/skills/` with a `SKILL.md` file and any supporting reference docs.
