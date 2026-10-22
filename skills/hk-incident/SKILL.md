---
name: hk-incident
description: >-
  Use when something is broken, urgent or going wrong: production issues, failed runs, data problems, a mistake the agent made. Applies Hong Kong's typhoon-signal and SARS habits (clear single-source warnings, pre-positioned responses, act before officials, restart within hours) plus blameless postmortems and honest scoping.
license: MIT
metadata:
  author: kamingfung
  version: "1.0"
  repository: https://github.com/kamingfung/hk-agent-skills
---

# hk-incident — Typhoon Signal No. 8

HK resumes normal life within hours of lowering Signal No. 8 because preparation is routine and warnings are unambiguous (S46, S47). After SARS, people masked up and organized on their own before official advice came (S44, S45).

## Borrow
- **One clear signal.** Open with a single unambiguous severity line: what's broken, who is affected, and the current state (S46).
- **Act on what you know.** Take safe, reversible containment steps immediately. Don't wait for perfect information (S44).
- **Pre-positioned crews.** Keep the rollback, logs and diagnostic commands ready before you touch anything (S47, S48).
- **Learn from the last storm.** Check prior incidents and notes for the same pattern (S43, S45).
- **Restart fast.** Restore service first, then find the root cause (S47).

## Guard
- **Honest blast radius.** Report the full scope, including what you haven't checked. Early under-counting compounds the damage (W60, W61).
- **Containment ≠ fix.** Label the hotfix as a stopgap and track the root-cause fix (W90, W96).
- **Irreversible actions still need confirmation.** Urgency doesn't license deleting data or force-pushing without a yes.
- **Blameless postmortem.** Fix the system, not the person. If blame is in the air, problems get hidden (W97, W98).
- **Own your own mistakes.** If the agent caused it, say so first and plainly.

## Postmortem template
Use [assets/postmortem.md](assets/postmortem.md).

Source IDs (`S#`, `W#`) resolve in [references/sources.md](references/sources.md).
