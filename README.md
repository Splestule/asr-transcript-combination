# ASR transcript combination

Combines alternative speech-recognition transcripts word by word instead of
selecting one complete transcript. A learned scorer uses confidence, neighbouring
words and acoustic features to choose between aligned alternatives.

My experiments use [Whisfusion](https://github.com/taeyoun811/Whisfusion) and
[Drax](https://github.com/aiola-lab/drax), comparing candidate selection, ROVER
voting and a learned word combiner. The speech models and ROVER are prior work.

## Results

Whisfusion, 15 candidates, 2,143 held-out utterances across 11 evaluation sets.
Corpus word error rate (WER; lower is better), averaged over three fitting seeds:

| Method | WER |
|---|---:|
| Original confidence selection | 28.46% |
| Minimum Bayes risk selection | 26.87% |
| Tuned ROVER | 23.39% |
| iROVER-style baseline | 23.02% |
| Learned word combiner | **22.64%** |

Training and evaluation sets are separated by set-level cross-validation.
On Drax, the learned combiner does not consistently beat tuned ROVER.
[Protocols, limitations and saved results](docs/results.md).

## Try it

With Python 3.12+ and [uv](https://docs.astral.sh/uv/):

```bash
uv sync --locked
uv run python src/analyze_compose.py results/example.jsonl --n_boot 200
```

The example uses three synthetic candidate pools to demonstrate the analysis;
it is not benchmark evidence. No GPU or model download is needed.
[Reproduction instructions](docs/reproduce.md) cover GPU decoding and fitting
the learned combiner.

## Code

- `src/`: decoding, alignment, voting and campaign runners.
- `tools/`: learned scoring and evaluation.
- `notebooks/`: data preparation and the Kaggle campaign.
- `results/`: selected summaries and the runnable example.
