# shared-claude-skills

A collection of Claude Code plugins (skills) for security and development workflows.

## Skills

### [vuln-hunter](./vuln-hunter)

Multi-stage security scanner for application code and infrastructure configurations, inspired by [Cloudflare's Project Glasswing](https://blog.cloudflare.com/project-glasswing/).

Instead of a single generic scan, parallel specialized agents each hunt one vulnerability class, then a validation pass eliminates noise.

**Pipeline:**

```
RECON → HUNT (15 agents, parallel) → VALIDATE → TRACE → REPORT
```

| Phase | What it does |
|-------|-------------|
| **RECON** | Maps entry points, trust boundaries, data flows, and infra topology |
| **HUNT** | 15 agents run simultaneously, each targeting one vulnerability class |
| **VALIDATE** | Eliminates false positives, deduplicates, normalizes severity |
| **TRACE** | Verifies reachability (code) or effective impact (infra) for Critical/High findings |

**Vulnerability classes covered:** Injection · Auth & Session · Secrets Exposure · Access Control · Crypto Failures · Input Validation · SSRF & Redirects · Supply Chain · Concurrency · Security Misconfiguration · AI-Specific Patterns · Business Logic · IAM & Privilege · Container Security · Network Exposure

**Usage:**

```
/vuln-hunter               # full scan of current directory (code + infra)
/vuln-hunter diff          # scan git diff (staged + unstaged)
/vuln-hunter staged        # scan staged changes only
/vuln-hunter path/to/file  # scan a specific file or directory
```

## Installation

Clone the repository, then load the plugin with `--plugin-dir`:

```bash
git clone https://github.com/genkist/shared-claude-skills.git
```

**Start Claude Code with a specific plugin:**

```bash
claude --plugin-dir ./shared-claude-skills/vuln-hunter
```

**Or add to your project's plugin config** (`.claude/settings.json`):

```json
{
  "pluginDirs": ["/absolute/path/to/shared-claude-skills/vuln-hunter"]
}
```

The skill becomes available as a slash command once the plugin is loaded.

**Keep skills up to date:**

```bash
cd shared-claude-skills && git pull
```

## Contributing

New skills are welcome. Each skill lives in its own top-level directory with the following structure:

```
my-skill/
├── .claude-plugin/
│   └── plugin.json        # name, version, description, author
└── skills/
    └── my-skill/
        └── SKILL.md       # skill definition and instructions
```

`plugin.json` minimum fields:

```json
{
  "name": "my-skill",
  "version": "0.1.0",
  "description": "One-line description of what the skill does.",
  "author": {
    "name": "your-github-username"
  }
}
```

Open a pull request with your new skill directory.

## License

MIT
