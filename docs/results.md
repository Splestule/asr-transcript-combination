# Results and limits

These are saved experiment outputs, not a fresh rerun. WER is total word edits
divided by total reference words; it is not mean utterance WER. Unless stated
otherwise, the tables use the repository's legacy text normalisation.

## Whisfusion confirmation

[Complete output](../results/confirm_eval_nogpt.txt), produced by
`tools/confirm_eval.py` without GPT-2 features (`--gpt_configs none`).
The [pre-registration](confirm_prereg.md) records the protocol and deviations.

The primary protocol evaluates 2,143 common utterances from shard s02 of 11 sets.
Three-fold cross-validation trains on s00 of the sets outside the held-out fold;
the two LibriSpeech development sets always train. Features, voting parameters
and neighbour-word statistics are fitted on training data. Numbers are means
over seeds 0, 1 and 2 of the fitting procedure, not three independent ASR models.

| Flat K=15 | Corpus WER |
|---|---:|
| Confidence selection | 28.46% |
| MBR selection | 26.87% |
| Tuned ROVER | 23.39% |
| iROVER-style baseline | 23.02% |
| Learned combiner (`final` in the output) | 22.64% |

Against tuned ROVER, the per-seed improvement is 0.69–0.80 WER points;
the paired 95% bootstrap intervals exclude zero on every seed.
The iROVER-style comparison is this repository's boosted-stump baseline, not
a claim of reproducing every detail of the original iROVER system.

Tree-early K=8 gives 23.33% WER versus flat K=8's 23.16%, with reported
encoder-plus-decode time of 0.370 versus 0.558 seconds per utterance on the run's
GPU. Timing excludes auxiliary acoustic scoring and composition. The overall
pre-registered non-inferiority hypothesis H2 **failed**: its condition had to
hold at every tested K and seed. H1, H3 and H4 passed. This no-GPT output does
not evaluate H5 or H6; absence of those tests is not a pass.

Whisper-small greedy reaches 14.46% WER on the same saved confirmation set.
The claim is an improvement to Whisfusion's candidate use, not the strongest
available speech-recognition system.

## Drax

[Complete output](../results/drax_eval.txt), produced by `tools/drax_eval.py`.
The combiner is refitted without Whisfusion-specific acoustic features; this
is not zero-shot transfer of the Whisfusion combiner.

| K=16 | Tuned ROVER | iROVER-style | Learned combiner |
|---|---:|---:|---:|
| English: 4,400 utterances, 11 sets | 12.92% | 12.90% | 12.97% |
| FLEURS: 1,614 utterances, six languages | 8.79% | 8.33% | 8.36% |

English uses set-level cross-validation on s01+s02, trained on s00. FLEURS
holds one language out for scorer fitting; that language's s00 still supplies
its neighbour-word statistics. The FLEURS comparison is therefore not fully
language-unseen. The saved FLEURS section reports one seed.

## Earlier campaign

The [campaign report](../results/campaign-report.md) and
[design](campaign.md) cover decoder-family controls, diversity and compute.
Its data splits and candidate counts differ from the confirmation above;
do not combine the tables as if they were one evaluation.
