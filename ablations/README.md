# Ablations

Score breakdowns for the edit benchmark (Stage 2). Each of 10 source images was edited with 5 different edit prompts, and identity similarity was measured via ArcFace. Three model configurations are compared: Phota (with profile), NB2 with reference image, and NB2 without reference.

Source #91 is excluded from aggregate means because the source image (face covered by hand) caused all models to score near zero.

- [By edit type](by_edit_type.md) - how each edit prompt affects identity preservation
- [By source difficulty](by_source_difficulty.md) - easy vs medium vs hard source images
- [By model](by_model.md) - per-model score distributions, best and worst performing edits
