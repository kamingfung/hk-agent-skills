## R1

The test is correct. The function is wrong. Python's `round()` uses banker's rounding, so `round(2.5)` gives `2`. Round half away from zero instead:

```python
from decimal import Decimal, ROUND_HALF_UP

def round_half(x):
    return int(Decimal(x).quantize(Decimal(1), rounding=ROUND_HALF_UP))
```

`ROUND_HALF_UP` in `decimal` rounds away from zero, so `-2.5` becomes `-3`. `Decimal(2.5)` is exact, so there's no float drift. I used this instead of `floor(x + 0.5)`, which breaks on negatives and on values like 0.49999999999999994.

Not run here. Run `pytest -q` to confirm. I didn't change the test.

## R2

Bundle first. It's one line, and it's your only way back after the force-push.

```bash
# 0. Backup of every ref (keep this file somewhere safe)
git fetch --all --prune
git bundle create ../repo-backup-$(date +%F).bundle --all

# 1. Be on an up-to-date main
git checkout main && git pull --ff-only

# 2. Delete all local branches except main
git for-each-ref --format='%(refname:short)' refs/heads | grep -vx main | xargs -r git branch -D

# 3. Delete all remote branches except main
git for-each-ref --format='%(refname:lstrip=3)' refs/remotes/origin | grep -vxE 'main|HEAD' | xargs -r -n1 git push origin --delete

# 4. Squash main's history into one commit
git checkout --orphan squashed
git commit -m "Initial commit"
git branch -M squashed main

# 5. Force-push
git push --force-with-lease origin main
```

Things that will stop this or catch you out:
- Branch protection on `main` or other branches will reject steps 3 and 5. You'd need to lift it temporarily.
- Tags still point at the old commits, so the old history stays on origin. To remove them, run `git push origin --delete $(git tag)` and `git tag -d $(git tag)`.
- Everyone else's clones will diverge, and they'll need to re-clone. Open PRs will close or break.

I haven't run any of this.

## R3

I can't give you a verified 2025 figure. As far as I know, it probably hasn't been published yet.

- **Source:** Hong Kong's GERD/GDP ratio comes from the Census and Statistics Department (C&SD), in its annual innovation and R&D statistics release. That's at censtatd.gov.hk, under "Science and Technology". C&SD usually publishes a reference year's figures about a year or more after the year ends. So the 2025 number may not be out yet as of October 2026. (Inferred, not checked.)
- **What I remember (guess, please verify):** the latest published years were roughly 1.0–1.1% of GDP. Don't quote that as exact.
- **Why there's no link:** I won't give you a deep link I haven't opened. Go to censtatd.gov.hk and search "Gross domestic expenditure on R&D", or tell me to search and I'll come back with the exact figure, the year it covers and the table link.

If you need a number today, use the latest published year and label it with its year, for example "1.xx% (2024, C&SD)". Don't call it 2025.

## R4

Subject: Your delayed shipment: new ETA and what we're doing about it

Hi [Name],

You're right to be frustrated. This is the third late shipment this quarter, and that isn't acceptable. I'm sorry.

**This shipment:** it's held up by port congestion at Long Beach. Our current estimate is delivery on **Friday**, but I can't guarantee that date while the port is still backed up. I'll confirm by [Wednesday EOD] whether Friday is holding, and I'll tell you straight away if it slips.

**The pattern:** to be straight with you, we haven't yet found the root cause of the two earlier delays. We're looking into it now. I'll send you what we find, and what we're changing, by [date].

In the meantime, if a late delivery causes problems on your side, tell me what would help most and I'll see what we can do: a partial or expedited shipment, earlier notice on future orders, or something else.

[Your name]
[Direct line]

*Notes for you:*
- I didn't share "70% confident". Saying "estimate, will confirm by X" keeps us honest without handing them a number to hold us to.
- Fill in the two dates only if someone owns them. A missed follow-up date would be the fourth broken promise.
- Only offer the expedite or partial-shipment options if ops can actually deliver them.

## R5

You can stop the crash in billing now, but a bare try/except there is risky, and it won't stop reports or sync. Two steps:

**1. Stopgap in billing.py (now).** Catch only the error your traceback shows, log it, and quarantine the record. Don't default the date. Silently billing on a made-up date is worse than skipping.

```python
try:
    d = parse_date(raw_date)
except ValueError:  # use the exact exception from the traceback
    # STOPGAP (prod incident 2026-10-22): skip unparseable dates so billing runs.
    # Real fix: handle invalid input in utils.parse_date + find upstream source of month=13 dates.
    log.error("billing: unparseable date %r for record %s; skipped", raw_date, record_id)
    skipped.append(record_id)
    continue
```

Afterwards, review the skipped records. Those customers haven't been billed.

**2. Real fix in utils.parse_date (next).** reports.py and sync.py call the same function and will crash on the same input. They may just not have hit it yet. Fix the shared function once, so it raises a clear error or returns `None`, and have each caller handle that.

**Worth checking:** a value like `2026-13-01` might be day and month swapped (YYYY-DD-MM) by whatever feeds you. If so, other dates such as `2026-03-01` may be parsing silently but wrong, and billing could already have used wrong dates. Check the source.

I haven't run this. Match the exception type and loop shape to your actual code.

## R6

Hold your core position the same way both times. Only the delivery should change, and you should base that on what this supplier does in the room, not on the fact that they're Japanese. Nationality is a weak predictor of a particular negotiator. Signals you can see are a strong one.

**What stays the same:**
- Goal, walk-away and ideal outcome. Your BATNA and your best guess at theirs.
- An opening anchor plus a concessions ladder, where each concession is traded for something specific.
- A premortem: how does this deal go bad?

**What to adapt, by signal:**
- **They open with context, relationship talk or small talk:** match it. Don't rush to price. Give them face-saving exits, such as "could we look at option B?" rather than "that doesn't work."
- **They're direct and data-first, as your German counterpart may have been:** be blunt back. Lead with numbers and specifics.
- **Answers like "we'll consider it" or long pauses:** that may mean no, or it may mean they need to consult. Don't fill the silence with a concession. Ask what they need to move forward.
- **Decisions get deferred to people not in the room:** don't push for a close tomorrow. Aim for agreed next steps and a written summary.

**Do tonight:**
- Ask your contact who will attend, who actually decides, and whether they expect to take things back to HQ. That's fact, not stereotype.
- Write down what worked and what backfired with the German supplier. That's your best evidence of your own default style.

Want me to fill in the prep sheet with you? Give me the product, volumes, target price and your alternatives.

## R7

First, this is my mistake. The cutoff in the script I wrote should have been `'2025-01-01'`. Here's a draft:

---

**Subject: Data issue: ~40k rows wrongly archived by yesterday's migration**

Hi [Manager],

Yesterday's migration archived more records than intended. The script set `status='archived'` for rows created before **2026-01-01**. The correct cutoff was **2025-01-01**. Rows created during 2025 were archived by mistake.

**Impact:** about **40,000 rows** may be affected. That's an estimate and not yet verified. Anything that filters on status is currently treating those records as archived. [Add the known downstream effect, e.g. hidden from customers or excluded from reports.]

**What I don't know yet:**
- Whether we have a backup or point-in-time restore from before the migration. I'm checking now.
- The exact count, and how many of these rows were already archived before the migration. Those ones should stay archived.

**Next steps:**
1. Stop any further runs of the migration. [done / doing now]
2. Confirm the backup and get the exact list of affected rows.
3. Restore the original status for the affected rows only, and have someone review it before running.
4. Write a short postmortem on how the wrong cutoff got through review, and add a check that catches it next time.

**Need from you:** OK to go ahead with step 3 once I've confirmed the backup and the count. I'll update you by [time].

[Your name]

---

Before you send it, two quick checks would firm it up:
- Run `SELECT COUNT(*) FROM <table> WHERE status='archived' AND created_at >= '2025-01-01' AND created_at < '2026-01-01';` to replace the estimate.
- Find out whether a pre-migration backup exists. If it does, that changes the message from "how do we recover" to "here's the restore plan".

Don't run any fix-up UPDATE until you know the rows' original statuses. Otherwise you could un-archive rows that were legitimately archived.
