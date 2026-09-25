# Local comparison leaderboard

This is a local, non-official comparison. Official nanochat rows are copied
from `README_original.md`; local screening rows use 1x H100 and fixed-step
training, so their time is not comparable to official 8x H100 Time-to-GPT-2
time. A dash in CORE means the full CORE evaluation was not completed.

## Official snapshot

| # | time | val_bpb | CORE | Description | Date | Commit |
|---|---:|---:|---:|---|---|---|
| 0 | 168 h | — | 0.2565 | Original OpenAI GPT-2 checkpoint | 2019 | — |
| 1 | 3.04 h | 0.74833 | 0.2585 | d24 baseline, slightly overtrained | Jan 29 2026 | `348fbb3` |
| 2 | 2.91 h | 0.74504 | 0.2578 | d26 slightly undertrained +fp8 | Feb 2 2026 | `a67eba3` |
| 3 | 2.76 h | 0.74645 | 0.2602 | bump total batch size to 1M tokens | Feb 5 2026 | `2c062aa` |
| 4 | 2.02 h | 0.71854 | 0.2571 | change dataset to NVIDIA ClimbMix | Mar 4 2026 | `324e69c` |
| 5 | 1.80 h | 0.71808 | 0.2690 | autoresearch round 1 | Mar 9 2026 | `6ed7d1d` |
| 6 | 1.65 h | 0.71800 | 0.2626 | autoresearch round 2 | Mar 14 2026 | `a825e63` |

## Local screening rows — not ranked

| # | time | GPU | val_bpb | CORE | Description | Date | Commit | Status |
|---|---:|---|---:|---:|---|---|---|---|
| S1 | 141.41 s training | 1x H100 80GB | 1.249343 | — | d12 baseline, 150 steps | Sep 25 2026 | train `d05bd40`, eval `6af5ebd` | incomplete CORE |
| S2 | 139.14 s training | 1x H100 80GB | 1.297368 | — | d12, warmup steps 40→15 | Sep 25 2026 | train `d05bd40`, eval `6af5ebd` | discarded |
