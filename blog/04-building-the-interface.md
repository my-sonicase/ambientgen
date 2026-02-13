---
layout: default
title: "Day 4: Building AmbientGen — From Notebook to Product"
date: 2025-02-12
---

# Day 4: Building AmbientGen — From Notebook to Product

*Building AmbientGen — a text-to-ambient-sound generator. This is post #4.*

## The leap from notebook to app

There's a big difference between running code in a Colab notebook and having something other people can use. Today I made that jump: AmbientGen is now a live web app where anyone can generate ambient soundscapes from text.

**[Try it here →](https://huggingface.co/spaces/sonicase/ambientgen)**

Getting here involved some real engineering decisions shaped by what I learned in the previous experiments. Let me walk through them.

## Architecture decision: layers, not monoliths

In [Day 3](03-prompt-engineering.md), I found that AudioLDM2 handles 2-3 sound elements well but struggles with complex scenes. "Rain falling on a cabin roof with thunder, wind, and a fireplace crackling inside" produced confused results — the model couldn't separate inside from outside.

This led to the core design decision of the app: **don't generate complex scenes in one pass. Generate individual layers and let the user mix them.**

The app has three tabs:

- **Quick Generate** — pick a preset, click, listen. Eight hand-crafted presets using the prompt patterns I discovered in Day 3 (quality modifiers, spatial context, acoustic descriptors).
- **Layer Mixer** — generate up to 3 separate layers, adjust their volumes independently, then mix them into a composite soundscape. This is the power feature. Rain at 80% volume + distant thunder at 50% + a crackling fire at 40% = a cozy rainy evening that the model could never generate in a single prompt.
- **Custom** — full control for experimentation. Custom prompt, adjustable guidance scale and inference steps.

## The presets encode everything I learned

Each preset isn't just a simple description. They're engineered prompts:

```python
PRESETS = {
    "🌧️ Rain": "ambient soundscape of gentle rain falling on a window",
    "🔥 Campfire": "high quality recording of a campfire crackling and popping at night with crickets",
    "🌲 Forest": "ambient soundscape of a forest at night with crickets and a gentle breeze through trees",
}
```

Notice the patterns from Day 3: every preset starts with a quality modifier ("ambient soundscape of", "field recording of", "high quality recording of"), includes specific acoustic descriptors ("crackling and popping", "gentle breeze"), and provides spatial context ("at night", "on a window", "through trees").

## Deployment: the ZeroGPU solution

AudioLDM2 needs a GPU. It's a large model — about 4.5GB of weights — and running diffusion on CPU would take minutes per generation. But GPUs are expensive.

Hugging Face Spaces offers ZeroGPU: a shared GPU pool that allocates a GPU to your app only when someone actually clicks "Generate", then releases it. The tradeoff is a few seconds of cold start, but for a demo that's perfectly fine.

The implementation requires one key change: decorating GPU functions with `@spaces.GPU`:

```python
import spaces

@spaces.GPU
def generate_sound(prompt, preset, seed):
    pipe.to("cuda")
    # ... generation code
```

The model loads into CPU memory when the Space starts. When a user triggers generation, `@spaces.GPU` moves it to a GPU, runs inference, and releases the GPU. Simple and cost-effective.

## The mixing is just numpy

The layer mixing is deliberately simple — weighted averaging of normalized audio arrays:

```python
def mix_layers(audio1, audio2, audio3, vol1, vol2, vol3):
    # Normalize each layer, apply volume
    # Pad to same length
    # Average and normalize to prevent clipping
```

No fancy DSP, no crossfading, no EQ. Just basic mixing. This is intentional: the goal right now is to validate the concept, not build a DAW. If users want better mixing, that's a feature for later.

## What I'd improve

This is a v1. Here's what's missing:

- **Looping**: ambient sounds should loop seamlessly. Right now each generation is an 8-second clip with a hard start and end. Crossfade looping would make it actually usable for focus/relaxation.
- **Longer audio**: 8 seconds is short. AudioLDM2 can generate longer clips but quality degrades. Exploring the AudioLDM2 long-form generation or stitching overlapping clips together would help.
- **Better mixing**: proper gain staging, maybe simple EQ to separate frequency ranges between layers, crossfading.
- **Seed exploration**: generate multiple seeds in parallel and let the user pick the best one, or auto-rank with CLAP similarity.
- **Model comparison**: swap in Stable Audio Open and let users choose which model to use.

## The full stack

For reference, here's what the project looks like now:

- **Model**: AudioLDM2 via HuggingFace Diffusers
- **Interface**: Gradio
- **Hosting**: Hugging Face Spaces with ZeroGPU
- **Blog**: GitHub Pages with Jekyll
- **Code**: [GitHub](https://github.com/my-sonicase/ambientgen)

Total cost: $9/month for HF Pro (for ZeroGPU access). Everything else is free.

## What's next

In the next phase, I'll benchmark AudioLDM2 against other models — specifically Stable Audio Open — to see if we can get better quality. I'll also explore the limitations I found and see which ones are solvable.

---

**Try the app:** [huggingface.co/spaces/sonicase/ambientgen](https://huggingface.co/spaces/sonicase/ambientgen)

*[← Back to blog index](index.md)*
