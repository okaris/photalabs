# Ablation: by model

Per-model ArcFace score distributions across all edits. Source #91 excluded (n=45 edits per model, from 9 sources x 5 edit types).

## Overall comparison

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| Mean | 0.700 | 0.571 | 0.544 |
| Median | 0.716 | 0.559 | 0.532 |
| Min | 0.483 | 0.412 | 0.227 |
| Max | 0.849 | 0.812 | 0.838 |
| Std | 0.078 | 0.101 | 0.139 |
| Count | 45 | 45 | 45 |

---

## Phota (with profile)

Phota uses a stored identity profile rather than a reference image, allowing it to generate consistent identity from a compact embedding.

**Score distribution:**
- 0.8+ : 1 edit (2%)
- 0.7-0.8 : 27 edits (60%)
- 0.6-0.7 : 13 edits (29%)
- 0.5-0.6 : 3 edits (7%)
- Below 0.5 : 1 edit (2%)

**Best 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 8 | Outfit + background | 0.849 |
| 3 | Outfit + background | 0.799 |
| 5 | Outfit + background | 0.794 |
| 1 | Laugh expression | 0.787 |
| 1 | Outfit + background | 0.779 |

**Worst 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 34 | Dramatic lighting | 0.561 |
| 70 | Dramatic lighting | 0.551 |
| 21 | Dramatic lighting | 0.526 |
| 34 | Look over shoulder | 0.507 |
| 21 | Laugh expression | 0.483 |

**Observations:**
- Phota is most consistent (lowest std dev at 0.078)
- Outfit + background edits produce the highest scores
- Weakest on source 21 (side profile), especially combined with lighting or expression changes
- Even worst cases stay above 0.48

---

## NB2 + reference image

NB2 with a reference image uses Gemini 3.1 Flash with an uploaded reference photo for identity guidance.

**Score distribution:**
- 0.8+ : 1 edit (2%)
- 0.7-0.8 : 4 edits (9%)
- 0.6-0.7 : 14 edits (31%)
- 0.5-0.6 : 14 edits (31%)
- 0.4-0.5 : 12 edits (27%)

**Best 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 5 | Outfit + background | 0.812 |
| 1 | Look over shoulder | 0.736 |
| 3 | Look over shoulder | 0.726 |
| 3 | Outfit + background | 0.724 |
| 1 | Dramatic lighting | 0.709 |

**Worst 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 14 | Laugh expression | 0.436 |
| 70 | Dramatic lighting | 0.426 |
| 21 | Laugh expression | 0.420 |
| 34 | Outfit + background | 0.416 |
| 34 | Look over shoulder | 0.412 |

**Observations:**
- Higher variance than Phota (std 0.101 vs 0.078)
- Struggles most with expression changes (laugh), scoring 0.42-0.56
- One strong outlier at 0.812 (source 5, outfit change) where it slightly beats Phota
- Medium/hard sources degrade more sharply than for Phota

---

## NB2 (no reference)

NB2 without reference uses the same Gemini 3.1 Flash model but with no uploaded reference image, relying solely on the source image context.

**Score distribution:**
- 0.8+ : 1 edit (2%)
- 0.7-0.8 : 3 edits (7%)
- 0.6-0.7 : 12 edits (27%)
- 0.5-0.6 : 10 edits (22%)
- 0.4-0.5 : 10 edits (22%)
- 0.3-0.4 : 5 edits (11%)
- 0.2-0.3 : 4 edits (9%)

**Best 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 3 | Outfit + background | 0.838 |
| 5 | Outfit + background | 0.785 |
| 83 | Outfit + background | 0.763 |
| 1 | Low angle shot | 0.736 |
| 14 | Dramatic lighting | 0.705 |

**Worst 5 scores:**

| Source | Edit | Score |
|--------|------|-------|
| 21 | Laugh expression | 0.349 |
| 21 | Look over shoulder | 0.345 |
| 21 | Dramatic lighting | 0.334 |
| 70 | Look over shoulder | 0.303 |
| 70 | Low angle shot | 0.227 |

**Observations:**
- Highest variance of all models (std 0.139), least predictable
- Can occasionally match or beat NB2+ref (outfit changes on easy sources)
- Collapses badly on hard sources, especially source 70 and 21
- Without a reference image, identity preservation on harder prompts becomes unreliable

---

## Model comparison by edit type

| Edit type | Phota | NB2+ref | NB2 noref | Phota lead vs best NB2 |
|-----------|-------|---------|-----------|------------------------|
| Dramatic lighting | 0.662 | 0.589 | 0.557 | +12% |
| Laugh expression | 0.690 | 0.477 | 0.449 | +45% |
| Look over shoulder | 0.689 | 0.589 | 0.535 | +17% |
| Low angle shot | 0.701 | 0.592 | 0.529 | +18% |
| Outfit + background | 0.756 | 0.609 | 0.647 | +17% |
