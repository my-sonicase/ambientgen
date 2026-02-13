# AmbientGen 🌧️

> A text-to-ambient-sound generator powered by AudioLDM2

Type a description like *"gentle rain on a window with distant thunder"* and get a generated ambient soundscape.

## 🚀 Try it

**[Live Demo on Hugging Face Spaces →](https://huggingface.co/spaces/sonicase/ambientgen)**

## 🎯 What is this?

This is a learning-in-public project documenting my journey into Generative AI for Audio. I'm building a text-to-ambient-sound app while studying the underlying models, papers, and techniques.

**Features:**
- 🎧 **Quick Generate** — 8 hand-crafted ambient presets, one click to generate
- 🎛️ **Layer Mixer** — generate up to 3 sound layers and mix them with volume control
- ✍️ **Custom** — write your own prompts with adjustable guidance scale and inference steps

## 📝 Blog

Follow the build process on the [project blog](https://my-sonicase.github.io/ambientgen/blog/):

1. [What is AudioLDM2 and why I'm using it](blog/01-what-is-audioldm2.md)
2. [First Sounds — What AudioLDM2 Can and Can't Do](blog/02-first-sounds.md)
3. [Prompt Engineering for Audio — What Actually Works](blog/03-prompt-engineering.md)
4. [Building AmbientGen — From Notebook to Product](blog/04-building-the-interface.md)

## 📚 Reading List

A curated list of papers and resources on generative AI for audio and music → [Papers & Resources](docs/papers.md)

## 🛠️ Tech Stack

- **Model:** [AudioLDM2](https://huggingface.co/cvssp/audioldm2) via HuggingFace Diffusers
- **Interface:** Gradio
- **Deploy:** Hugging Face Spaces (ZeroGPU)
- **Blog:** GitHub Pages + Jekyll

## Project Structure

```
ambientgen/
├── blog/           # Blog posts (Markdown)
├── app/            # Gradio application code
├── experiments/    # Colab notebooks & experiment logs
├── docs/           # Papers reading list & resources
└── README.md
```

## License

MIT
