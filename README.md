# Emotionally Intelligent Dialogue Modeling

**SFT + Emotion & Strategy Heads + DPO + EQ-Bench Evaluation**

This repository presents an end-to-end pipeline for building, aligning, and evaluating an **emotionally intelligent conversational model**. The work combines **supervised fine-tuning**, **explicit emotional supervision**, **preference optimization**, and **EQ-Bench evaluation**.

---

## Motivation

Large Language Models (LLMs) trained with standard supervised fine-tuning (SFT) often produce fluent but emotionally shallow responses. This project investigates whether **explicit emotional and conversational strategy supervision**, combined with **Direct Preference Optimization (DPO)**, leads to **measurable improvements in emotional intelligence (EQ)**.

---

## System Overview

The pipeline consists of four major stages:

1. **Supervised Fine-Tuning (SFT)** on empathetic dialogue data
2. **Auxiliary Heads**
   - Emotion Classification Head (GoEmotions)
   - Strategy Classification Head (ESConv)
3. **Direct Preference Optimization (DPO)**
4. **EQ-Bench Evaluation**

Each component is modular and independently reusable.

---

## Base Model & Training Stack

- **Base Model**: GPT-style causal language model
- **Training Framework**: Unsloth
- **Parameter Efficiency**: PEFT / LoRA adapters
- **Hardware**: Kaggle T4 / P100 (memory-constrained setup)

---

## Datasets

### Supervised Fine-Tuning

- `DianaW/empathetic_dialogues`
- `Ashokajou51/ESConv_Original`
- `google-research-datasets/go_emotions`

Datasets were normalized into a unified text format and mixed using **temperature-based sampling** to avoid dominance by larger corpora.

### DPO Dataset

Preference pairs extracted from ESConv-style conversations:

```json
{
  "prompt": "...",
  "chosen": "empathetic response",
  "rejected": "less appropriate response"
}
```

---

## Auxiliary Heads

### Emotion Head

- Predicts 28 GoEmotions classes
- Trained on frozen base model embeddings
- **Architecture**: LayerNorm → Linear → ReLU → Linear

### Strategy Head

- Predicts dialogue strategies (reflection, affirmation, questioning, etc.)
- Trained on ESConv dataset
- Uses the same pooled embedding pipeline as the emotion head

Both heads are trained independently and saved as `.pt` checkpoints.

---

## Direct Preference Optimization (DPO)

DPO is used to align the model with human-preferred responses beyond SFT.

- Implemented using Unsloth's `DPOTrainer`
- Adapter-based training
- Improves consistency and response preference alignment

---

## Evaluation: EQ-Bench

Evaluation is performed using **EQ-Bench** (`pbevan11/EQ-Bench`), a benchmark designed to assess emotional intelligence in generated responses.

### EQ Scoring Method

For each prompt:

1. Generate a model response
2. Extract pooled embeddings
3. Score the response with the emotion and/or strategy heads
4. Compute an EQ score based on:
   - Empathy-related emotion mass
   - Optional alignment with human preference signals

**Final EQ score**:

```
EQ = empathy_mass + 0.3 × alignment
```

---

## Results

Two result files are included in this repository:

### `results.xlsx` — 30-Sample EQ-Bench Run

- Used for sanity checking and debugging
- Confirms relative trends across model variants
- Includes comparisons for:
  - Base DPO model
  - Base + Emotion Head
  - Base + Strategy Head
  - Base + Emotion + Strategy Heads

### `results2.xlsx` — Full EQ-Bench Run

- Computed on all available EQ-Bench data points
- Used for final quantitative evaluation
- Represents the primary result set for conclusions

---

## Summary

This project demonstrates that:

**Explicit emotional and conversational strategy supervision, combined with DPO, leads to more emotionally intelligent dialogue models than SFT alone.**

The pipeline is modular, reproducible, and suitable for further research or production experimentation.

---

## License

This repository is intended for research and educational use. All datasets and base models follow their respective Hugging Face licenses.
