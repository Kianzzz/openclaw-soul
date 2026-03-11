# Self-Improving Fallback Skill

This directory contains the offline backup of the Self-Improving skill for openclaw-soul v1.2.0.

## Contents Required

When populated, this directory should contain:
- `SKILL.md` - Main skill definition
- `setup.md` - Setup instructions
- `learning.md` - Learning protocols
- `operations.md` - Operation procedures
- `scaling.md` - Scaling guidelines
- `boundaries.md` - Boundary definitions
- `memory-template.md` - Memory structure template
- `corrections.md` - Corrections template
- `reflections.md` - Reflections template
- `memory.md` - Memory file template

## Populate Instructions

Run on the OpenClaw server:
```bash
scp -r /root/.openclaw/workspace/skills/self-improving/* <target>:/tmp/openclaw-soul/fallback/self-improving/
```

Or copy files manually from the server's `/root/.openclaw/workspace/skills/self-improving/` directory.
