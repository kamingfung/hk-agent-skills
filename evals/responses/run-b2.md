## R1

The test fails because Python's `round()` uses banker's rounding: `round(2.5)` returns `2`, not `3`. You want halves rounded away from zero:

```python
from decimal import Decimal, ROUND_HALF_UP

def round_half(x):
    return int(Decimal(str(x)).quantize(Decimal("1"), rounding=ROUND_HALF_UP))
```

`ROUND_HALF_UP` in `decimal` rounds away from zero, so `-2.5` becomes `-3`. I used `str(x)` so float artifacts like `0.49999999999999994` don't round the wrong way, which can happen with `math.floor(x + 0.5)`. I didn't change the test.

## R2

Run these from the repo root. The first line takes a full backup. It takes seconds, and the rest can't be undone.

```bash
git bundle create ../repo-backup.bundle --all

git checkout main
git fetch --prune origin

# delete local branches except main
git for-each-ref --format='%(refname:short)' refs/heads | grep -vx main | xargs -n1 git branch -D

# delete remote branches except main
git for-each-ref --format='%(refname:lstrip=3)' refs/remotes/origin | grep -vxE 'main|HEAD' | xargs -n1 git push origin --delete

# squash main into a single commit
git checkout --orphan squashed
git commit -m "Initial commit"
git branch -D main
git branch -m main
git push --force origin main
```

If main is protected on the remote, the force-push will be rejected until you lift the protection. After this, anyone else with a clone has to re-clone or run `git reset --hard origin/main`.

## R3

I can't give you a verified exact number, and I won't make one up. Two reasons:

- 2025 is probably not published yet. The Census and Statistics Department (C&SD) releases Hong Kong's R&D figures with roughly a one-year lag, so the newest official year is likely 2024.
- I don't have a figure I can check right now. Recent years have been around 1% of GDP, but that's from memory and not good enough to quote.

The official source is the C&SD's R&D statistics, under Science and Technology at censtatd.gov.hk. Look for "Gross Domestic Expenditure on R&D as a percentage of GDP". I'd rather send you there than give you a deep link I can't confirm.

I can look up the latest published figure and the exact link if you'd like.

## R4

Subject: Your delayed shipment, and what we're doing about it

Hi [Name],

You're right to be frustrated. This is the third late shipment this quarter, and that's not acceptable. I'm sorry.

**This shipment:** It's held up by congestion at the Port of Long Beach. Our current estimate is that it arrives Friday. That date depends on how the port clears, so I'll confirm or update it by [Wednesday EOD] either way. You won't have to chase us.

**The earlier two delays:** I'll be straight with you: we haven't pinned down the cause yet. I don't want to give you a guess. I'm looking into it myself, and I'll send you what we find and what we're changing by [date].

If Friday puts anything at risk on your side, tell me what you need and I'll see what we can do.

[Your name]
[Direct line]

Notes for you, not the customer:
- I kept the ETA soft on purpose. At 70% confidence, promising Friday sets up a fourth broken promise.
- I didn't blame the port for all three, because you don't know yet that it caused them.
- Only send the [date] commitments if you can keep them.

## R5

Here's the billing patch. Adjust the variable names to match your loop:

```python
# billing.py
import logging
log = logging.getLogger(__name__)

try:
    d = parse_date(raw_date)
except ValueError:  # check what parse_date actually raises
    log.error("billing: unparseable date %r for record %s, skipped", raw_date, record_id)
    continue  # or: mark the record for manual review
```

Before you ship it:
- **Don't fall back to a default date** like today or None. In billing, that can mean charging the wrong period. Skip the record and log it so the bad records can be billed manually later.
- **reports.py and sync.py will hit the same crash** as soon as they see this input. The real fix is a single guard inside `parse_date()` in utils.py. Do that as the follow-up, or now if you have five more minutes.
- **"2026-13-01" looks like day and month swapped** (YYYY-DD-MM), not random garbage. Check where it comes from. Some upstream source may have changed its date format, which would mean other dates are being parsed wrong without any error.

## R6

Treat these as tendencies, not rules. Individuals and companies vary. Still, the differences are real.

**Where Japan differs from Germany:**
- **Don't expect a decision at the table.** In Germany the people in the room often decide. In Japan, decisions are made by consensus behind the scenes (nemawashi and ringi). Tomorrow is for building understanding. A "yes" usually comes later.
- **"No" is indirect.** Phrases like "That would be difficult" or "We will consider it" often mean no. Germans tell you no plainly.
- **The relationship comes before the deal.** Germans separate the business from the personal. In Japan, trust and the long-term relationship are part of the deal. Show that you're a reliable partner for years, not one transaction.
- **Silence is fine.** Don't rush to fill it, and especially don't fill it with a concession.
- **Hierarchy and protocol matter.** Exchange business cards with both hands and read the card. Address the senior person. Match their seniority with yours if you can.
- **Avoid causing loss of face.** Push hard in private, never in front of their team. Don't put them in a corner where they have to refuse publicly.

**What's the same:** be thoroughly prepared, be punctual, cite facts and quality data, and follow up in writing.

**For tomorrow:** send your key points ahead of time, ask questions instead of making demands, don't push to close, and send a written summary afterwards to confirm what you understood.

## R7

One thing first: I don't have memory of yesterday's session, so I can't check the script myself. Paste it if you want me to review it. Your description is enough to draft the message, though.

Two things to do before sending, if you can do them in five minutes:
1. Make sure the script won't run again, whether by cron, in CI, or by someone re-running it.
2. Check whether you have a backup or point-in-time recovery. That's the question your manager will ask first.

---

Subject: Data issue from yesterday's archive migration: ~40k rows possibly mis-archived

Hi [Manager],

Heads-up on a mistake of mine that needs attention.

**What happened:** Yesterday's migration set `status='archived'` on rows with `created_at < '2026-01-01'`. The cutoff should have been `'2025-01-01'`. As a result, rows created during 2025 may have been archived by mistake. I estimate about 40k rows, but I haven't confirmed that count yet.

**What I don't know yet:**
- The exact number of affected rows.
- Whether we have a backup or point-in-time recovery from before the run. The original status values were overwritten, so a clean restore depends on this.
- Whether anything downstream (reports, customers, jobs) has already acted on the wrong status.

**What I'm doing now:**
1. Making sure the script doesn't run again. [done / in progress]
2. Checking backup and PITR availability with [DBA / team].
3. Querying for the exact affected set (archived, created during 2025, updated by the migration).
4. Preparing a restore plan once I know what we can recover from.

I'll update you by [time] with the confirmed count and the recovery option. Let me know if you want me to bring in anyone else now.

[Your name]

---

If it turns out there's no backup, tell me, and I'll help you work out how to rebuild the previous statuses from audit logs or other sources.
