---
name: hk-research
description: >-
  Use when researching, searching the web, analyzing data, or producing a recommendation from evidence. Applies wet-market shopping habits (compare stalls, buy fresh, haggle the first number) and guards against fast-answer overconfidence, rote pattern-matching and invented sources.
license: MIT
metadata:
  author: kamingfung
  version: "1.0"
  repository: https://github.com/kamingfung/hk-agent-skills
---

# hk-research — Wet-Market Research

Shop like a wet-market regular: go daily for fresh produce, compare stalls, and never take the first price at face value (S20, S21).

## Borrow
- **Compare stalls.** Get at least 2–3 independent sources for any claim that matters, and prefer primary sources over syndicated ones (S20).
- **Buy fresh.** Prefer recent sources and note the date. A 2015 survey isn't today's market (S21).
- **Haggle the first number.** Treat the first figure you find as an opening offer. Who published it, and why? Vendor and government numbers have an interest (S20, S92).
- **Fan out, then distill.** Run searches in parallel and compress the results into a few principles that cite their evidence (S77, S8).
- **Bottom line first.** Give the answer, then the confidence, then the evidence (S7, S61).

## Guard
- **No invented sources.** Cite only URLs and figures you actually saw. If you didn't open the full text, say "from summary" (W82).
- **Calibrate.** Tag each claim as verified, inferred or guess. Hard and unfamiliar questions are where overconfidence peaks (W82, W83).
- **Time pressure narrows the search.** Before concluding, name one alternative explanation and say why it loses (W78, W79).
- **No rote templates.** Shape the analysis to the question rather than the familiar framework (W40, W42).
- **Show the counterpoint.** A good market report names the counterpoint, as with the Lion Rock "spirit" versus inequality (S56, W75).
- **Long-term lens.** Ask what still holds in 5 years and what is just this quarter's noise (W26, W47).

## Output shape
1. Answer (one line) and confidence.
2. Three to five key findings, each with source IDs or links.
3. What would change the conclusion.
4. Caveats: source quality, date and gaps.

Source IDs (`S#`, `W#`) resolve in [references/sources.md](references/sources.md).
