# Reproduction

Run commands from the repository root with Python 3.12+. `uv.lock` pins the
analysis environment. The project is a collection of research scripts; it does
not install a Python package or a console command.

## CPU analysis

```bash
uv sync --locked
uv run python src/analyze_compose.py results/example.jsonl --n_boot 200 --json /tmp/asr-example.json
```

`results/example.jsonl` contains three synthetic transcript pools. It exercises
selection, word voting, oracle bounds and bootstrap comparisons. Its scores
are a demonstration, not a measured research result. For reporting on real
candidate dumps, use more bootstrap samples (the default is 4,000).

The JSONL format has one utterance per line, with `reference` and `candidates`;
each candidate needs `text` and `avg_conf`. Real dumps can also include
per-word confidence, token information, timings and dataset metadata.
`requirements-analysis.txt` is the alternative for a pip-managed environment;
`uv sync --locked` is the tested, pinned setup.

## GPU decoding

Use a CUDA environment and activate the project environment, or run the setup
through uv. `setup.sh` installs GPU dependencies and downloads Whisfusion,
checkpoints (about 1.9 GB) and LibriSpeech test-clean (about 331 MB).
It does not start training or evaluation.

```bash
uv run bash setup.sh /work
export PYTHONPATH="$PWD/src:/work/Whisfusion/src:$PWD/tools"
uv run python src/selftest.py --work /work
uv run python src/dump_candidates.py --source librispeech --path /work/data/LibriSpeech/test-clean \
  --base_model /work/ckpt/mdm_safetensors/mdm-170M-100e18-rsl-0.01.safetensors \
  --adapter /work/ckpt/whisfusion_stage2_decoder.pt \
  --out results_latest_run/test-clean.jsonl --resume
uv run python src/analyze_compose.py results_latest_run/test-clean.jsonl
```

The compatibility code uses fp16 and SDPA on T4/P100 rather than FlashAttention 2.
The setup pins a new Whisfusion clone to the confirmation revision; it leaves an
existing clone untouched. GPU dependencies are installed separately from the
CPU lockfile. GPU decoding has not been rerun as part of the repository cleanup.

For Kaggle, `notebooks/prepare_data.ipynb` downloads reusable data without an
accelerator. `notebooks/campaign.ipynb` is a self-contained GPU campaign;
configure its time budget before running it. The campaign prepares its own
multi-dataset manifests. Stop unused interactive GPU sessions to avoid spending
quota twice. [Campaign design and outputs](campaign.md).

## Fit the learned combiner

The saved headline output is [confirm_eval_nogpt.txt](../results/confirm_eval_nogpt.txt).
It needs an acoustic-enriched confirmation cache, which is not distributed in
this checkout. Earlier campaign caches are not substitutes for that input.

Given a cache produced by `tools/run_confirm.sh`:

```bash
uv run python tools/confirm_eval.py --cand /path/to/cand_audio_confirm.pkl \
  --gpt_configs none --work results_latest_run/confirm-cache
```

To generate the inputs, prepare the campaign datasets and run
`bash tools/run_confirm.sh /path/to/campaign_data` in the GPU environment.
The runner launches decoding, acoustic scoring, Whisper reference decoding and
evaluation; it may consume a full GPU session. `STEPS` selects stages. Its default
evaluation includes GPT-2; use `--gpt_configs none` in the CPU evaluation command
to match the saved no-GPT summary. Only load pickle caches you trust.

The [pre-registration](confirm_prereg.md) records input reuse and protocol
deviations. Regenerating inputs does not guarantee byte-identical historical
results; original GPU dependencies and input provenance also matter.

Drax uses campaign candidate dumps, refitting without Whisfusion-specific
acoustic features:

```bash
uv run python tools/drax_eval.py --dumps_root /path/to/campaign/dumps
```

The full dumps and intermediate caches are not in the current public checkout.
The original campaign artifacts remain in repository history at `92913ae` under
`results/campaign-2026-09-20/`; retrieve them from that revision to rerun the
historical Drax evaluation. See [results and limits](results.md) for scope.
