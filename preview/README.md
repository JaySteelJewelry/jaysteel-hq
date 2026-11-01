# JAY STEEL HQ — preview (v3)

Private, mobile-first dashboard: everything the business and its automations are doing, readable in 10 seconds.
Preview URL: https://jaysteeljewelry.github.io/jaysteel-hq/preview/  ·  Live HQ (unchanged): https://jaysteeljewelry.github.io/jaysteel-hq/

This folder is a **preview**. The live page at the repo root stays as it is until Luis says "make it live".

## What you see (in this order)
0. **10-second strip** (always on top): Next event (name, day, time, countdown; tap = details), Money due this week
   (total + count of payments due in 7 days; tap = list), Urgent asks (due within 72 h, overdue counted; tap = jump),
   Agents health ("7/9 OK" with a dot; tap = jump). "Updated X min ago" sits in the header.
1. **Needs You**: only the top 5 (Settings: 3 / 5 / 10) by real urgency: deadline soon, money, event date soon, Scout score.
   Compact card: bold title, one-line why (≤ 96 chars), deadline chip, ✅ / ❌ / ✏️ buttons, link button; the chevron opens the
   full why, the action, the deadline note and the full application draft. "Show all (N)" expands the rest grouped as
   Payments / Decisions / Approvals / Accounts (collapsible, with counts). Asks overdue more than 7 days, or whose event
   date has passed, go to a collapsed **"Stale — clear these?"** drawer: ❌ (or "Clear all") sends "❌ No (stale, dismissed
   from HQ)" on the same signed answer channel after 3 s, with Undo. Settings can hide the drawer.
2. **Today & Tomorrow**: today's and tomorrow's events/posts; when both are empty it shows the next upcoming event so the
   section is never blank. Agent runs later today are one small line, not rows.
3. **Agents**: compact rows (dot, name, last result in one line, next run). Failing (red) then yellow float to the top.
   Tap a row for what it does, last run, schedule, result, health reason.
4. **Numbers**: one tile per account: followers big, ▲/▼ change over 7 days (or since the first point while history is
   shorter), a sparkline once there are 2+ days of history, views underneath. "Data missing" tiles are greyed with the reason.
5. **Last 24 h**: timeline of posts with platform badge, time and link.
6. **Next 48 h**: timeline of events, promo posts due and one-time jobs not already shown under Today & Tomorrow.

Look: Chain Link brand (gold #C9A227 on near-black; Rose accent in Settings), sticky section tabs that highlight where you
are, smooth expand/collapse, Refresh spins and toasts "Updated just now". Settings (gear): top N, hide stale drawer,
accent, answer buttons, hide answered, larger text, compact cards, show/hide/reorder sections, auto re-pull, data status,
remember / forget device.

Rules the builder enforces: duplicates merge into one row with a ×count; an item is never in two sections (Needs You wins);
the page shows "Updated X min ago"; dark theme, 44 px+ tap targets, one HTML file (CSS inlined, no external scripts or fonts).

## Privacy
- Every data file in `data/` is **encrypted** (gzip, then AES-256-GCM; key = PBKDF2-SHA256(password, salt, 600,000)).
  The raw GitHub URLs show only ciphertext. Nothing renders until the password is entered.
- Same password and salt as the live HQ, so a phone that already unlocked the live HQ opens this one too
  ("Remember this device" keeps the derived key, never the password, in this browser's storage).
- The password is never in this repo. Home address and phone numbers are scrubbed from the data before encryption.

## Folder structure
```
preview/
  index.html          the whole app (HTML + Tailwind CSS inlined + vanilla JS)
  README.md           this file
  data/
    needs_you.json    encrypted
    agents.json       encrypted
    posts.json        encrypted
    schedule.json     encrypted
    stats.json        encrypted
```
On Tab's box (not in the repo): `files/hq/build.py` (live HQ builder) calls `files/hq/preview/hq2.py` (this preview's data
builder), `preview/encrypt_many.mjs` (encryption), `preview/tw/` (index.template.html + app.js + Tailwind build), `preview/test/test_preview3.py`
(real-browser test), `preview/sample/` (sample data), `preview/data_plain/` (last plain copy, box only).

Each encrypted file looks like:
```json
{"v":2,"alg":"AES-256-GCM/PBKDF2-SHA256","z":"gzip","iter":600000,"salt":"…","iv":"…","ct":"…","name":"agents.json"}
```
A file whose source failed is still written, as an encrypted `{"missing": true, "error": "…"}`, so the page shows
"data missing" for that section instead of stale data. A file that is absent or unreadable also shows "data missing";
the other sections keep working.

## Sample JSON (decrypted shape; sample values, not real data)
### needs_you.json
```json
{
 "generated_at": "2026-11-01T15:00:00Z",
 "items": [
  {
   "id": "pay-example-market",
   "title": "Pay Example Holiday Market, Sat Dec 5 ($50)",
   "why": "Approved for Sat Dec 5, not paid yet.",
   "detail": null,
   "action": "Pay the $50 on their Square invoice so the spot is yours.",
   "deadline": "2026-11-03T04:59:00Z",
   "deadline_note": null,
   "link": "https://example.com/invoice",
   "link_label": "Open invoice →",
   "kind": "money",
   "type": "payments",
   "short_title": "Pay Example Holiday Market, Sat Dec 5 ($50)",
   "why1": "Approved for Sat Dec 5, not paid yet.",
   "amount": 50,
   "event_date": "2026-12-05",
   "expired": null,
   "count": 1,
   "answer": {
    "kind": "pay",
    "multi": false,
    "options": [
     "✅ Done/paid",
     "⏳ Later",
     "❌ Skip"
    ]
   }
  },
  {
   "id": "approve-example-fair",
   "title": "✋ Approve (72): apply to Example Craft Fair Sat Nov 21",
   "why": "6.1 mi from base · ~2,000 shoppers (source: event page) · $40 fee.",
   "detail": "EMAIL TAB WOULD SEND\nTo: vendors@example.com\nSubject: Vendor application: JAY STEEL JEWELRY\n…",
   "action": "Tap ✅ and Tab submits it.",
   "deadline": "2026-10-30T23:59:00Z",
   "deadline_note": null,
   "link": null,
   "link_label": null,
   "kind": "approve",
   "type": "approvals",
   "short_title": "Apply to Example Craft Fair Sat Nov 21",
   "why1": "6.1 mi away · $40 fee",
   "score": 72,
   "event_date": "2026-11-21",
   "count": 2,
   "answer": {
    "kind": "approve",
    "multi": false,
    "options": [
     "✅ Yes, send it",
     "❌ No",
     "✏️ Change"
    ]
   }
  }
 ],
 "hidden_expired": []
}
```
v3 fields are written by `hq2.enrich()`: `short_title` (emoji/score prefix removed), `why1` (one line; a `why_short` field in
`needs_you_source.json` overrides it), `type` (approvals / payments / decisions / accounts), `amount`, `miles`, `score`,
`event_date` (first future date in the title), `expired` (every date in the title has passed → stale drawer). Urgency and
"stale" are computed on the phone so they stay right between rebuilds.
```
```
### agents.json
```json
{
 "generated_at": "2026-11-01T15:00:00Z",
 "agents": [
  {
   "key": "booker",
   "name": "Booker",
   "what": "Sweeps the business inbox every 3 h.",
   "cadence": "every 3 h + live",
   "last_run": "2026-11-01T15:22:00Z",
   "last_result": "inbox empty, silent.",
   "next_run": "2026-11-01T18:21:00Z",
   "health": "green",
   "health_why": null,
   "link": null
  },
  {
   "key": "example-bot",
   "name": "Example bot",
   "what": "Shows a red row.",
   "cadence": "daily 9:00 AM",
   "last_run": "2026-10-30T13:00:00Z",
   "last_result": "failed: login expired",
   "next_run": "2026-11-02T14:00:00Z",
   "health": "red",
   "health_why": "missed the 9:00 AM run",
   "link": "https://example.com"
  }
 ]
}
```
### posts.json
```json
{
 "generated_at": "2026-11-01T15:00:00Z",
 "posts": [
  {
   "platform": "Facebook",
   "time": "2026-11-01T12:30:00Z",
   "title": "Promo: Example Market (24h)",
   "link": "https://www.facebook.com/example/posts/1",
   "source": "autoposter",
   "count": 1
  }
 ],
 "checked": [
  "Facebook",
  "Discord"
 ],
 "last_post_before": null
}
```
### schedule.json (the phone cuts Today and Next 48 h from this 8-day list every minute)
```json
{
 "generated_at": "2026-11-01T15:00:00Z",
 "horizon_days": 8,
 "items": [
  {
   "kind": "event",
   "title": "Example Market",
   "start": "2026-11-01T19:00:00Z",
   "end": "2026-11-01T23:00:00Z",
   "all_day": false,
   "where": "Example Park",
   "status": "Booked",
   "link": null,
   "source": "Google Calendar",
   "count": 1
  },
  {
   "kind": "post",
   "title": "Promo post (morning): Example Market",
   "start": "2026-11-02T13:00:00Z",
   "end": null,
   "all_day": false,
   "where": "Facebook page",
   "status": "autoposter",
   "link": null,
   "source": "autoposter"
  }
 ]
}
```
### stats.json
```json
{
 "generated_at": "2026-11-01T15:00:00Z",
 "previous_build_at": "2026-11-01T12:00:00Z",
 "accounts": [
  {
   "key": "facebook",
   "platform": "Facebook",
   "handle": "Example page",
   "url": "https://www.facebook.com/example",
   "followers": 120,
   "views_48h": 40,
   "views_7d": 210,
   "views_label": "Views",
   "delta": {
    "followers": 3,
    "views_48h": -5,
    "views_7d": 12
   },
   "note": null,
   "change_7d": 3,
   "change_since": "week",
   "history": [["2026-10-30", 117], ["2026-10-31", 118], ["2026-11-01", 120]]
  },
  {
   "key": "instagram",
   "platform": "Instagram",
   "handle": "@example",
   "url": null,
   "followers": null,
   "views_48h": null,
   "views_7d": null,
   "views_label": "Views",
   "delta": {},
   "note": "API failing; shown as data missing, never estimated"
  }
 ]
}
```

## Refresh: the real cadence
- **Data** is gathered on Tab's box, because it comes from connected accounts (Gmail, Calendar, Facebook, Discord, Tab's
  schedules) that a GitHub Actions runner cannot reach. `build.py` runs inside every scheduled job: Booker every 3 h (:21 past),
  6:30 AM brief, 10 AM Scout, 7 PM brief, 11:30 PM nightly sync, and right after any inbox status change. So data is
  **about every 3 hours**, not every 30 minutes. No extra 30-minute job was added.
- **The page** re-pulls the encrypted files every 30 min while open (Settings: off / 5 / 15 / 30), on return to the app after
  10 min, and on the Refresh button (no rebuild). Today, Next 48 h, "overdue" and "Updated X min ago" are recomputed every
  minute on the phone, so they are right between rebuilds.
- A GitHub Actions cron was not added: it could only re-publish the same encrypted files, and it cannot decrypt them.

## Setup / deploy (from scratch)
1. Repo `JaySteelJewelry/jaysteel-hq`, GitHub Pages on `main` / root. This folder lives at `/preview/`.
2. On Tab's box: password in `/home/user/tab/state/hq/password.txt` (mode 600). Salt is created on first run in
   `/home/user/tab/state/hq/crypto.json`; changing the password makes every device unlock again.
3. Page: edit `files/hq/preview/tw/index.template.html` (markup + CSS) and `tw/app.js` (all JS), then `sh files/hq/preview/tw/build_site.sh`
   (Tailwind 3 from `files/hq/tw/node_modules`).
4. Build + push: `TAB_TASK_ID=<task id> timeout -k 5 200 python3 /home/user/tab/files/hq/build.py`
   (exit 2 = push failed, retry once). Add `--preview-shell` to force re-pushing index.html + README.md, `--no-push` for a dry run.
   The preview is never fatal: if it fails, the live HQ still pushes and the log says why.
5. Test: `python3 files/hq/preview/test/test_preview3.py --shots DIR` (local), `--url https://jaysteeljewelry.github.io/jaysteel-hq/preview/`
   (live), `--missing agents` (missing-file check), `--site DIR` (another build, e.g. sample data).
6. Make it live (only on Luis's word): copy `preview/index.html` to the root, point it at `preview/data/` (or move the data),
   and keep the old root page as a backup.

## Add a new agent
In `hq2.py` → `build_agents()`, add one `row(...)` call: key, name, what it does, cadence, then last run / last result /
next run / health. For a Tab schedule use `find_sched(sched_list, r'^FIRST WORDS OF ITS INSTRUCTION')` and `sched_health(s, now)`,
which reads the schedule's own run records (on time, late, failed, missed). For anything else, read its own log or API
(see the Crypto Funding Desk row, which reads GitHub's run list) and set health honestly.

## Add a new account to Numbers
Add an entry to `files/hq/accounts_source.json` (platform, handle, url, collector: instagram | facebook | pinterest | public | none).
For a new platform, add a collector branch in `build.py` → `collect_account()` (or a block in `hq2.py` → `build_stats()`, like
Discord and Square). Return `followers`, `views_48h`, `views_7d` or `None`, plus a `note` saying why a value is missing.
Change since last rebuild, the 7-day change and the sparkline history are computed automatically: every build appends a
point to `files/hq/preview/stats_history.json` (box only, last 500) and ships the last 30 days, one point per day, in stats.json.
