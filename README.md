# Comparing open-weight models for stance in multimodal social-media posts

This repository contains the code for analysing posts that combine text and images (memes). It compares text models, vision models, a dual-encoder model (CLIP) and a vision-language model (SmolVLM) on one task: classifying the stance of social-media posts on vaccine mandates.

All models are open-weight, so every result can be reproduced on a free GPU.

## What the notebook does

Every pretrained model is kept **frozen** and turns each post into a vector of numbers. The same logistic regression is trained on every set of vectors (a **linear probe**), using the same cross-validation splits and the same score (macro-F1). Differences in score therefore come from the models, not from the training setup.

| | Comparison | Systems | Scored on |
|---|---|---|---|
| C1 | Text models | RoBERTa-base, GPT-Neo-125M, TinyLlama-1.1B, CLIP Text | message label |
| C2 | Vision models | ResNet18, ViT-B/16, CLIP Image | image label |
| C3 | Cross-Modal dual encoder | CLIP text only, image only, text + image | both labels |
| C4 | Vision-Language Model | SmolVLM-256M and -500M × text only, image only, text + image | both labels |

A majority-class reference (always predicting the most common class) is reported in every table. The supplement adds a two-model fusion (RoBERTa + ResNet18), SmolVLM vs CLIP, per-class F1, and balanced accuracy.

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
3. Create the folder `/content/study` and upload the three input files into it: `text.xlsx`, `images.xlsx`, `images.zip`.
4. **Runtime > Run all.** A full run takes about 15–20 minutes on a T4 GPU. Everything is computed fresh; no saved features are reused.

Tables and figures are written to `/content/study/results`.

## Input format

| File | Contents |
|---|---|
| `text.xlsx` | One row per post. Columns: `ID`, `Message` (post text), `Label_message` (stance of the text) |
| `images.xlsx` | One row per post. Columns: `ID`, `Image_label` (stance of the image) |
| `images.zip` | One image per post, named by its ID (e.g. `17.jpg`); |

Labels must be one of: `Anti-Mandate`, `Pro-Mandate`, `Neutral`, `Other`. Column names and image naming can be changed in the **Settings** cell.

## Data

The data is shared in data folder, it has three input files: text.csv, image.csv and images.zip

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
