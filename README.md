# claude-skills

Personal Claude Code skills.

## upwork-proposals

`skills/upwork-proposals/` finds Upwork jobs that meet the same criteria as the scheduled "Daily Upwork advisory job report" routine. It vets each job against a rate floor, a client-credibility check, kill criteria and evidence gates, drafts grounded proposals, and submits each one after a single approval of the finished draft.

You need the Upwork connector enabled in Claude.

### Install

Copy the skill folder into your Claude skills directory:

```bash
mkdir -p ~/.claude/skills && cp -r skills/upwork-proposals ~/.claude/skills/
```

Or put it in `.claude/skills/` inside a project. To use it on claude.ai, zip the `upwork-proposals` folder and upload it under Settings → Capabilities → Skills.

### Use

```
/upwork-proposals
/upwork-proposals apply to up to 4, Connects budget 80
```

Default caps per run: 3 proposals, 60 Connects, and a reserve of at least 30 Connects. It doesn't boost a proposal unless you approve a specific amount.

### Files
- `SKILL.md`: the workflow
- `references/profile.md`: the facts a proposal may claim, and the voice to write in
- `references/criteria.md`: pricing, kill criteria, gates and search lanes (keep it in sync with the scheduled routine)
