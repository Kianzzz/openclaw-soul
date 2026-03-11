# EvoClaw Fallback Skill

This directory contains the offline backup of the EvoClaw skill for openclaw-soul v1.2.0.

## Contents Required

When populated, this directory should contain:
- `SKILL.md` - Main skill definition
- `configure.md` - Configuration guide
- `references/` - Supporting documentation
- `validators/` - Validation rules

## Populate Instructions

Run on the OpenClaw server:
```bash
scp -r /root/.openclaw/workspace/skills/evoclaw/* <target>:/tmp/openclaw-soul/fallback/evoclaw/
```

Or copy files manually from the server's `/root/.openclaw/workspace/skills/evoclaw/` directory.
