# Training Throughput Bench

Best-known **SFT training throughput** per model × accelerator, with the exact recipe and cost. Higher tok/s/GPU and lower $/M tokens are better.

**Live leaderboard:** https://leofan-i.github.io/TrainingThroughputBench/

## Qwen3.5
### Qwen3.5-122B-A10B
| Accelerator | GPUs | Best recipe | tok/s/GPU | $/M tokens |
|---|---|---|---|---|
| H100 | 48 | `TP4/PP6/EP8, mt16384, flex dispatcher` | 980 | $1.95 |
| H200 | 32 | `TP1/PP4/EP8, mt8192` | 2345 | $0.94 |
| B200 | 16 | `TP1/PP4/EP4, mt8192` | 2563 | $1.54 |

### Qwen3.5-397B-A17B
| Accelerator | GPUs | Best recipe | tok/s/GPU | $/M tokens |
|---|---|---|---|---|
| H100 | 128 | `TP2/PP16/EP8, mt8192, cpu-offload` | 307 | $6.23 |
| H200 | 48 | `TP2/PP6/EP8, cpu-offload` | 305 | $7.22 |
| B200 | 64 | `TP2/PP8/EP8, mt16384, vpp2` | 1290 | $3.07 |

## GLM-5.2
### GLM-5.2-744B-A40B
| Accelerator | GPUs | Best recipe | tok/s/GPU | $/M tokens |
|---|---|---|---|---|
| H100 | 256 | `TP4/PP8/CP1/EP32, mt8192, cpu-offload` | 68 | $28.10 |
| H200 | 128 | `TP4/PP4/CP1/EP32, mt8192, selective recompute, cpu-offload` | 160 | $13.71 |
| B200 | 64 | `TP4/PP2/CP4/EP32, mt12288, cpu-offload` | 150 | $26.33 |

## How the numbers are defined
- **Workload:** fixed-shape SFT, 8192 tokens/sequence, global batch 128, bf16.
- **Measurement:** aggregate tokens/s/GPU over a 27-step window (steps 3-22).
- **Cost:** `$/M tokens = (per-GPU $/hr) / (tok/s/GPU × 3600) × 1e6`. Per-GPU on-demand rates — H100 $6.88 ([p5.48xlarge](https://instances.vantage.sh/aws/ec2/p5.48xlarge)), H200 $7.91 ([p5en.48xlarge](https://instances.vantage.sh/aws/ec2/p5en.48xlarge)), B200 $14.24 ([p6-b200.48xlarge](https://instances.vantage.sh/aws/ec2/p6-b200.48xlarge)). AWS EC2 on-demand, us-east-1, fetched 2026-09-18; prices change — recompute for your region/commitment.
- **Recipe notation:** TP/PP/CP/EP parallelism degrees, `mt` = max-tokens-per-GPU (packing), plus recompute / offload / dispatcher.

Recipes were found by an autonomous throughput-tuning agent and each shipped winner passed a 100-step loss-parity test (same data and seed), so it trains the same model.

Machine-readable data: [`data.json`](data.json).

## Call for recipes
Have a recipe that beats one of these — or a new model, accelerator, or search method? We want it. Open an issue or a pull request with the recipe and its measured tok/s/GPU on the **same workload and measurement** above, so the numbers are directly comparable, and we'll add it to the board. The aim is a shared, reproducible record of the best-known training throughput; contributions and challenges are welcome.
