---
layout: default
title: "Day 2: First Sounds — What AudioLDM2 Can and Can't Do"
date: 2025-02-11
---

# Day 2: First Sounds — What AudioLDM2 Can and Can't Do

*Building AmbientGen — a text-to-ambient-sound generator. This is post #2.*

## From paper to audio in 5 lines of code

One of the reasons I picked AudioLDM2 is how easy it is to get running. Thanks to HuggingFace's `diffusers` library, generating your first audio from text takes roughly five lines of Python:

```python
from diffusers import AudioLDM2Pipeline
import torch

pipe = AudioLDM2Pipeline.from_pretrained("cvssp/audioldm2", torch_dtype=torch.float16)
pipe = pipe.to("cuda")

audio = pipe("gentle rain falling on a window with distant thunder",
             num_inference_steps=50, audio_length_in_s=10.0).audios[0]
```

That's it. Free T4 GPU on Google Colab, model downloads in about 30 seconds, and you get a 10-second audio clip. The barrier to entry for audio generation in 2025 is remarkably low.

But low barrier to entry doesn't mean high quality output. Let me tell you what I actually heard.

## The results: honest impressions

I tested seven prompts ranging from simple ("rain") to detailed ("busy coffee shop with people talking and cups clinking"). Here's what I found.

**What worked reasonably well:**
- **Ocean waves** sounded the most convincing. Waves have a natural rhythm and spectral shape that the model seems to handle well.
- **Forest at night with crickets** was decent — the crickets had a believable texture and the overall atmosphere felt right.
- **Fireplace crackling** produced recognizable sounds, though with some metallic artifacts.

**What didn't work well:**
- **Rain** was the biggest disappointment. Almost every rain generation had noticeable distortion and artifacts — a kind of digital crunchiness that breaks the illusion immediately. This was true across both the base model and the large model.
- **Coffee shop ambiance** produced something vaguely busy-sounding but not convincingly human.

The pattern I noticed: sounds with distinct, punctual events (waves crashing, wood crackling) came out better than continuous, textural sounds (steady rain, ambient noise). My hypothesis is that diffusion models handle discrete acoustic events more naturally than they handle sustained textures, where any artifact in the spectral continuity becomes immediately obvious to human ears.

## Prompt length matters — but not how I expected

I ran a systematic test with five versions of a rain prompt, from minimal to very detailed:

1. "rain"
2. "rain falling"
3. "gentle rain falling on a window"
4. "gentle rain falling on a window with distant thunder rumbling softly"
5. "high quality recording of gentle rain falling on a window with distant thunder, no music, ambient soundscape"

Prompt 5 was clearly the best — it had audible thunder and fewer artifacts. But it wasn't because more words equals better audio. It's because certain phrases act as quality signals. Adding "high quality recording" and "ambient soundscape" seems to steer the model toward a different region of its training distribution — probably closer to well-recorded sound design samples rather than noisy YouTube audio.

This is essentially **prompt engineering for audio**, and it follows a similar logic to image generation: you're not just describing what you want, you're describing the *quality and style* of what you want.

## Negative prompts help

AudioLDM2 supports negative prompts — text that tells the model what to avoid. Adding `"music, voices, low quality, distorted"` as a negative prompt consistently improved the ambient sound results. It's a simple trick but it makes a noticeable difference, especially for reducing unwanted musical elements that sometimes bleed into sound effect generation.

## Guidance scale and inference steps

I tested guidance scale from 3.0 to 15.0 and inference steps from 20 to 150.

**Guidance scale** controls how strongly the model follows your text prompt. The sweet spot was around 5.0–7.5. Below that, the audio becomes generic and vague. Above 10, artifacts increase noticeably — the model "tries too hard" and produces distorted, overcooked results.

**Inference steps** had a surprising result: 50 steps sounded better than 100. More steps didn't mean better quality — past a certain point, the audio actually became more distorted. This suggests the denoising schedule isn't perfectly calibrated for all audio types, or that the model starts to "overfit" to the conditioning at high step counts. Either way, 50 steps seems to be the practical optimum.

## The honest assessment

Let me be direct: AudioLDM2's output quality is not good enough for a consumer product right now. The rain sounds would not pass as a relaxation app. There are audible artifacts in most generations, and the overall fidelity is noticeably below a real recording.

But that's not the point of this project — at least not yet. AudioLDM2 is an excellent learning platform. The architecture is well-documented, the code is accessible, and it clearly demonstrates both the potential and the current limitations of audio diffusion models.

In Phase 5, I plan to benchmark AudioLDM2 against Stable Audio Open, which uses a newer DiT architecture and reportedly produces higher-quality results. The comparison will be interesting precisely because I now have a detailed understanding of where AudioLDM2 falls short.

## Key takeaways for prompt engineering

From today's experiments:

1. **Be specific about the recording quality** — "high quality recording" and "ambient soundscape" act as quality modifiers
2. **Use negative prompts** — always include "music, voices, low quality, distorted" for ambient sound generation
3. **Keep guidance scale moderate** — 5.0 to 7.5 works best
4. **50 inference steps is enough** — more isn't better
5. **Discrete events > continuous textures** — the model handles waves and crackling better than steady rain

## What's next

In the next post, I'll build a more systematic prompt engineering framework for ambient sounds — categorizing different soundscape types and finding the optimal prompt patterns for each. I'll also start building the Gradio interface so we have something interactive to play with.

---

**Code:** The full experiment notebook is available in the [experiments folder](../experiments/).

*[← Back to blog index](index.md)*
