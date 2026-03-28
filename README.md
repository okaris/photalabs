# Phota Labs identity benchmark

An independent benchmark measuring Phota Labs' identity preservation claims against Google's Gemini 3.x (Nano Banana). 250 scored image pairs, four evaluation metrics.

**[Read the full analysis](post.md)**

## Quick results

### Stage 1: generation (n=100)

| Metric | Phota (with profile) | NB2 (ref image) | Winner |
|--------|---------------------|-----------------|--------|
| ArcFace (identity) | **0.716** | 0.516 | Phota +39% |
| CLIPScore (prompt) | 0.260 | **0.270** | NB2 +4% |
| PickScore (quality) | 20.72 | **22.69** | NB2 +10% |
| Run time (avg) | 55.3s | **19.8s** | NB2 2.8x faster |

### Stage 2: edits (n=150)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| ArcFace (identity) | **0.700** | 0.571 | 0.544 |
| CLIPScore (prompt) | 0.185 | **0.195** | 0.193 |
| PickScore (quality) | 19.97 | **20.10** | 20.01 |
| Run time (avg) | 48.0s | 29.4s | **28.1s** |

## Browse the data

- [All generation samples](samples/generation/) - 100 Phota vs NB2 pairs with scores
- [All edit samples](samples/edits/) - 150 edits across 5 prompt types and 3 models
  - [Dramatic lighting](samples/edits/lighting/)
  - [Laugh expression](samples/edits/laugh/)
  - [Look over shoulder](samples/edits/shoulder/)
  - [Low angle shot](samples/edits/low_angle/)
  - [Outfit + background change](samples/edits/outfit/)
- [Ablations](ablations/) - Score breakdowns by edit type, source difficulty, and model

## Raw data

All CSV files are in the [`data/`](data/) directory:

### Stage 1
- `data/stage1/arcface_scores.csv` - Per-image identity similarity
- `data/stage1/clipscore_scores.csv` - Per-image prompt adherence
- `data/stage1/pickscore_scores.csv` - Per-image human preference
- `data/stage1/inception_scores.csv` - Phota/NB2 perceptual distance
- `data/stage1/generation_tasks_results.csv` - Phota task results + image URLs
- `data/stage1/generation_tasks_nb2_results.csv` - NB2 task results + image URLs
- `data/stage1/prompts.txt` - 100 generation prompts

### Stage 2
- `data/stage2/edit_arcface_scores.csv` - Per-edit identity similarity
- `data/stage2/edit_clipscore_scores.csv` - Per-edit prompt adherence
- `data/stage2/edit_pickscore_scores.csv` - Per-edit human preference
- `data/stage2/edit_timings.csv` - Per-edit run times
- `data/stage2/edit_tasks_results.csv` - Edit task results + image URLs
- `data/stage2/edit_sources.csv` - 10 source images with difficulty tiers
- `data/stage2/edit_prompts.txt` - 5 edit prompts

## Evaluation apps

All scoring was done with GPU-accelerated apps on [inference.sh](https://inference.sh):

- [eval/arcface](https://inference.sh/apps/eval/arcface) - ArcFace identity similarity
- [eval/clipscore](https://inference.sh/apps/eval/clipscore) - CLIP prompt adherence
- [eval/pickscore](https://inference.sh/apps/eval/pickscore) - PickScore human preference
- [eval/fid-score](https://inference.sh/apps/eval/fid-score) - FID + InceptionV3 perceptual similarity

## Methodology

- **Subject:** Eva, a synthetic identity trained with 50 images via Phota profile
- **Reference anchor:** Phota-chosen profile picture, resized to 1024x1024
- **Resolution:** All outputs at 1024x1024 (1:1)
- **Models:** Phota (with profile), Gemini 3.1 Flash + reference, Gemini 3.1 Flash without reference

Conducted March 28, 2026 by [okaris](https://okaris.com), founder of [inference.sh](https://inference.sh).
