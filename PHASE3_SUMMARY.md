# PHASE3_SUMMARY.md
Internal reference only — not for submission. Written for viva prep: a
concise, evidence-based answer to "what changed since Phase 2, and why?"

## The one-sentence version
Phase 2 established the idea (joint inventory-aware RL) on a small pilot
(3 seeds, 2 scenarios, RL-only baselines). Phase 3 tested it properly
(10 seeds, 4 scenarios, classical optimisation baselines added) — and the
honest result is more conditional than the pilot suggested, which is
itself the main scientific finding.

## What actually expanded

| Area | Phase 2 | Phase 3 |
|---|---|---|
| Training seeds | 3 (42, 123, 777) | 10 (+7, 13, 21, 99, 314, 500, 999) |
| Logistics scenarios | 2 (Normal, Delayed) | 4 (+ Monsoon, Extreme — eval-only OOD stress tests) |
| Statistical method | Welch's independent-samples t-test | Paired seed-level t-test + Cohen's $d_z$ |
| Baseline set | RL-only decomposition (TrackB) | + MPC, Oracle-MPC (optimisation-based) |
| Core policies evaluated | ~7 | 9 (36,000 rollouts) + 2 exploratory (44,000 total) |
| Cross-site check | Limited | All 10 sites, every policy, every scenario |

## Statistical methodology correction
Phase 2's Welch's-test-with-n=3 was underpowered and (for H1 specifically)
gave misleadingly clean-looking numbers (p=0.042, d=3.67) that didn't
survive replication. Phase 3's paired seed-level design directly compares
each policy against itself across the *same* 10 seeds, which is both more
statistically appropriate (removes between-instance variance common to
both arms) and more powerful. This is a methodology fix, not a result that
got worse — it's the same question asked with a design that actually
answers it.

## MPC / Oracle-MPC addition — why it matters
Without an optimisation-based baseline, "RLInv beats TrackB" could mean
RLInv learned better ordering, better dispatch, or both — no way to tell
which. Adding MPC (persistence-forecast, H=24) and Oracle-MPC
(perfect-information, full-episode horizon) lets the thesis show: even a
policy with *perfect* forecasting and dispatch optimisation doesn't close
the gap to RLInv under logistics stress — so the advantage is
attributable to ordering, not just dispatch quality. This is the
single most important structural addition in Phase 3.

## The H1 finding changed — and that's the headline result, not a weakness
- Phase 2 pilot: "joint learning always wins."
- Phase 3 (10 seeds, paired): significant under Normal and Extreme, no
  difference under Delayed, nominally favours the classical-ordering
  comparator (A6) under Monsoon.
- **This is not a weaker result — it's a more useful one.** Large-scale
  replication refined an over-generalised pilot finding into a precise,
  scenario-dependent statement about *when* joint optimisation helps.

## Reward sensitivity
Added: systematic sweep of every reward coefficient (20-fold range for
the unmet-load penalty $\lambda$), plus discount factor. Result: EENS
varies by only 3.32 kWh across the entire sweep at the Extreme scenario —
the primary comparison is not an artefact of a specific weighting choice.

## Distributional robustness
Added: lognormal ($\sigma$=0.5, 0.8) and Weibull ($k$=2) delivery-delay
models, tested against the geometric training distribution. Policy
rankings preserved across all three; RLInv is *less* sensitive to this
modelling choice than the classical baseline, not more.

## Observation-space correction
Found and fixed a real documentation error carried from Phase 2: the
observation space was described as 9-dimensional everywhere (Problem
Formulation, Methodology, even stale comments in the codebase itself).
The actual implemented space is **11-dimensional** — two active/inert
channels (`hours_since_order`, `delivery_remaining`) were never
documented. RLInv-DA adds a 12th (EWMA delay estimate). This was a
documentation gap, not an implementation change — the code was always
right; the thesis text wasn't.

## Baseline clarification
Two similar corrections, same root cause (old text never verified
against the actual source code):
- **B0** was described as "fixed-interval ordering" — the code shows it's
  actually threshold-based (same structural form as B1, just uncalibrated
  thresholds). Fixed everywhere.
- **The shared $(s,S)$ rule** was described with a "+48-hour safety
  buffer" that doesn't exist in the implementation — actual formula is
  proportional to mean-lead-time demand with a clipping range. Fixed
  everywhere, consolidated into one authoritative Methodology note.

## RLInv-DA (exploratory extension, Comments 9 & 10)
Investigated whether explicit delay-history tracking (EWMA under a
persistent two-regime supplier model) improves on RLInv further. Verified
working mechanism, no consistent improvement (5 better / 11 worse / 20
similar across 36 comparisons). Reported as a genuine null result, not
hidden — the interpretation is that RLInv's existing state already
captures most of what explicit delay-tracking would add.

## Reviewer responses (Phase 1 review, R1–R11)
All 11 items closed with verified evidence, not just claimed closed —
full table in `THESIS_FREEZE_v1.md`. The one that took real new work: R9
(supply-chain realism) got a dedicated Methodology subsection consolidating
scope/assumptions that had previously only been scattered across chapters.

## What this thesis's contribution actually is, after all the corrections
Not: "reinforcement learning is better than classical optimisation."
Is: joint optimisation of replenishment and dispatch provides measurable
value specifically under uncertainty that classical rule-based
coordination can't easily represent — and that value is conditional on
the logistics regime, which is a stronger, more defensible claim than an
unconditional one would have been.
