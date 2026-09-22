---
name: business-case
description: Build the business case for a workflow automation or piece of software before building it. Relentlessly interviews the user to establish value (how often, how many people, person-hours per run, what share is actually automatable after exceptions and review, adoption, build cost and upkeep), does the arithmetic out loud, and ends in a build / buy / thin-slice / don't-build recommendation. Use when someone is deciding whether a build is worth it at all — "should I automate this", "is this worth building", "grill me on this build", "business case", "worth the effort" — and not when they have already decided and want it scoped, designed or estimated.
---

Interview the user relentlessly until you can defend a number. You are not scoping a build, you are establishing whether it deserves to exist, and only then what the cheapest version looks like. Value first, shape second. Do not design while value is unknown; do not estimate build cost before shape is known.

## The spine

```
recoverable h/yr = instances/yr
                 x automatable share (1 - exception rate)
                 x (clean-instance person-hours - irreducible human time per instance)

annual value = recoverable h/yr x loaded hourly cost
             - annual upkeep (maintenance + rot)
             - annual cost to run (cost per instance x automated instances/yr)

verdict      = annual value against one-time build cost, and against the price of the
               best thing that already exists,
               plus value that isn't hours, sized separately, never summed in
```

A question that moves no term in that is a question you don't ask. Two traps: **never multiply people by elapsed time** (three people for two weeks is 120 person-hours, not 480), and **never put a blended average into the clean-instance term** (a 4-minute average across a 35% exception rate is not 4 minutes on the 65% you can automate — derive the clean-instance figure from the average and the exception time).

## How to run it

Work in rounds. The **frontier** is every question whose prerequisites are settled: what you can ask now without guessing at answers you haven't heard. Ask the whole frontier in one round, numbered, each with your recommended answer, then wait. Answers reshape the tree; recompute and ask the next round. A question that depends on another still open belongs to a later round.

```
❓ **Q1** - **<title>**: <body; offer brackets or choices wherever a number is wanted>

➡️ <your recommended answer, or your best guess plus one line of reasoning>
```

**Round one** is your one-sentence restatement as a plain sentence ending "correct me" (not a numbered question — it has no recommended answer), then exactly four: instances per year, person-hours per instance, who is waiting on the output, and whether something they already pay for or could buy already does this. Population, adoption, error cost and shape all wait. That fourth question ends sessions on the spot: dispatch a sub-agent on it immediately and ask the rest while it runs.

**Size gate.** Compute gross hours per year from round one and say the number out loud. Ten minutes a week for one person is about eight hours a year. Under 40 gross h/yr, cap the session at two more rounds and skip error cost, adoption and decision rule unless they raise them: a $400/yr case does not deserve seventeen questions. There's a ceiling too — above ~200 gross h/yr with a measured exception rate, a named owner and no buy option, stop interviewing value and move to shape and cost.

**When the spine doesn't apply.** If gross hours come out immaterial and the value they're describing is revenue, retention or a customer-facing capability, say so and stop running the spine — it will produce a real number and a meaningless one, and a meaningless number computed rigorously is worse than no number. Switch to the product-bet questions instead: who asked for this and out of how many, who named a price, what it costs per instance against what it sells for, and what comes off the roadmap to make room. The term that decides a product bet is willingness to pay, and there is no substitute for asking.

**Facts**, three kinds. In the environment, or on the market (files, repos, scripts, row counts, ticket history; what vendors already sell and roughly what they charge): **yours** — dispatch a sub-agent, search, and never ask. If you have no sub-agent, do it yourself between rounds. Only in the user's own records (their real exception rate, last month's volume): **a measurement, not a question** — name the column, the tag and the duration, and don't take memory instead. If they could measure it but won't, don't deadlock: keep their number, mark it `assumed`, put your own scepticism in the low column rather than theirs, say out loud which way the verdict flips if the count comes back worse, and name the measurement again at the end. Nobody has them (a private company's loaded rate): state a default, mark it `assumed`, move on, never block. The **judgments** are always theirs: put each one to them and wait.

**When they push to the build** — and they will — name what they're reaching for, park it, offer the trade: "That's build detail. The term that decides this isn't the four minutes, it's what's left after the automation runs. Two more rounds, then shape takes ten minutes and you get a better answer for having a size to fit it to." If they insist twice, do it their way but write shape as a conditional: *if the exception rate is under X, build this.* Never drop value silently.

## The ledger

Post the running arithmetic after every round. The user watches their own case get built, or collapse.

```
📊 **Ledger** (likely: **<N> h/yr** · **$<N>/yr**, payback **<N> weeks** — payback only once build cost has a row)

| term | low | likely | high | source |
| --- | --- | --- | --- | --- |
| instances / yr | 40 | 52 | 80 | stated |
```

- Tag every row `stated` / `derived` / `assumed` / `unknown`.
- Omit rows still entirely unknown rather than printing dashes: a ledger with no derived row isn't worth posting.
- Carry low/likely/high, but put a known term's single value in all three columns rather than manufacturing spread.
- If high ÷ low exceeds 3x on the bottom line, **the spread is the finding** — lead with it, not the likely column.
- Derived rows couple. When the low column compounds into something impossible — clean-instance time below the review floor, say — write that under the table, because the honest low is often zero.
- When something already on the market does this, give it its own row (`alternative $/yr`) and judge the build against that, not only against zero: a build that beats doing nothing can still lose to a quote.
- Never quietly swap a user's number for one you prefer. Disagree out loud and keep both.
- `payback = build cost ÷ annual value` (already net of upkeep and cost to run), in weeks under 26 and months above, and "never" when annual value is at or below zero in the low case.
- Money in the user's own currency, not the dollars these examples happen to use.

## The question bank

Roughly in dependency order. Ask what matters for *this* case, not all of it.

**The thing.** Walk me through the last time, start to finish: trigger, what you touched, where it ended. What's one unit (one invoice, one report, one onboarding)? Stable process, or does every instance differ?

**Instances per year.** How many last month, not "how often usually". Steady or bursty? Growing?

**Person-hours per instance.** How long did the *last* one take — hands-on, waiting on a system or a person, and the interruption tax? Several people on one instance means the sum of their hours, never elapsed calendar time. Waiting on a slow system isn't hands-on: ask whether they sit idle or tab away, because tabbing away makes it wall-clock rather than capacity, and it gets its own row outside the money. And whatever number they give you is a blend of the good instances and the bad ones — once you have the exception rate and the exception time, derive the clean-instance figure from them and put *that* in the spine.

**Exception rate.** What share need a judgment call no automation clears? The term people inflate most, and pushing on it means one thing: no numbers from memory. The first answer is routinely well under the truth. Send them to count last month against a tag.

**Irreducible human time.** On the instances the tool *does* handle, what must a human still do before the output is trusted or acted on — review, approve, sign? Price it per instance and subtract it in the spine; folded into the automatable share it vanishes.

**Loaded hourly cost.** Don't ask for this. Default to a Boston tech median of **$150,000 base** — 2026 aggregator and BLS-derived medians for the metro cluster around $130k–$160k — and compute `salary × 1.3 ÷ annual working hours`, roughly 2,000 in the US and nearer 1,700 across much of Europe, where 1.3 covers benefits only and 1.5 to 2.0 loads overhead too. That gives **~$98/hr**. Mark it `assumed`, carry a band 1.5x wide, then check whether the verdict moves at the top of that band.

If they supply a salary, a band or a rate, drop the default and use theirs, marked `stated`. If they've said they're somewhere other than Boston, adjust and say you have. If they're solo or an agency, use what an hour of their time bills or earns. Never leave this term unknown: it's what converts the case into money.

**Recapture.** If the hours came back, what specifically happens in them? Two non-answers: "nothing in particular", and "they'd go back to work that's already late". Both are relief from toil, which is real, and belongs in the unpriced column rather than the dollar one. An avoided hire is lumpy: divide recovered hours by a working year and state the FTE fraction before crediting any salary. Below ~0.7 FTE it does not avoid a headcount, and saying so is the honest move. That test bounds avoided-hire claims only: it never discounts recovered hours that have a named destination.

**Population and adoption.** Who else does this, the same way or their own version? "Would use it" means would run it or does the work today — recipients and stakeholders are an audience, not a population, so re-ask if they hand you one. Whose habits change, who explains it to them, and is it still positive at n=1?

**Cost of getting it wrong.** What happens today when someone errs, and who catches it? What would a confidently wrong output cost, and how far would it travel before anyone noticed?

**Value that isn't hours.** Turnaround somebody is waiting on, throughput unreachable by hand, revenue or deals unblocked, audit evidence, risk removed, a thing impossible today at any effort. Never summed into the dollar figure — but **size each one to an order of magnitude**, or the reader gets rigour on the term that doesn't matter and a bullet point on the term that does. "$400k parked in security review, and a tenth of it closing three weeks sooner is X" is a bound. "Faster deals" isn't. If they genuinely can't price it, bound it yourself and say that the bound is yours.

**Upkeep and rot.** How often do upstream systems, forms or formats change, and what does each break cost to fix? Who owns it in twelve months, on whose credentials, and what happens the week after the builder leaves? An owner who is a phone number is not an owner. Under ~12 instances a year, say out loud that it rots between uses: add a re-break row of (hours to make it work again) × (instances/yr) and subtract it from recovered hours.

**Cost per run.** Does running it cost money per instance — inference, API calls, per-seat licences, compute? This is not upkeep: it scales with usage, not with time. Get the cost per instance, multiply by the instances it actually runs on, and subtract it in the spine as its own line. If anything is sold on top of this, put cost per unit against price per unit and say the gross margin out loud before anything else.

**Alternatives, revisited.** You asked in round one; now hold them to it. "Probably too expensive" without a quote is not an answer, and the quote is cheaper than the build — get it before estimating build cost. Could the process be deleted, or simplified so there's less of it to automate? What happens if you do nothing for six months?

**Shape.** *What are you picturing?* Hand-run script, scheduled job, internal page, deciding agent? What triggers it, where does it run and as whom, where's the human in the loop, and what does v1 deliberately not do?

**Build cost.** Who builds it, at what rate, instead of what? How long to usable, not finished? Estimate the thin slice, not the vision. Get the builder's rate separately from the user's — when every value term is measured, build duration becomes the dominant term. First estimates on anything touching someone else's API tend to run two to three times long.

**Decision rule.** What payback do they need, and over what horizon? Ask for their number before you offer a threshold. Payback beyond twelve months is not a failure when the asset's life is long, the volume is contractual rather than hopeful, and the value terms are measured rather than assumed: fifteen months on a measured four-year asset is a better build than six weeks on a guessed one. Who signs off, if anyone? What would change your mind?

## Rules

- The thresholds in this skill — 40 and 200 gross h/yr, ~12 instances, ~0.7 FTE, 2-3x on estimates — are rules of thumb, not findings. Move them to fit the context you're in, and say when you have.
- Numbers, not adjectives. "A lot" isn't an answer. Offer brackets to react to — "5, 50, or 500 a year?" — because people reject a wrong bracket far better than they produce a right number.
- Anchor to the last real occurrence. Memory of averages is fiction.
- People overstate frequency and population, and understate the exception rate and the review that follows it. Probe all four.
- Name the dominant term: which single assumption the case rests on, and what would collapse it.
- Be willing to land on don't build. The usual honest verdicts: one person and ten minutes a month; the process shouldn't exist; the time isn't recapturable; exceptions plus the review floor eat the gain.
- Don't act on any of it until they confirm you've reached a shared understanding.

## The output

Done when the frontier is empty: every spine term has a value and a source, nothing load-bearing silently assumed. Then, short, in this order:

1. **Recommendation** — exactly one of: build now; build this thin slice (name it); build this thin slice gated on one measurement (name it and its duration); buy this instead (name the quote to get first); don't build (name what to do instead); not enough signal (name the one thing to measure, and for how long).
2. **The thing** — one paragraph, their words where possible.
3. **The arithmetic** — final ledger, multiplication shown, low/likely/high, payback.
4. **What the case rests on** — the most sensitive assumption, and the cheapest way to test it this week.
5. **Value that isn't hours** — listed, each bounded to an order of magnitude, none of it summed in.
6. **Cost** — build range, upkeep including rot, cost per run, named owner. "No owner" is a finding, not a blank.
7. **Thin slice** — the smallest thing worth shipping, and what it deliberately leaves out.
8. **Kill criteria** — what you'd see in 90 days that means stop.
9. **Assumptions and unknowns** — every `assumed` and `unknown` row, who settles it, how fast.

Both escape hatches get a short form. Under the size gate, write sections 1, 3 and 4 only. When the spine doesn't apply, write 1, 4, 5 and 9, with the product-bet numbers in 5 and no ledger at all.

Keep it in chat by default; offer a doc or artifact only if they need to send it to someone.

---

The round-and-frontier interview mechanic is adapted, with thanks, from Matt Pocock's `grilling` skill: [github.com/mattpocock/skills](https://github.com/mattpocock/skills), MIT licensed.
