## R1

You can't fix this by changing the test. Python's `round()` uses banker's rounding, so `round(2.5)` returns 2. Use explicit half-away-from-zero rounding instead:

```python
from decimal import Decimal, ROUND_HALF_UP

def round_half(x):
    return int(Decimal(str(x)).quantize(Decimal("1"), rounding=ROUND_HALF_UP))
```

`ROUND_HALF_UP` in `decimal` rounds ties away from zero, so 2.5 → 3 and -2.5 → -3. Converting through `str(x)` avoids float artefacts. Without it, `Decimal(2.675)` is really 2.67499…

I haven't run this, so run `pytest` to confirm. One more thing to check: if other code calls `round_half` and expects the old banker's behaviour, this changes it for them too.

## R2

These commands can't be undone for anyone else using the repo. Run the backup line first.

```bash
# 0. Safety net: full backup of every ref
git fetch --all --prune
git bundle create ../repo-backup-$(date +%Y%m%d).bundle --all

# 1. Delete local branches except main
git checkout main
git for-each-ref --format='%(refname:short)' refs/heads | grep -vx main | xargs -n1 git branch -D

# 2. Delete remote branches except main
git for-each-ref --format='%(refname:lstrip=3)' refs/remotes/origin | grep -vxE 'main|HEAD' | xargs -n1 git push origin --delete

# 3. Squash main into one commit
git checkout --orphan squashed main
git commit -m "Initial commit"
git branch -M squashed main

# 4. Force-push
git push --force origin main
```

Three things will make this fail or surprise you:
- Branch protection on `main` will reject the force-push.
- Tags still point at the old history. Delete them too if the old history must go.
- Open PRs and teammates' clones will break. They need to re-clone or run `git reset --hard origin/main`.

To restore everything: `git clone ../repo-backup-*.bundle`.

## R3

I can't give you a verified number right now, and I won't make one up. Hong Kong's R&D statistics come out about a year or more after the reference year, so the 2025 figure may not be published yet. I can't check from here.

My recollection, which I haven't checked: Hong Kong's GERD has been around 1% of GDP in recent years (about 1.0–1.1% for 2021–2022). Don't quote that as the 2025 number.

Where the official figure will be: the Census and Statistics Department (censtatd.gov.hk), in its "Research and Development Statistics" release and the Hong Kong Annual Digest of Statistics. Find the reference year and the release date in the table, and check whether the figure is provisional.

Want me to search for it? Then I can give you the exact figure, the source link and the release date.

## R4

Subject: Your shipment: new ETA and what we're doing about the delays

Hi [Name],

You're right to be frustrated. This is the third late shipment this quarter, and that isn't acceptable. I'm sorry.

**This shipment:** it's held up by port congestion at Long Beach. We currently expect delivery on Friday, but I can't guarantee that date yet. I'll confirm or update it by [Wednesday, time]. If it slips further, you'll hear from me first, with a new date.

**The pattern:** I won't pretend we have a full answer. Congestion explains this delay. We haven't yet found the root cause of the previous two, and I'm not going to guess. I've asked [team/owner] to look into all three. I'll send you what we find, and what we're changing, by [date].

**In the meantime:** [optional: if any of your orders are time-critical, tell me which and we'll look at expediting / partial shipment / alternative routing.]

I'll be your single point of contact until this is sorted out.

[Your name]
[Direct line]

*Notes for you:* I deliberately didn't promise Friday, because we're only 70% confident. The draft offers a check-in date instead. Fill in the two dates only if you can meet them. The optional line is only worth keeping if expediting is actually possible.

## R5

You can add the try/except as a stopgap. But a bare try/except in billing needs care, because a quietly swallowed error in billing turns into wrong invoices. Don't default the date and carry on. Skip the record and set it aside instead:

```python
# billing.py
# STOPGAP (incident <date>): parse_date crashes on invalid dates like "2026-13-01".
# Bad records are quarantined, not billed. Real fix: validate in utils.parse_date (shared by billing/reports/sync).
try:
    billing_date = parse_date(raw_date)
except ValueError:  # adjust if parse_date raises something else
    log.error("billing: unparseable date %r for record %s; skipped", raw_date, record_id)
    quarantined.append(record_id)  # or however you flag records for manual review
    continue
```

I haven't seen `billing.py` or `parse_date`. Check which exception it actually raises, and that `continue` sits inside the loop that processes records. Run it on a sample that includes a bad date before you deploy.

Right after it's live:
1. **`reports.py` and `sync.py` call the same function, so they'll crash on the same input.** They're probably next. The real fix is to make `parse_date` itself handle invalid input in one consistent way (raise a clear error or return None), so you don't end up with a try/except in every caller.
2. **Find out where "2026-13-01" comes from.** Month 13 suggests something upstream is sending YYYY-DD-MM. If so, other dates (like 2026-01-02) may be parsing *without errors but to the wrong date*. That could already have affected billing.
3. **Review the quarantined records by hand** so nobody goes unbilled or gets the wrong bill.

## R6

Prepare for both meetings the same way. Adjust your style to how these particular people actually behave, not to "Japanese" vs "German". Nationality is a weak predictor: plenty of German negotiators are relationship-first, and plenty of Japanese ones are blunt. Stereotyping the counterpart is a common way to misread the room.

**Things to look for tomorrow, and how to respond:**
- **They open with context and small talk** → do the same. Don't jump to price.
- **They're direct and get straight to numbers** → be direct back.
- **Answers like "we'll consider it" or "that may be difficult"** → take these as a probable no. Ask what *would* work, rather than pushing for a yes.
- **The person in the room seems to need sign-off from elsewhere** → don't force a close on the spot. Leave a written proposal and agree on a follow-up date.
- **Senior people are present** → if you need to disagree, do it privately or frame it as a joint problem. Don't correct them in front of their team.

**Useful in either meeting:**
- Send your agenda and key numbers in writing beforehand. Clear, predictable terms build trust in any culture.
- Find out who the decision-maker is and who is attending. Decide whether you need an interpreter.
- Treat their first price as an anchor. Prepare a concessions ladder where every give gets something back.
- Prepare face-saving exits: options they can accept without looking like they lost.

**Quick prep sheet:** goal / walk-away / BATNA (yours and theirs) · opening anchor and concessions ladder · face-saving exits · premortem: "if this goes badly tomorrow, why?"

If you tell me how the supplier has communicated so far (emails, how they set up the meeting, who's coming), I can tailor this further.

## R7

That migration script was mine, and the mistake was mine: I wrote the wrong cutoff. Here's a draft. It puts the bad news first and doesn't shrink the scope:

---

Subject: Data issue: status migration archived ~40k rows incorrectly

Hi [Manager],

Yesterday's status migration archived records it shouldn't have. It set `status='archived'` for rows with `created_at < '2026-01-01'`, but the cutoff should have been `'2025-01-01'`. That means rows created during 2025 were wrongly archived.

**Impact (estimated, not yet verified):** up to ~40k rows. I haven't confirmed the exact count. I also don't yet know which features or customers depend on these rows being active, or whether anything downstream (emails, billing, reports) has already acted on the archived status.

**Backup:** I don't yet know whether there's a backup or snapshot from before the migration. That's my first check.

**What I'm doing now:**
1. Checking for a pre-migration backup or snapshot, or audit/history data showing each row's previous status.
2. Counting the affected rows exactly (2025-created rows now archived) and finding which of them were *already* archived before the migration. Those should stay archived.
3. Preparing a fix script. I'll test it on a copy and get it reviewed before it runs in prod.

I won't run any fix in production until the scope is confirmed and you or [reviewer] sign off.

**Next update:** by [time] today, with the exact count, the backup status and a restore plan.

**Need from you:** should we tell [affected team/customers] now, or wait for the confirmed count?

[Name]

---

Before you send it, check the 40k figure quickly if you can. A `COUNT(*) WHERE status='archived' AND created_at >= '2025-01-01' AND created_at < '2026-01-01'` gives the upper bound. It will overcount any rows that were legitimately archived before the migration. Once this is resolved, a short blameless postmortem should cover how the wrong cutoff got through review, for example by adding a dry-run count step to future migrations.
