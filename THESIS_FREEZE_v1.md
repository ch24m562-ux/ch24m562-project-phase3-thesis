# THESIS_FREEZE_v1.md
Internal reference only — not part of the thesis. This is the canonical
"constitution" every sentence in Discussion, Conclusion, and the final
whole-thesis pass should conform to. If a draft sentence contradicts
anything here, the sentence is wrong, not this document — this document
is derived directly from verified source files (`master_summary.csv`,
`hparams.yaml`, and the actual `.py` implementations), not from any
earlier chapter draft.

## Canonical statistics
- **Paired seed-level analysis** — same seeds evaluated under every policy;
  paired $t$-test on within-seed differences, effect size Cohen's $d_z$.
  Never Welch's / independent-samples / $n=3$ as a current method (that was
  an earlier, superseded pilot phase — fine to mention as historical
  context, never as the method actually used for reported conclusions).
- **10 training seeds**: 42, 123, 777, 7, 13, 21, 99, 314, 500, 999.
- **10 evaluation episodes** per seed (not 30 — that's a stale `hparams.yaml`
  default never actually used; not 5 — that's `evaluate.py`'s unrelated
  CLI default).
- **10 sites** — all ITU/Zindi sites, every policy, every scenario (full
  factorial, not policy-specific coverage).
- **Four logistics scenarios**: Normal (24h), Delayed (48h), Monsoon (72h,
  eval-only), Extreme (336h, eval-only).
- **9 core policies**: B0, B1, MPC, Oracle-MPC, TrackB, A5, A6, A7, RLInv —
  36,000 core rollouts. Two additional exploratory policies
  (`mpc_forecast`, `multi`) exist in the data (44,000 rollouts total) but
  are excluded from the primary comparison.
- **H1 primary comparator is A6, not TrackB.** TrackB is confounded
  (differs in both ordering *and* dispatch architecture); A6 holds
  dispatch architecture constant. H1 is scenario-dependent — RLInv wins
  Normal/Extreme, A6 nominally wins Monsoon, no difference Delayed. Never
  write "H1 confirmed" or "H1 rejected" as a single verdict.
- **H2 evidence is A6-vs-TrackB** (ordering held constant, masking varies),
  large effect at Monsoon/Extreme only. RLInv-vs-A7 (masking varies,
  ordering already joint) is a documented near-null result — mention both,
  don't imply masking explains the entire RLInv-TrackB gap.
- **H3 (A5, inventory observability) is not statistically significant**
  at any scenario; closest at Extreme, $p=0.058$. Don't call it
  "supported" or "not supported" — state the evidence directly.

## Canonical terminology
- **11-dimensional observation** (12D for RLInv-DA with the EWMA channel
  added). Never "9D"/"9-dimensional." The two channels beyond the original
  9 are `hours_since_order_n` (always active) and `delivery_remaining_n`
  (present but zero in the main geometric-scenario campaign).
- **B0 = fixed-threshold inventory policy using uncalibrated inventory
  thresholds.** Never "fixed-interval ordering."
- **B1/TrackB/A6/MPC/Oracle-MPC share a site-calibrated $(s,S)$ policy** —
  reorder point covers estimated mean consumption over the expected
  delivery lead time, subject to clipping limits. Never an additive
  "48-hour safety buffer" — no such term exists in the implementation.
- **A5 removes five channels**: inventory level, pending-delivery flag,
  pending quantity, elapsed time since ordering, remaining delivery-time
  channel. Never "two observations."
- **RLInv-DA is an exploratory extension** (Comments 9/10), not part of
  the primary contribution. Neutral/mixed result, reported honestly as a
  genuine finding, not a shortfall.
- **Forecast-MPC (`mpc_forecast`) was implemented and evaluated but is
  not part of the primary comparison** — it did not change the
  qualitative conclusions drawn from the persistence-forecast MPC
  baseline. Don't present it as if it were part of the headline result.
- **MPC horizon = 24h** (24 binary dispatch variables — this is a modelled
  fact; don't cite an unbenchmarked runtime figure like "~5ms" unless a
  real benchmark log exists). **Oracle-MPC horizon = 360 steps** (full
  episode, receding), perfect-information forecast.

## Reviewer comment closure status (final, agreed)
| Reviewer | Status | Where Addressed |
|---|---|---|
| R1 | Fully addressed | Results (primary comparison, lead-time sensitivity, behavioural analysis) |
| R2 | Fully addressed | Entire Results chapter |
| R3 | Fully addressed | Methodology (coefficient rationale) + Results (reward sensitivity) |
| R4 | Fully addressed | Rebuilt figures throughout |
| R5 | Fully addressed | Results + Discussion |
| R6 | Fully addressed | Results (Lognormal, Weibull) + Discussion |
| R7 | Fully addressed | Captions, labels, tables |
| R8 | Fully addressed | Methodology baseline table + consistent terminology |
| R9 | Fully addressed (within stated modelling scope) | Methodology §4.7 (explicit supply-chain assumptions), Results (lead-time and RLInv-DA analyses), Discussion/Future Work (richer logistics and multi-agent extensions) |
| R10 | Fully addressed | Results (RLInv-DA) + Discussion/Conclusion |
| R11 | Fully addressed | Whole thesis, canonical statistics |

## Open items carried forward (not yet resolved — do not silently resolve
## in Discussion/Conclusion; flag if touched)
1. **Appendix relocation** — Experimental Execution was compressed inline
   rather than moved to an appendix as originally suggested. Deferred to
   the final pass by explicit agreement (2026-07-12 session). Appendix
   files already exist in the repo (`appn-general-notes.tex`, etc.) if
   this is picked up later.
2. **Workflow-diagram redundancy check** — three separate workflow-style
   figures now exist across the thesis (Problem Formulation's system
   diagram, Methodology's RLInv per-step loop + chapter roadmap +
   evaluation-protocol flow, Experimental Execution's pipeline figure).
   Current assessment: each answers a different question (mathematical
   formulation vs. algorithm workflow vs. experimental workflow) and no
   redundancy has been found — but flagged for one more explicit check
   during the final whole-thesis pass, per author's request.
3. **Conclusion/Discussion rewrite** — complete as of this update
   (2026-07-12). `chap-conclusion.tex` is now titled "Discussion and
   Conclusion" and fully rewritten against the canonical facts above;
   nothing in it should be treated as stale Phase 2 content anymore.

## Provenance
Every fact above was independently re-derived from source during this
session (`master_summary.csv`, `hparams.yaml`, `mpc_policy.py`,
`s_S_policy.py`, `rule_based.py`, `telecom_env.py`, `a6_env.py`,
`drop_inventory_obs.py`, `train_ablation_a7.py`), not copied from any
earlier thesis draft. If any of the above is ever found to be wrong, the
error is here, in this document — check the source files listed, not an
older chapter draft, to resolve the disagreement.
