# Comparing open-weight models for stance in multimodal social-media posts

This repository contains the code for analysing posts that combine text and images (memes). It compares text models, vision models, a dual-encoder model (CLIP) and a vision-language model (SmolVLM) on one task: classifying the stance of social-media posts on vaccine mandates.

All models are open-weight, so every result can be reproduced on a free GPU.

**Below are some comparisons of the modality done in this notebook:**

| |Comparison | Models | |
|---|---|---|---|
|  | Text models | RoBERTa-base, GPT-Neo-125M, TinyLlama-1.1B, CLIP Text ||
|  | Meme models | ResNet18, ViT-B/16, CLIP meme | |
|  | CLIP: text only, meme only, text + meme | CLIP Text, CLIP meme, CLIP Text + meme | |
|  | SmolVLM: input type and model size | 256M and 500M, each with text only, meme only, text + meme | |

A majority-class reference (always predicting the most common class) is reported in every table. The supplement adds SmolVLM vs CLIP Analysis

**Evaluation:** stratified group 5-fold cross-validation repeated 5 times (identical texts kept together), macro-F1, and 95% bootstrap intervals (1,000 resamples; paired for differences). Fixed random seed, so every run gives the same results.

## Repository contents

```
multimodal_model_comparison.ipynb   the full analysis (one notebook)
requirements.txt                    Python packages
results/                            tables (CSV), Figure 1 (PNG, PDF), package versions
results/supplement/                 supplementary tables
data/                               see "Data" below
```

## How to run

### Google Colab 

1. Open the notebook in Colab (use the "Open in Colab" button at the top of the notebook).
2. Choose a GPU: **Runtime > Change runtime type > T4 GPU**.
3. Create the folder `/content/study` and upload the three input files into it: `post-text.xlsx`, `memes.xlsx`, `memes.zip`.
4. **Runtime > Run all.** A full run takes about 15–20 minutes on a T4 GPU. Everything is computed fresh; no saved features are reused.

Tables and figures are written to `/content/study/results`.


## Data

The data is shared in data folder, it has three input files: post-text.csv, memes.csv and memes.zip

## Models

All models are downloaded automatically from Hugging Face or torchvision when the notebook runs.

| Model | Identifier |
|---|---|
| RoBERTa-base | `roberta-base` |
| GPT-Neo-125M | `EleutherAI/gpt-neo-125M` |
| TinyLlama-1.1B | `TinyLlama/TinyLlama-1.1B-Chat-v1.0` |
| ResNet18, ViT-B/16 | torchvision, default ImageNet weights |
| CLIP | `openai/clip-vit-base-patch16` |
| SmolVLM-256M, SmolVLM-500M | `HuggingFaceTB/SmolVLM-256M-Instruct`, `HuggingFaceTB/SmolVLM-500M-Instruct` |

## Reproducibility

- One fixed random seed (42) for the splits, the classifier and the bootstrap.
- The exact package versions used for the reported results are in `results/versions.txt`.
- Small differences in the last decimal can occur on different GPUs or package versions.
