# Ablation: by edit type

ArcFace identity similarity broken down by edit prompt. Source #91 excluded from means (all models scored near 0.1 due to the source image having the face covered).

Five edit types were tested:
1. **Dramatic lighting** - change the lighting to dramatic side lighting with half the face in deep shadow
2. **Laugh expression** - make her expression a wide genuine laugh with eyes crinkled and mouth open
3. **Look over shoulder** - make her look over her right shoulder toward something behind her
4. **Low angle shot** - change the camera angle to a low angle shot looking up at her
5. **Outfit + background** - change her outfit to a bright red formal dress with a different background

## Summary

| Edit | Phota | NB2+ref | NB2 noref | Phota vs NB2+ref |
|------|-------|---------|-----------|------------------|
| Dramatic lighting | 0.662 | 0.589 | 0.557 | +13% |
| Laugh expression | 0.690 | 0.477 | 0.449 | +45% |
| Look over shoulder | 0.689 | 0.589 | 0.535 | +17% |
| Low angle shot | 0.701 | 0.592 | 0.529 | +18% |
| Outfit + background | 0.756 | 0.609 | 0.647 | +24% |

Key findings:
- Phota wins on all five edit types
- Largest gap on expression changes (laugh) where Phota leads by 45%
- Outfit + background changes are the easiest for all models
- Expression changes are the hardest for NB2 variants

---

## Dramatic lighting

| Source | Tier | Phota | NB2+ref | NB2 noref |
|--------|------|-------|---------|-----------|
| 1 | easy | 0.778 | 0.709 | 0.620 |
| 3 | easy | 0.715 | 0.680 | 0.443 |
| 5 | easy | 0.675 | 0.610 | 0.697 |
| 8 | easy | 0.753 | 0.686 | 0.691 |
| 14 | medium | 0.726 | 0.608 | 0.705 |
| 21 | medium | 0.526 | 0.503 | 0.334 |
| 34 | medium | 0.561 | 0.488 | 0.409 |
| 70 | hard | 0.551 | 0.426 | 0.479 |
| 83 | hard | 0.676 | 0.587 | 0.632 |
| 91 | hard | 0.135 | 0.092 | 0.106 |

---

## Laugh expression

| Source | Tier | Phota | NB2+ref | NB2 noref |
|--------|------|-------|---------|-----------|
| 1 | easy | 0.787 | 0.529 | 0.579 |
| 3 | easy | 0.741 | 0.557 | 0.528 |
| 5 | easy | 0.730 | 0.511 | 0.457 |
| 8 | easy | 0.771 | 0.514 | 0.483 |
| 14 | medium | 0.658 | 0.436 | 0.385 |
| 21 | medium | 0.483 | 0.420 | 0.349 |
| 34 | medium | 0.667 | 0.443 | 0.388 |
| 70 | hard | 0.655 | 0.442 | 0.433 |
| 83 | hard | 0.714 | 0.442 | 0.443 |
| 91 | hard | 0.092 | 0.102 | 0.082 |

---

## Look over shoulder

| Source | Tier | Phota | NB2+ref | NB2 noref |
|--------|------|-------|---------|-----------|
| 1 | easy | 0.667 | 0.736 | 0.686 |
| 3 | easy | 0.760 | 0.726 | 0.665 |
| 5 | easy | 0.696 | 0.559 | 0.472 |
| 8 | easy | 0.734 | 0.502 | 0.617 |
| 14 | medium | 0.749 | 0.672 | 0.449 |
| 21 | medium | 0.737 | 0.545 | 0.345 |
| 34 | medium | 0.507 | 0.412 | 0.622 |
| 70 | hard | 0.606 | 0.487 | 0.303 |
| 83 | hard | 0.745 | 0.664 | 0.658 |
| 91 | hard | 0.085 | 0.085 | 0.090 |

---

## Low angle shot

| Source | Tier | Phota | NB2+ref | NB2 noref |
|--------|------|-------|---------|-----------|
| 1 | easy | 0.714 | 0.691 | 0.736 |
| 3 | easy | 0.684 | 0.627 | 0.604 |
| 5 | easy | 0.760 | 0.561 | 0.507 |
| 8 | easy | 0.716 | 0.674 | 0.530 |
| 14 | medium | 0.689 | 0.577 | 0.488 |
| 21 | medium | 0.623 | 0.513 | 0.425 |
| 34 | medium | 0.739 | 0.547 | 0.562 |
| 70 | hard | 0.653 | 0.500 | 0.227 |
| 83 | hard | 0.733 | 0.640 | 0.682 |
| 91 | hard | 0.182 | 0.232 | 0.015 |

---

## Outfit + background

| Source | Tier | Phota | NB2+ref | NB2 noref |
|--------|------|-------|---------|-----------|
| 1 | easy | 0.779 | 0.655 | 0.627 |
| 3 | easy | 0.799 | 0.724 | 0.838 |
| 5 | easy | 0.794 | 0.812 | 0.785 |
| 8 | easy | 0.849 | 0.606 | 0.603 |
| 14 | medium | 0.734 | 0.610 | 0.653 |
| 21 | medium | 0.672 | 0.501 | 0.601 |
| 34 | medium | 0.683 | 0.416 | 0.422 |
| 70 | hard | 0.734 | 0.507 | 0.532 |
| 83 | hard | 0.759 | 0.649 | 0.763 |
| 91 | hard | 0.110 | 0.053 | 0.072 |
