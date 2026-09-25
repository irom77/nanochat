# Local nanochat autoresearch log

These experiments use nanochat's tokenizer, ClimbMix data, model code, and
evaluation scripts. They are single-GPU fixed-step screening runs and are not
official Time-to-GPT-2 leaderboard results. Training time is measured by
nanochat's `total_training_time`; GPU setup and evaluation scope are recorded
explicitly.

## 2026-09-25

| train commit | eval commit | GPU | steps | warmup | training time | val_bpb | CORE | peak memory | result |
|---|---|---:|---:|---:|---:|---:|---:|---|
| `d05bd40` | `6af5ebd` | 1x H100 80GB HBM3 | 150 | 40 | 141.41 s | 1.249343 | — | 28,817 MiB | baseline; full CORE stopped before first task completed |
| `d05bd40` | `6af5ebd` | 1x H100 80GB HBM3 | 150 | 15 | 139.14 s | 1.297368 | — | 28,817 MiB | discarded; worse BPB |

Evaluation used a fixed `--split-tokens=524288` BPB pass. The full CORE pass
was attempted with nanochat's evaluator but stopped during the first large
task to stay within the single-GPU screening budget; neither run is eligible
for a ranked CORE leaderboard row.

Commands:

```bash
python -m scripts.base_train --depth=12 --num-iterations=150 \
  --device-batch-size=32 --total-batch-size=524288 \
  --eval-every=-1 --core-metric-every=-1 --sample-every=-1 --save-every=-1

python -m scripts.base_train --depth=12 --num-iterations=150 \
  --device-batch-size=32 --total-batch-size=524288 --warmup-steps=15 \
  --eval-every=-1 --core-metric-every=-1 --sample-every=-1 --save-every=-1
```
