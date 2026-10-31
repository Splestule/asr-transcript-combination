# Original candidate-generation campaign

Historical experiment, preceding the held-out learned-combiner confirmation.
Two Kaggle sessions covered 146 jobs, 28,900 utterance decodes and about 695,000
candidate transcripts across parallel, autoregressive and CTC decoders.

Pooled improvement in WER points; positive favours ROVER. Brackets are the
reported random-effects confidence intervals.

| Comparison | Whisfusion (14 sets) | Drax (19 sets) |
|---|---:|---:|
| ROVER versus confidence selection | +4.43 [3.60, 5.26] | +1.73 [1.37, 2.08] |
| ROVER versus MBR selection | +2.77 [2.17, 3.37] | +0.52 [0.30, 0.74] |

The autoregressive and CTC controls did not show the same consistent gains.
Candidate disagreement, rather than decoder family alone, tracked the benefit
of combination. These results do not establish universal superiority of a
learned combiner, and use different inputs from the later confirmation.

Original report, raw candidates, manifests, logs and derived tables are retained
in Git history at `92913ae:results/campaign-2026-09-20/`.
[Campaign code and setup](../docs/campaign.md); [current results](../docs/results.md).
