---
name: upwork-proposals
description: Find Upwork jobs that meet Andrew Randolph's standing criteria (rate floor, client credibility, kill criteria, artifact gates) and submit proposals for them on his behalf. Use when Andrew asks to "find and apply to Upwork jobs", "submit proposals", "run the Upwork routine and apply", "send proposals for today's matches", or invokes /upwork-proposals. Not for client-side hiring, replying to messages, or changing profile boost or availability-badge settings.
---

# Upwork proposals: search, qualify, submit

This skill takes the same criteria as Andrew's scheduled "Daily Upwork advisory job report" routine and goes one step further: after drafting proposals it **submits** them, once Andrew approves each draft in the session.

Read `references/profile.md` (who Andrew is, what he can honestly claim) and `references/criteria.md` (pricing, kill criteria, gates) before doing anything else. They are the source of truth, and everything below depends on them.

## Hard rules

1. **Nothing is submitted without Andrew's approval of the exact preview.** The Upwork connector marks `confirm_preview` as a write that needs explicit user confirmation, and every submission spends Connects that can't be refunded. "Submit on my behalf" means Claude does all the searching, vetting, drafting and form-filling, and Andrew's whole part is saying yes or no to a finished draft. If the skill runs unattended (a scheduled routine with nobody watching), stop after building the drafts and report them. Never confirm one on your own.
2. **Never fabricate anything.** That covers metrics, clients, projects, praise, training history, certifications, platform experience, Looms and portfolio links. If a screening question needs experience Andrew doesn't have, drop the job or ask him; never write around it.
3. **Spending caps per run** (Andrew can override them in his request, e.g. "apply to up to 4, budget 80"):
   - At most **3** proposals submitted.
   - At most **60 Connects** spent on submissions, boosts included.
   - Always leave at least **30 Connects** in the balance.
   - No boost unless Andrew approves a specific number of Connects for it.
4. Never touch the profile boost, the availability badge, the rate, or the profile itself.

## Workflow

### 1. Set up
- `list_accounts` → confirm the freelancer org_uid (expected `1337455235260203009`).
- If the Upwork MCP tools aren't available, say so and stop. Don't guess at results.
- `get_profile action=connects_balance` → record the balance and work out how much this run can spend under the caps.

### 2. Build the do-not-apply list
Collect every job id Andrew already has a relationship with:
- `list_freelancer_proposals action=list` (page through it with limit 10)
- `list_freelancer_proposals action=invitations`. An invited job goes through `accept_invitation`, never `create`.
- `get_messages action=list_rooms`. Also flag any client who is waiting on Andrew, and any call he has agreed to that falls today. Put those at the top of the output; a live conversation is worth more than a new listing.

### 3. Search
Run `find_jobs action=search` (limit at most 10, `verified_payment_only: true`) across the lanes in `criteria.md` → *Search lanes*. Sort some searches by recency and some by client_total_charge. Run `action=smart_search` once with `mode=most_recent` and `days_posted` 3. Collect the candidates and remove duplicates.

### 4. Cheap kills first
Apply the kill criteria from `criteria.md` to the search results before calling `get`. Keep a one-line drop reason for every job you remove.

### 5. Deep-check the survivors
Call `find_jobs action=get` on each survivor and read hourlyBudgetMin/Max, hourlyBudgetType, `activityStat.applicationsBidStats`, proposal_count, jobActivity (totalHired vs personsToHire), client spend/hires/feedback, screening_questions, connects_cost and preferred_locations. Then apply the **qualification test** and the **gates** in `criteria.md`.

### 6. Rank and pick
Rank the qualifying jobs by fit (advisory or architecture work first, then CRM/ERP discovery, then desktop/Windows automation, then build work that pays $50+), market rate (avgRateBid), freshness and low competition. Take the top ones that fit within the caps after adding up each job's connects_cost.

### 7. Draft
For each job you picked:
- Check again that it isn't on the do-not-apply list. Upwork rejects `create` if a proposal already exists (VJ-JA-10).
- Write the cover letter in Andrew's voice (see `profile.md` → *Voice*): it addresses the posting's actual problem, cites real relevant history, and puts the rate in the body when the posting asks for one. 5000 characters at most; 1200–2500 is better.
- Bid (`charged_amount`): use Andrew's rate logic in `criteria.md` → *Bid amount*.
- Answer every screening question truthfully in `answers`. If a question depends on a fact only Andrew knows, ask him; don't accept "no preference" as permission to invent one.
- Attach certificate ids only where they're relevant (`profile.md` → *Certificates*).
- Call `manage_proposals action=create` and keep the `preview_id`.

### 8. Present each draft and get approval
For each draft, show:
- Title, URL, client (spend / hires / rating), budget or "rate unstated (avg bid $X)", proposal count, and one line on why it fits
- The full `preview_text`. Andrew's approval only covers what he has actually seen.
- connects_cost, connects_balance, and the Connects left in this run's budget
- `unmet_preferred_qualifications`, if there are any
- Boost: the real `boost.current_top_bids` and `boost.recommended_connects`, or a note that boosting isn't available. When placing a boost is genuinely cheap, say so.
- **Attachments question (mandatory):** ask whether he wants any files attached (resume, work samples). If yes, use `start_attachment_upload context='proposals'`, then rebuild the draft with the `attachments` param.

Ask for each draft: **submit / edit / skip** (and a boost amount, if he wants one). Previews expire about 15 minutes after they're built. If approval comes later, or Andrew asks for edits or a boost, rebuild with `create` and show the new preview before confirming.

### 9. Submit
Only for drafts Andrew approved: `confirm_preview action=confirm params={type:"proposal", preview_id}`. Then check each one with `list_freelancer_proposals action=get` (status Pending, and `terms.connectsBid` if he boosted). If a confirm fails, report the error exactly as given and don't retry blindly.

### 10. Report
Keep it short:
- **Waiting on you:** client messages or calls (only if there are any)
- **Submitted:** title, URL, bid, Connects spent, proposal id
- **Drafted, not submitted:** the reason for each (skipped, expired, over the caps)
- **Qualifying but gated:** the gate named (`criteria.md` → *Artifact and evidence gates*)
- **Dropped:** one line per job with the reason. This is how Andrew learns the market.
- **Connects:** starting balance, spent this run, ending balance

If nothing qualifies, say that in one or two sentences. Don't lower the bar to fill the list; "nothing today" is the correct result most days.
