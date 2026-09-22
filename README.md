# business-case

A Claude Code skill that interrogates you about a workflow automation or a piece of software **before** you build it, does the arithmetic out loud, and ends in a verdict you can defend to someone holding a budget.

It is not a scoping tool. It is built to answer one question — *does this deserve to exist* — and it is willing to answer no.

## What a session looks like

You say "should I automate our invoice reconciliation?" and instead of a plan, you get a round of questions with recommended answers attached:

```
❓ Q1 - Instances per year: How many invoices came through last month, not
a typical month? 100, 1,000, or 10,000?

➡️ Guessing ~1,000/month, steady.

❓ Q2 - Person-hours per instance: How long did the last one take, hands-on,
and how much of that was waiting on someone else?

➡️ Guessing 5 min hands-on.
```

You answer; it posts a running ledger; the ledger either builds your case or collapses it in front of you:

```
📊 Ledger (likely: 300 h/yr · $13,478/yr, payback ~111 weeks)

| term                       | low   | likely | high   | source                  |
| -------------------------- | ----- | ------ | ------ | ----------------------- |
| invoices / yr              | 14400 | 14400  | 15840  | stated                  |
| exception rate             | 40%   | 35%    | 25%    | stated (412/1200 counted)|
| min / clean invoice        | 2.00  | 2.92   | 3.67   | derived from the average |
| irreducible review, min    | 1.5   | 1.0    | 1.0    | stated                  |
| hours / yr                 | 72    | 300    | 481    | derived                 |
```

The naive version of that case was 960 h/yr and $43k. Two questions took 69% of it off the table.

## Install

As a plugin from a marketplace you control:

```bash
claude plugin marketplace add Levison/business-case-skill
```

```bash
claude plugin install business-case@levison
```

Or drop the skill in by hand:

```bash
git clone https://github.com/Levison/business-case-skill
cp -r business-case-skill/skills/business-case ~/.claude/skills/
```

Skills are read at startup, so restart Claude Code afterwards. Then just describe something you're thinking of building, or say "grill me on this build".

## How it works

Every question has to move a term in one equation:

```
recoverable h/yr = instances/yr
                 x automatable share (1 - exception rate)
                 x (clean-instance person-hours - irreducible human time per instance)

annual value = recoverable h/yr x loaded hourly cost
             - annual upkeep (maintenance + rot)
             - annual cost to run (cost per instance x automated instances/yr)
```

A question that moves no term doesn't get asked. That constraint is most of the skill.

The rest is a set of traps it has been beaten into avoiding, each of which was worth a large fraction of a real case in testing:

- **Blended averages.** A 4-minute average across a 35% exception rate is not 4 minutes on the 65% you can automate.
- **People × elapsed time.** "Three people for two weeks" is 120 person-hours, not 480.
- **Review floors.** The minute someone must spend checking the output before it's trusted is a separate subtraction, not a percentage — folded into "automatable share" it disappears.
- **Rot.** A tool used twice a year is broken every time you open it. Under ~12 instances a year, revival hours come off the top.
- **Unrecapturable savings.** "I'd catch up on Slack" and "they'd go back to work that's already late" are relief from toil, not money.
- **Phantom headcount.** 300 recovered hours is 0.15 FTE. It does not avoid a hire, whatever anyone wants to believe.
- **Audiences mistaken for populations.** People who *read* the weekly report are not users of the tool that generates it.
- **The thing you already pay for.** This is the question that ends sessions, so it's asked in round one and a sub-agent goes looking while the rest of the round runs.

## What it deliberately doesn't do

- It doesn't design. Value first, shape second, and if you push it to the build twice it will write the shape as a conditional rather than pretend the value question was settled.
- It doesn't price revenue bets. If your hours come out immaterial and the real case is retention or willingness to pay, it says the spine doesn't apply and switches questions instead of computing a rigorous, meaningless number.
- It doesn't sum non-hour value into the dollar figure — but it does force each item to an order-of-magnitude bound, because "faster deals" next to "$8,840/yr net" is not a decision.

## Output

A nine-part case ending in exactly one of: build now · build this thin slice · build this thin slice gated on one measurement · buy this instead · don't build · not enough signal. Plus what the case rests on, the cheapest way to test that this week, and kill criteria for 90 days out.

## Defaults you may want to change

The skill assumes a **Boston tech median of $150,000 base** and computes `salary × 1.3 ÷ 2,000` ≈ $98/hr loaded when nobody supplies a rate. It uses a rate you give it in preference to that, always. If you're somewhere else, edit the **Loaded hourly cost** entry in `skills/business-case/SKILL.md`.

Its thresholds — 40 and 200 gross h/yr, ~12 instances, ~0.7 FTE, 2–3x on build estimates — are rules of thumb, not findings. Move them.

## Credit

The round-and-frontier interview mechanic is adapted, with thanks, from Matt Pocock's [`grilling`](https://github.com/mattpocock/skills) skill, MIT licensed. If you want a general-purpose plan interrogator rather than a business-case one, install his instead — it's the better tool for that job.

## License

MIT. See [LICENSE](LICENSE).
