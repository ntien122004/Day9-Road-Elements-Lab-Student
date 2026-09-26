# MOCK QA Scenario — For Practice Only

**All figures below are invented.** The image names are fictional; this is not peer feedback, gold, or a real score. Do not copy these values into `gold_decisions.csv`, `transfer_score.csv`, or the official peer-feedback record.

## Hypothetical results

Assume a five-image blind test with ten non-geometry decisions (one image-status and one exact-count decision per image), two geometry decisions, and three decisions marked critical.

| Mock image | Image status | Exact sign count | Critical decision | Geometry |
|---|---|---|---|---|
| Mock-A | Correct | Correct | N/A | Pass |
| Mock-B | Correct | Correct | Correct | N/A |
| Mock-C | Incorrect | Incorrect | Incorrect: one critical sign missed | Pass |
| Mock-D | Correct | Correct | Correct | N/A |
| Mock-E | Correct | Correct | N/A | N/A |

Assume the reviewer also received two clarification questions.

## Metrics

- Image-status accuracy: `4/5 = 80%`
- Exact sign-count accuracy: `4/5 = 80%`
- Non-geometry decision accuracy (D): `8/10 = 80%`
- Critical accuracy (C): `2/3 = 66.67%`; critical escape rate: `1/3 = 33.33%`
- Geometry pass rate (G): `2/2 = 100%`
- Independence score (I): `70` for 2 questions

Using the lab formula `GTS = 0.60 D + 0.20 C + 0.10 G + 0.10 I`:

`GTS = 0.60(80) + 0.20(66.67) + 0.10(100) + 0.10(70) = 78.33`

## Example gate decision

**REJECT / ESCALATE**, despite a GTS of about 78.3: the mock run has a critical escape, and both image-status and exact-count accuracy are below 100%. The team should inspect the image and export evidence, classify the cause as `guideline_gap`, `data_ambiguity`, or `execution_error`, then choose a targeted action. Do not assume the peer is at fault or revise gold just to improve the score.
