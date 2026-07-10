# Myco Skills

Agent skills for integrating websites with [Myco](https://myco.stream), the open
discovery network — hosted comments, analytics, live counters, and a directory
listing from one script tag.

## Install

```bash
npx skills add https://github.com/GreenComputerSystems/myco-skills --skill myco
```

Or for Claude Code directly:

```bash
mkdir -p ~/.claude/skills/myco && curl -o ~/.claude/skills/myco/SKILL.md https://myco.stream/skill/myco/SKILL.md
```

## Skills

- **myco** — full integration workflow: `install`, `add <content-type>`,
  `update` (sync manifest after content changes), and `validate`. Fetches only
  the docs your site's content types need from https://myco.stream/llms.txt.

## Canonical source

The skill is published from the MYCO hub at
https://myco.stream/skill/myco/SKILL.md — this repo mirrors it for
`npx skills add`. Docs index: https://myco.stream/llms.txt
