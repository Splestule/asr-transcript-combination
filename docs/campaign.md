# Candidate-generation campaign

The original campaign compares Whisfusion and Drax with autoregressive Whisper
and Parakeet-CTC controls. Data covers LibriSpeech, Open ASR Leaderboard sets,
six FLEURS languages, SLR83 accents and a synthetic-noise ladder.

Utterances are filtered to 0.8–28 seconds and 1–75 reference words, then ordered
by a hash of their ID. Shards therefore identify the same utterances across
decoders. LibriSpeech development splits are used for tuning.

## Run

`notebooks/campaign.ipynb` embeds the current campaign sources and pins its
upstream model repositories. It runs one worker per GPU, saves candidates and
provenance, then analyzes the outputs. Its default budget is about 11 hours;
review the configuration before enabling an accelerator.

Regenerate the notebook after changing source code:

```bash
uv run python tools/build_campaign_notebook.py --out notebooks/campaign.ipynb
```

For an existing campaign environment, the command-line equivalent is:

```bash
PYTHONPATH=src python -m campaign.prep_data --data /path/to/campaign_data --only ls-dev-clean
PYTHONPATH=src python -m campaign.run --config /path/to/config.json \
  --root results_latest_run/campaign --data /path/to/campaign_data
```

The campaign configuration specifies upstream checkout paths, decoder arms,
datasets and time limits. See `src/campaign/config.py`; the notebook constructs
a complete configuration. Preparing every dataset can download substantial data.

## Outputs

- `dumps/` or `dumps.tar`: candidate transcripts and confidences.
- `analysis/`: per-utterance edit counts and parameter grids.
- `results/`: aggregate WER, paired comparisons, timings and a report.
- `state/`, manifests and logs: input and execution provenance.

CPU campaign aggregation additionally needs `uv sync --locked --group campaign`.
Run `campaign.analysis` and `campaign.aggregate` with `PYTHONPATH=src` against an
existing campaign root; inspect their `--help` before selecting inputs.

The public checkout keeps a [short historical summary](../results/campaign-report.md).
The complete original artifacts remain in Git history at revision `92913ae`;
they are not part of a current clone's working tree. The later confirmation
uses a different protocol: [results](results.md), [pre-registration](confirm_prereg.md).
