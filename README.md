# AmbientGen 🌧️

> A text-to-ambient-sound generator powered by AudioLDM2

Type a description like *"gentle rain on a window with distant thunder"* and get a generated ambient soundscape.

## 🎯 What is this?

This is a learning-in-public project documenting my journey into Generative AI for Audio. I'm building a text-to-ambient-sound app while studying the underlying models, papers, and techniques.

## 📝 Blog

Follow the build process on the [project blog](blog/index.md):

1. [What is AudioLDM2 and why I'm using it](blog/01-what-is-audioldm2.md)
2. [Day 2: First Sounds — What AudioLDM2 Can and Can't Do](02-first-sounds.md)
3. [Day 3: Prompt Engineering for Audio — What Actually Works](03-prompt-engineering.md)


## 📚 Reading List

A curated list of papers and resources on generative AI for audio and music → [Papers & Resources](docs/papers.md)

## 🛠️ Tech Stack

- **Model:** [AudioLDM2](https://huggingface.co/cvssp/audioldm2) via HuggingFace Diffusers
- **Interface:** Gradio
- **Deploy:** Hugging Face Spaces
- **Blog:** GitHub Pages + Jekyll

## 🚀 Try it

*Demo link coming soon on Hugging Face Spaces*

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
