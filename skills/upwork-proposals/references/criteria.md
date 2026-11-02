# Job criteria

These are taken from Andrew's scheduled routine "Daily Upwork advisory job report". When he updates that routine, update this file to match.

## Tool mechanics
- `find_jobs` limit is 10 at most; a higher value errors. Page through results with `next_cursor`. `list_freelancer_proposals` and `connects_balance` also cap limit at 10.
- The `budget_min` filter is unreliable. Use it only as a loose pre-filter and check every budget with `find_jobs action=get`.
- `action=search` has no date filter. For a date window, use `smart_search mode=most_recent days_posted=N`.
- `activityStat.applicationsBidStats` (from `get`) is the most valuable field available: it shows what other freelancers are actually bidding.
- Client "rating" is the score freelancers gave that client. A low score is a warning.
- In `create`, `job_reference` is the numeric `jobs[].id`, not the `~02…` ciphertext.

## Search lanes
Search across these lanes, varying the terms:
1. Automation mentor / tutor / trainer / consultant / advisor
2. Integration architect, process automation architecture, enterprise workflow architecture
3. Fractional automation lead, AI automation implementation lead
4. CRM / ERP migration and discovery. This lane prices well: a Salesforce → monday.com discovery job paid $50–100, avg bid $78.
5. **Desktop / Windows application automation.** Search for wording like "automate repetitive tasks in [software]", "no API" or "Windows workstation". It prices far better than anything labelled RPA (example: $40–60, avg bid $55.60).
6. Business process automation
7. Credential-gated seats (a specific named certification required): few proposals, often $50–80/hr
8. UiPath / Blue Prism / RPA at rate_min 50. **Run this once to confirm it's still empty, then move on.** It has returned zero results twice.

## Kill criteria (apply before calling `get`)
- **Already filled:** totalHired ≥ 1 while personsToHire is 1. *Exception:* the description says they're building a bench of several contractors.
- Payment not verified
- Posted more than about 14 days ago, unless the proposal count is unusually low (even then, check totalHired)
- Already applied, invited, or in conversation
- **Staffing mills:** around 100 jobs posted against roughly $1k lifetime spend, or near-duplicate listings
- **Fronting arrangements:** a US-based person wanted to sit on calls or interviews representing an offshore team ("not daily development work", "professional representation", the client in a different country from the required freelancer location)

## Qualification test (both must hold)
**(a) Rate**
- When an hourly range is stated, `hourlyBudgetMax` must be at least $50. Ignore the seniority in the title and the client's lifetime spend; neither predicts the rate.
- When no rate is stated, the job qualifies only if `avgRateBid` is at least $50, or there are too few bids to compute one. Without a stated rate, avgRateBid is the real price (example: a UiPath tutoring job with no rate had an average bid of $22).
- When the max is exactly $50, check avgRateBid. If the market is bidding below $50, treat the job as **not qualifying**.
- `hourlyBudgetType` "platform default range" means Upwork filled in the numbers, so the range carries little signal. A client-specified (MANUAL) range carries much more.

**(b) Client credibility**
- Verified payment, meaningful spend relative to the number of hires, and a decent feedback score
- A new client with good rate and fit can still qualify, but name the thin history as a risk.

## Artifact and evidence gates
Don't draft a job blocked by any of these. Report it with the gate named instead.
1. **Sales or marketing outcomes:** the posting asks for measurable sales or marketing operations results. Andrew doesn't have two such examples.
2. **Adult training delivery:** trainer, instructor or enablement roles that ask for a training he ran in the last 1–2 years. Andrew has declined to claim one. Don't ask him again unless he volunteers an example.
3. **A named certification or platform he lacks** (examples: PL-600/PL-400, Global Shop ERP, Next.js/Supabase, Claude Enterprise MCP, a production GenAI/RAG deployment)
4. A **Loom** requirement isn't a hard block, but ask Andrew for the link before submitting.
5. A **GitHub** requirement is satisfied by his AI-Agents repo, as long as the proposal doesn't claim it shows CRM or marketing work.

## Flags to show with each draft
- Video or Loom, GitHub or portfolio, a work sample, a fixed-quote demand, a "begin your proposal with X" test (follow it exactly)
- `preferred_locations.timezones`: report a UTC-05/-06 restriction as a risk, not a blocker; it has gone through fine before.
- Pacific-hours expectations: "9 to 5 Pacific" means noon to 8 PM for Andrew. Name the friction.

## Bid amount
- Stated range: bid inside it, near the top. Use `hourlyBudgetMax` when it's $100 or less; otherwise bid $100.
- Rate unstated: bid around avgRateBid rounded up to the nearest $5, with $50 as the minimum and $100 as the maximum.
- Never bid under $50/hr.
- Fixed-price jobs: only consider them when the budget implies $50/hr or more for a realistic number of hours; state the assumed hours in the letter.

## Boosting
Usually not worth it, since paid slots on popular jobs run 100–200 Connects. Still, report the real `current_top_bids` every time; a fresh job once showed top bids of 15/10/8/7. A boost can't be added at confirmation; it needs the draft rebuilt with `boost_connects`.
