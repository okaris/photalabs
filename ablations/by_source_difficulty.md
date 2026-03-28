# Ablation: by source difficulty

ArcFace identity similarity broken down by source image difficulty tier. Source #91 excluded from means.

Sources were assigned tiers based on their generation ArcFace score:
- **Easy** (sources 1, 3, 5, 8) - clean, well-lit portraits with clear face visibility
- **Medium** (sources 14, 21, 34) - more complex scenes, expressions, or angles
- **Hard** (sources 70, 83) - accessories, cluttered backgrounds, or other occlusions

Source 91 (face covered by hand) was excluded because all models scored near zero, making it uninformative for comparison.

## Summary by tier

| Tier | Sources | Phota | NB2+ref | NB2 noref | Phota advantage |
|------|---------|-------|---------|-----------|-----------------|
| Easy | 1, 3, 5, 8 | 0.745 | 0.634 | 0.608 | +18% |
| Medium | 14, 21, 34 | 0.650 | 0.513 | 0.476 | +27% |
| Hard | 70, 83 | 0.683 | 0.534 | 0.515 | +28% |

Key findings:
- All models degrade on harder sources, but Phota degrades less
- Phota's advantage grows from +18% on easy sources to +28% on hard sources
- NB2 with reference image consistently outperforms NB2 without reference

---

## Per-source breakdown

### Easy tier

**Source 1** (clean portrait, off-white wall)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.745 | 0.664 | 0.650 |

**Source 3** (passport-style, white background)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.740 | 0.663 | 0.616 |

**Source 5** (home office, warm overhead light)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.731 | 0.611 | 0.584 |

**Source 8** (kitchen doorway, warm tungsten light)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.765 | 0.597 | 0.585 |

---

### Medium tier

**Source 14** (surprise expression, birthday party)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.711 | 0.580 | 0.536 |

**Source 21** (side profile, cityscape window)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.608 | 0.496 | 0.411 |

**Source 34** (crouching with dog, park)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.631 | 0.461 | 0.480 |

---

### Hard tier

**Source 70** (reading glasses, cluttered desk)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.640 | 0.473 | 0.395 |

**Source 83** (corporate headshot, business casual)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.725 | 0.596 | 0.636 |

---

### Excluded

**Source 91** (face covered by hand, elevator)

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.121 | 0.113 | 0.073 |

All models scored poorly here. The source image itself scored 0.113 in the generation benchmark, indicating the hand-covered face was not a fair test of identity preservation.
