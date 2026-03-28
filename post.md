# Phota Labs: What's behind the identity model? A benchmark

**TL;DR:** Based on our observations, information shared publicly by Phota Labs, and benchmarks conducted today, here is what we believe to be true: Phota Labs has a proprietary identity model that genuinely works. It improves identity preservation by +39% (generation) and +23% (edits) over vanilla Nano Banana 2. But it doesn't do the generation or editing itself. Google's Nano Banana handles that; Phota's model runs as a second pass to correct identity drift. Without a trained profile, the identity model doesn't run at all. You get vanilla NB at Google pricing + $0.01. The identity correction also fights the edit: expressions and poses get pulled back toward training data, trading creative range for identity stability. We benchmarked 250 images across 4 metrics. Full data below.

---

When Phota Labs launched last week with claims of superior identity preservation for photos of people and pets, we were excited. The generative AI space has been waiting for a serious identity-preserving model, something that doesn't just generate beautiful images but keeps *your person* looking like *your person* through edits, enhancements, and generations.

We released the Phota APIs on [inference.sh](https://inference.sh) within hours. Customers wanted it as an alternative to Nano Banana. We wanted it to be good.

Then we looked closer.

## What we found under the hood

Testing with Gemini's content analysis revealed that every Phota output carried Google's [SynthID watermark](https://deepmind.google/technologies/synthid/), flagged as images created with Google models. When we ran the same prompts through vanilla Nano Banana (Gemini 3.x) with the Phota-selected profile picture as a reference image, we got the same outputs. Same default clothing from the reference photo. Same composition patterns. Same artifacts.

After community questions, Phota Labs [shared how their system works](https://x.com/PhotaLabs/status/2037723587071918541): *"For base image generation, Phota uses leading foundation models (both open and closed source - including Nano Banana). On top of those, we've trained our own identity model [...] to preserve identity consistently."*

When [we asked directly](https://x.com/okaris/status/2037749210003460359) whether their proprietary model always contributes, Phota [confirmed](https://x.com/PhotaLabs/status/2037779340075880862): *"If there are no profiles present, there is no notion of personalization. Only in that case [...] one of the flagship foundation models (like NB) is doing the heavy lifting."*

In other words: **without a trained profile, Phota is a passthrough to Google's Nano Banana at Google pricing plus a $0.01 markup.** No proprietary model runs at all.

The way Phota was introduced — from the [a16z announcement](https://a16z.com/announcement/investing-in-phota-labs/) to the product launch — gave the impression of a new, standalone model built from the ground up. When a team of ex-Adobe AI researchers raises $5.6M to build "personalized generative AI models," it's reasonable to expect that the core generation is their own. The reality — a pipeline that delegates generation to Google's models — isn't a problem in itself. It's a valid architecture. But the gap between expectation and reality caught us and our customers off guard.

To be fair: Phota does have a proprietary identity model, and it works. Especially with occlusions, lighting changes, and other difficult conditions, the identity correction is impressive. The issue was never the quality of their work. It's how it was positioned. With a profile active, Phota runs this identity model as a second pass on top of NB's output. The question became: how much does that second pass actually help?

We decided to stop speculating and measure it.

## About the author

I'm [okaris](https://okaris.com), the founder of [inference.sh](https://inference.sh), the platform this research ran on. Before inference.sh, I co-founded [StyleOf](https://styleof.com) in 2023, where I spent two years focused exclusively on generative AI personalisation research. Over that period I ran more than 100,000 personalisation fine-tunings and over 10,000 training experiments with novel architectures and approaches. Identity preservation in generative models isn't a new problem to me. It's the problem I've spent a lot (maybe too much) time on.

This benchmark is not a hit piece. Phota's identity layer works and adds measurable value. But the positioning left customers, including ours, believing they were getting a new model, not a pipeline wrapper. This is our honest, data-driven answer to the question: *what exactly are you getting for the premium?*

## Experimental setup

### The subject: Eva

Eva is a synthetic identity, created from real reference identities but unique, not a specific real person. She has a stable, consistent identity refined through prior work. Her training images have a slightly smooth, artificial skin texture from older generation techniques. This is intentional and important: **models that add realistic skin texture to Eva are actually drifting *away* from her identity, not improving it.** The smooth skin is a feature of who Eva is, not a flaw in the data.

We trained a Phota profile with 50 images of Eva. Training took 8 minutes and 11 seconds. Phota's full API - training, generation, editing, and enhancement - is available on [inference.sh](https://inference.sh). You can [train your own profile](https://inference.sh/apps/phota/train), then run [generate](https://inference.sh/apps/phota/generate), [edit](https://inference.sh/apps/phota/edit), and [enhance](https://inference.sh/apps/phota/enhance) against it.

**Reference image** - the profile picture Phota auto-selected from Eva's training set. This is the identity anchor for all ArcFace comparisons:

![Eva - ArcFace reference](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmta72p5vhpfaynebf2rh4ym.png)

### Models under test

We compare three configurations throughout. Nano Banana 2 (NB2) is Google's Gemini 3.1 Flash Image Preview, the latest foundation model in the Nano Banana family. NB Pro is Gemini 3.0 Pro Image Preview.

| Model | Description |
|-------|-------------|
| **Phota (with profile)** | Full pipeline: NB base + proprietary identity model |
| **NB2 + reference image** | Gemini 3.1 Flash with Eva's profile picture as a second input image for identity guidance |
| **NB2 (no reference)** | Gemini 3.1 Flash with only the source/prompt, no identity anchor |

*Note: Phota may also use Nano Banana Pro (Gemini 3.0 Pro) for some tasks. NB Pro is generally considered the higher-quality model. We focused our benchmark on NB2 (Flash) as the comparison baseline since it's cheaper and faster, and Phota's timing overhead suggests they're more likely using Flash as their base. A limited NB Pro comparison on the lighting prompt is included in Stage 2.*

### Evaluation metrics

We built four GPU-accelerated evaluation apps on inference.sh, each deployed as a reusable API:

- **[ArcFace](https://inference.sh/apps/eval/arcface)** - Face recognition embedding similarity (cosine). THE key metric: measures whether the output face is the same person as the reference. Uses InsightFace's buffalo_l model.
- **[CLIPScore](https://inference.sh/apps/eval/clipscore)** - CLIP ViT-L/14 cosine similarity between text prompt and output image. Measures how well the model followed the prompt.
- **[PickScore](https://inference.sh/apps/eval/pickscore)** - PickScore v1 (CLIP-H fine-tuned on Pick-a-Pic human preferences). Measures overall image quality and human appeal.
- **[InceptionV3 Similarity](https://inference.sh/apps/eval/fid-score)** - Perceptual similarity between paired outputs. Not a quality metric; it measures how visually similar two model outputs are for the same prompt.

All images generated at 1024x1024 (1:1 aspect ratio). ArcFace comparisons use the Phota-chosen profile picture as the single reference anchor.

## Stage 1: generation benchmark (n=100)

We generated 100 images from 100 diverse prompts, ranging from simple portraits to extreme challenges like group photos, age changes, artistic styles, extreme close-ups, and full-body action shots. The goal: build a stable representation of how each model depicts Eva across varied conditions, and establish a baseline before testing edits on the same identity. Each prompt was run through Phota (with profile) and NB2 (with reference image).

Here are a few Phota generations to give a sense of Eva's identity and the range of prompts:

| Prompt 1: Smiling in a park | Prompt 3: Wearing sunglasses at beach | Prompt 5: Golden hour portrait |
|:---:|:---:|:---:|
| ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84vemy0jdbxwqmw8q99v6.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84q39ahn110qwvk4q552b.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84z6emayr9nh01nk3zrea.png) |
| ArcFace: 0.830 | ArcFace: 0.824 | ArcFace: 0.838 |

And the same prompts through NB2 (with reference image):

| Prompt 1 | Prompt 3 | Prompt 5 |
|:---:|:---:|:---:|
| ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtbcpygvpvk9axwy1yv87v7.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtbcq11sbv6ftae12xbk3n4.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtbd71w18z7kqypcp74zcs3.png) |

Notice how NB2 adds more realistic skin texture. Arguably better rendering, but it's drifting from Eva's identity. Eva has smooth skin. That's who she is.

### Results

| Metric | Phota (with profile) | NB2 (ref image) | Winner | Delta |
|--------|---------------------|-----------------|--------|-------|
| **ArcFace** (identity) | **0.716** | 0.516 | Phota | **+39%** |
| **CLIPScore** (prompt adherence) | 0.260 | **0.270** | NB2 | +4% |
| **PickScore** (human preference) | 20.72 | **22.69** | NB2 | +10% |
| **Perceptual similarity*** | 0.765 mean | _(paired)_ | — | — |
| **Run time** (avg) | 55.3s | **19.8s** | NB2 | 2.8x faster |

*2 face detection failures per model on extreme crops, excluded from means.*

*\*Perceptual similarity: InceptionV3 cosine similarity between Phota and NB2 outputs for the same prompt (1.0 = identical, 0.0 = completely different). Not a quality metric; it measures how visually similar the two models' outputs are.*

![Stage 1: Identity Preservation](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmty7q27fjqtmtst6kx6dw38.png)

**The identity gap is significant.** Phota's mean ArcFace similarity of 0.716 vs NB2's 0.516 is a +39% improvement. In face recognition terms, this is the difference between "clearly the same person" and "could be a relative."

But NB2 wins on aesthetics. Its PickScore is 10% higher, meaning human raters would generally prefer NB2's outputs - they're more photorealistic, with richer skin texture and more natural rendering. NB2 also follows prompts marginally better (CLIPScore +4%). This is expected for two reasons. First, Eva's identity is inherently synthetic-looking, so models that "improve" her appearance drift from who she actually is. Second, identity refinement passes typically trade prompt alignment and output diversity for identity consistency, a well-known pattern in personalisation research.

**This is the fundamental tradeoff:** Phota preserves identity by constraining the output space. NB2 has more freedom to produce aesthetically pleasing images, but that freedom means drifting from the source identity.

Phota is also 2.8x slower, consistent with a two-stage pipeline where NB generates first, then the identity model refines.

### Perceptual similarity: how different are the outputs?

The InceptionV3 cosine similarity between Phota and NB2 outputs for the same prompt averaged 0.765 (where 1.0 = identical, 0.0 = completely different). The outputs share the same compositional DNA. They're clearly derived from the same base model but diverge meaningfully on faces, skin texture, and expression intensity. The divergence is strongest on challenging prompts involving extreme expressions, age transformations, and dramatic poses, which is exactly where identity preservation matters most.

## Stage 2: edit benchmark (n=150)

Generation is one thing. Editing is where identity preservation gets really stressed - you're asking the model to change specific aspects of an existing image while keeping the person recognizable. This is where Phota claims to shine.

We selected 10 source images from Stage 1's Phota outputs across three difficulty tiers:
- **Easy** (4 images): Neutral portraits, clear faces
- **Medium** (3 images): Varied expressions, poses, lighting
- **Hard** (3 images): Accessories, artistic styles, challenging angles

Each source was edited with 5 prompts designed to stress identity preservation:

1. *"Change the lighting to dramatic side lighting with half the face in deep shadow"*
2. *"Make her expression a wide genuine laugh with eyes crinkled and mouth open"*
3. *"Make her look over her right shoulder toward something behind her"*
4. *"Change the camera angle to a low angle shot looking up at her"*
5. *"Change her outfit to a bright red formal dress with a different background"*

This time we tested three conditions: Phota (with profile), NB2 with reference image, and NB2 without reference image. 150 tasks total.

### Identity preservation (ArcFace)

| Model | Mean Similarity | vs Phota |
|-------|----------------|----------|
| **Phota (with profile)** | **0.700** | — |
| NB2 + reference image | 0.571 | -18% |
| NB2 (no reference) | 0.544 | -22% |

*Excluding source #91 (all models scored ~0.1, extreme artistic style broke face detection).*

![Stage 2: Edit Identity Preservation](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmty7rqw4r0djhp8n3zbbv9b.png)

The three-tier ranking is clear and confirms the empirical observations:

1. **Phota** preserves identity best. The identity model is doing real work.
2. **NB2 with reference** helps modestly. The reference image provides some identity anchor, but NB2 treats it as guidance, not gospel.
3. **NB2 without reference** is worst. Even with the source image as input, NB2 treats it as a scene to modify, not an identity to preserve. The face drifts noticeably.

Here's the same source image edited with all five prompts across all three models. Source #1 (easy tier, neutral portrait):

| | Source | Phota | NB2 + ref | NB2 no ref |
|---|:---:|:---:|:---:|:---:|
| **Lighting** | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84vemy0jdbxwqmw8q99v6.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmwj69rgdyv1anfsy63we.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnmfmkvcmy790agqgb3xj.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmev6caaef8a963428hkd.png) |
| ArcFace | — | 0.778 | 0.709 | 0.620 |
| **Laugh** | | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthn3511ffq1b8j2jz4k435.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmqrfz47rg4kt9ea07tyq.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnb4z7x5g2kphmcth0m09.png) |
| ArcFace | — | 0.787 | 0.529 | 0.579 |
| **Shoulder** | | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthp97vf7x1p98kdre54sgk.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnmd1v1ng5ndpw35sx9dy.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnzd9frmyczcze32rv548.png) |
| ArcFace | — | 0.667 | 0.736 | 0.686 |
| **Low angle** | | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthprzkhcrpa3h907et9txb.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthpma0gkgmjmqp29djrnmb.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthpe19fx302d24t3kv51gy.png) |
| ArcFace | — | 0.714 | 0.691 | 0.736 |
| **Outfit** | | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthqf8etebg6gynffb2crhw.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthq4ap8k647s4s577dbpe1.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthq7ee98rvjsh37xvw2yby.png) |
| ArcFace | — | 0.779 | 0.655 | 0.627 |

And the same edits on a harder source, #70 (artistic style, challenging angle):

| | Source | Phota | NB2 + ref | NB2 no ref |
|---|:---:|:---:|:---:|:---:|
| **Lighting** | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt8tpg8cw7jkja9zqpfdz4r.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjd1dc1xh3mxax9mpnkxk5.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjcetym87c1k0ee2evxx6k.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjdaq553g44587a1v76zmb.png) |
| ArcFace | — | 0.551 | 0.426 | 0.479 |
| **Laugh** | | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjd53sj03q5dbfj8nkhk5n.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjdg72q4qkq1d0hzmbyqg8.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmtjd9qd1pz08mkjbvbbfhw1.png) |
| ArcFace | — | 0.655 | 0.442 | 0.433 |

### Identity by edit type

Not all edits stress identity equally:

| Edit | Phota | NB2+ref | NB2 noref | Phota vs NB2+ref |
|------|-------|---------|-----------|------------------|
| Dramatic lighting | 0.662 | 0.589 | 0.557 | +12% |
| Laugh expression | **0.690** | **0.477** | **0.449** | **+45%** |
| Look over shoulder | 0.689 | 0.589 | 0.535 | +17% |
| Low angle shot | 0.701 | 0.592 | 0.529 | +18% |
| Outfit + background | 0.756 | 0.609 | 0.647 | +24% |

![Identity by Edit Type](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmty7nf5w3gxt7cb5yk90k9q.png)

**The expression edit is the most revealing.** When asked to produce "a wide genuine laugh with eyes crinkled and mouth open," Phota's identity advantage peaks at +45% over NB2. But here's the twist: look at what actually happened.

### The expression tradeoff: when identity preservation backfires

NB2 produced a convincing, natural laugh - deep nasolabial folds, genuinely crinkled eyes, open mouth. It looks like a real person laughing.

Phota produced a teeth-showing smile. Eyes mostly open. Minimal wrinkling. It looks like a portrait photographer said "say cheese" and the subject complied.

| Source | Phota (ArcFace: 0.787) | NB2+ref (ArcFace: 0.529) | NB2 noref (ArcFace: 0.579) |
|:---:|:---:|:---:|:---:|
| ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84vemy0jdbxwqmw8q99v6.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthn3511ffq1b8j2jz4k435.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmqrfz47rg4kt9ea07tyq.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnb4z7x5g2kphmcth0m09.png) |

Phota scores +49% on identity because its identity model acts as a **regularizer**. It resists large deformations of the face, pulling expressions back toward the training distribution (which was mostly neutral/mild expressions). A wide laugh deforms the face significantly: eyes squeeze shut, cheeks push up, mouth geometry changes entirely. The identity model sees these deformations as identity drift and corrects them back toward the trained face.

The result is uncanny. A half-committed expression that doesn't reach the eyes. It scores higher on identity metrics precisely *because* it didn't actually execute the edit. This is the core tension in Phota's approach: **the identity model and the edit prompt are fighting each other, and identity always wins.**

### The lighting debacle: when NB doesn't understand photography

The dramatic lighting prompt exposed a different failure mode, this time in NB itself.

When asked to "change the lighting to dramatic side lighting with half the face in deep shadow," NB2 (Gemini 3.1) interpreted this literally. Instead of producing Rembrandt lighting or split lighting, where a directional light source creates natural shadow falloff across the face, NB2 produced what looks like **flash sync curtain banding**: half the frame is evenly illuminated while the other half drops to black with an unnaturally sharp boundary, as if a focal-plane shutter curtain was caught mid-travel during a flash exposure.

| Source | Phota (0.778) | NB2+ref (0.709) | NB2 noref (0.620) |
|:---:|:---:|:---:|:---:|
| ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmt84vemy0jdbxwqmw8q99v6.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmwj69rgdyv1anfsy63we.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthnmfmkvcmy790agqgb3xj.png) | ![](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmthmev6caaef8a963428hkd.png) |

This is not a lighting direction. It's a compositing mask. The model interpreted "half in shadow" as "apply a 50% opacity black rectangle."

NB Pro (Gemini 3.0) handled it marginally better. Phota also showed the artifact on some outputs, but its second-pass identity refinement incidentally softened the hard edge, consistent with the two-stage pipeline where Phota's post-processing smooths NB's compositing artifacts.

We ran a limited comparison (20 images) of NB Pro vs NB2 on this prompt. NB Pro produced noticeably better results for both quality and identity. There are hints that Phota may use 3.0 and 3.1 interchangeably depending on the task, but we couldn't fully confirm this within our budget constraints.

### Prompt adherence and image quality

| Metric | Phota | NB2+ref | NB2 noref |
|--------|-------|---------|-----------|
| CLIPScore (prompt adherence) | 0.185 | **0.195** | 0.193 |
| PickScore (human preference) | 19.97 | **20.10** | 20.01 |

NB2 follows prompts ~5% better and scores marginally higher on human preference, but the PickScore gap is much smaller for edits (+0.7%) than for generation (+10%). When editing existing images rather than generating from scratch, NB2's aesthetic advantage nearly disappears.

### Timing

| Model | Mean Run Time |
|-------|---------------|
| Phota (with profile) | 48.0s |
| NB2 + reference | 29.4s |
| NB2 (no reference) | 28.1s |

Phota is ~1.7x slower for edits (vs 2.8x for generation). The identity model adds roughly 20 seconds of overhead per edit.

![Inference Time Comparison](https://cloud.inference.sh/app/files/u/4mg21r6ta37mpaz6ktzwtt8krr/01kmty7sbrj98jr51hc4wxw8kn.png)

## Confirming the two-stage pipeline

Multiple lines of evidence confirm Phota runs NB as a first stage, then applies identity correction:

1. **SynthID watermark** - All Phota outputs are flagged by Gemini as carrying Google's SynthID, meaning Google models generated the base image.

2. **Blue dress conditioning** - Despite training with 50 diverse images, Phota generations default to a specific blue dress from the training set when no outfit is prompted. Phota auto-selects a "profile picture" (the one with the blue dress), and we believe this image is passed to NB as a reference in the first stage. Our evidence: NB2 with the same profile picture as reference produces the same default dress behavior. When specific clothing is prompted, both Phota and NB2 work fine. Without clothing prompts, both default to the dress in the profile picture.

3. **Timing overhead** - Phota consistently takes 1.7-2.8x longer than direct NB calls, consistent with running two models sequentially.

4. **Artifact softening** - Phota's outputs show softened versions of NB-specific artifacts (like the flash sync banding on the lighting prompt), consistent with a refinement pass that incidentally smooths the base model's failures.

5. **Phota's own statement** - ["If there are no profiles present [...] one of the flagship foundation models (like NB) is doing the heavy lifting."](https://x.com/PhotaLabs/status/2037723587071918541)

## The enhance pilot (n=3)

As a preliminary test, we degraded three Phota-generated images (blur, noise, JPEG compression) and ran enhance through Phota with and without profile.

| | With Profile | Without Profile (= vanilla NB) | Delta |
|---|---|---|---|
| Mean ArcFace | **0.855** | 0.449 | **+90%** |

The gap is even larger for enhance. The identity model nearly doubles identity preservation when restoring degraded images. Without the profile, Phota's enhance is just NB enhance, which reconstructs a plausible face but not necessarily *the right* face.

*Note: n=3, included for directional signal only.*

## What this means

### The identity layer works.

Phota's proprietary model adds real, measurable identity preservation: +39% on generation, +23% on edits, and potentially +90% on enhancement. If your use case is "keep this specific person looking like this specific person through edits," Phota delivers something that vanilla NB does not.

### But it's not a model. It's a pipeline.

Phota is not a new foundation model for image generation. It's a post-processing pipeline: NB generates, then a proprietary identity model refines. This distinction matters because:

- **Without a profile, you're paying Google pricing + $0.01 for vanilla NB.** No proprietary model runs. This was not communicated clearly at launch.
- **The identity model fights the edit model.** Expressions, poses, and lighting changes are constrained because the identity model treats facial deformations as identity drift. You get better identity at the cost of creative range.
- **You're locked into NB's limitations.** If NB can't do proper lighting (it can't), Phota can't either. It just softens the failure slightly.

### The open-source implication

Phota's approach - SOTA foundation model + identity correction in post - is a replicable pattern. The identity model doesn't need to be a strong standalone generator. It just needs to know what the person's face should look like and nudge the output back toward it. A fine-tuned model focused purely on identity fixup as a second pass could achieve similar results. My bet would be on something like Qwen's image editing models, but any capable image-to-image architecture could fill this role.

This suggests the value isn't in Phota's identity model architecture. It's in the two-stage pipeline pattern itself. Anyone with access to a strong base model and an identity-aware refinement model could build this.

## How we built this

The entire research pipeline ran on [inference.sh](https://inference.sh):

1. **Training** - Phota profile trained via our [`phota/train`](https://inference.sh/apps/phota/train) app wrapper
2. **Generation & Editing** - 250 image tasks submitted via async batch scripts using `infsh app run --no-wait`
3. **Evaluation** - Four custom GPU-accelerated scoring apps, deployed on inference.sh and callable via API:
   - [`eval/arcface`](https://inference.sh/apps/eval/arcface) - Identity similarity (batch + single + embedding modes)
   - [`eval/clipscore`](https://inference.sh/apps/eval/clipscore) - Prompt adherence
   - [`eval/pickscore`](https://inference.sh/apps/eval/pickscore) - Human preference scoring
   - [`eval/fid-score`](https://inference.sh/apps/eval/fid-score) - FID and perceptual similarity

From training to final scores, the entire benchmark was orchestrated through shell scripts and the `infsh` CLI. No notebooks, no local GPU required. The eval apps are public. You can use them for your own benchmarks.

## Conclusion

Phota Labs built something that works. The identity preservation improvement is real, statistically significant, and visible to the naked eye. If you need identity-consistent image generation and are willing to accept the tradeoffs - slower inference, constrained expressiveness, NB's base-model limitations - Phota with a trained profile delivers.

But the launch positioning was misleading. Customers and platform operators (including us) integrated Phota believing it was a fundamentally new model. It's not. It's a well-executed pipeline on top of Google's models, and without a profile, it's a $0.01 markup on vanilla NB with no added value.

What I personally hoped for, and what I believed for a few short hours after the launch, was that Phota would be identity-aware even without a trained profile. That when you submitted an image for editing, it would extract the identity from the input and make identity-preserving edits automatically. No training step, no profile ID, just *understanding* that the face in the image matters. That would have been genuinely new. Instead, without a profile, it doesn't even try.

We would have been just as excited if they had said: "We built the best identity-preserving post-processing layer for Nano Banana." That's a genuinely valuable product. The data proves it. We just wish they'd led with that.

---

*Phota's APIs (train, generate, edit, enhance) and all evaluation apps are available on [inference.sh](https://inference.sh). Try them yourself and draw your own conclusions. Raw data, scoring scripts, and ablations are on [GitHub](https://github.com/okaris/photalabs).*

*This research was conducted on March 28, 2026, using Phota Labs API v1 and Google Gemini 3 / 3.1 Flash Image Preview via inference.sh.*
