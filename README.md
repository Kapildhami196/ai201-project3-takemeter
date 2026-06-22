# TakeMeter

TakeMeter is a text-classification project that analyzes the style of public World Cup football discussion from Reddit's `r/soccer` community.

It compares two approaches on the same locked test set:

- A zero-shot baseline using Groq's `llama-3.3-70b-versatile`
- A fine-tuned `distilbert-base-uncased` classifier

## Labels

Every comment is assigned to exactly one mutually exclusive label.

| Label | Definition |
|---|---|
| `analysis` | Meaningful football reasoning about tactics, match events, player roles, rules, statistics, or cause and effect. |
| `hot_take` | A strong opinion, criticism, praise, prediction, ranking, or judgment without enough meaningful explanation. |
| `reaction` | Emotion, humor, celebration, sarcasm, disbelief, wordplay, or a short response without a football argument. |

## Dataset

The dataset contains **210 manually annotated public English-language comments** from 2026 FIFA World Cup-related `r/soccer` match threads and post-match threads.

Dataset file:

```text
data/world_cup_comments.csv
```

### Label distribution

| Label | Examples |
|---|---:|
| `analysis` | 70 |
| `hot_take` | 70 |
| `reaction` | 70 |
| **Total** | **210** |

The CSV fields are:

```text
id,text,label,notes
```

- `id`: unique comment identifier
- `text`: exact public comment text
- `label`: manually assigned class
- `notes`: concise annotation rationale

## Example annotations

| Comment | Label | Why |
|---|---|---|
| “Spain struggled because Cape Verde stayed organized in a low block and Spain could not create space.” | `analysis` | Explains a tactical cause-and-effect relationship. |
| “Uruguay are a mediocre team now.” | `hot_take` | Strong judgment without enough supporting explanation. |
| “The World Cup of draws” | `reaction` | Humorous reaction rather than football reasoning. |

## Data split

The dataset was split with stratification and a fixed seed of `42`.

| Split | Examples | Purpose |
|---|---:|---|
| Training | 147 | Fine-tune DistilBERT |
| Validation | 31 | Select the best checkpoint |
| Test | 32 | Final evaluation for both models |

The locked test set contains 10 `analysis`, 11 `hot_take`, and 11 `reaction` examples.

## Models

### Zero-shot baseline

The baseline uses Groq's `llama-3.3-70b-versatile` model. It receives definitions and examples for the three labels, then returns exactly one label: `analysis`, `hot_take`, or `reaction`.

### Fine-tuned model

The fine-tuned classifier uses `distilbert-base-uncased`.

| Setting | Value |
|---|---|
| Epochs | 8 |
| Learning rate | `2e-5` |
| Training batch size | 16 |
| Evaluation batch size | 32 |
| Weight decay | `0.01` |
| Warmup ratio | `0.1` |
| Random seed | 42 |
| Best checkpoint selection | Validation accuracy |

## Evaluation results

Both models were evaluated on the same locked 32-comment test set.

| Model | Accuracy | Correct predictions |
|---|---:|---:|
| Groq zero-shot baseline | **0.906** | **29 / 32** |
| Fine-tuned DistilBERT | **0.625** | **20 / 32** |

The fine-tuned model improved substantially over the initial three-epoch attempt, but it did not outperform the larger instruction-following zero-shot baseline.

Complete saved metrics:

```text
outputs/evaluation_results.json
```

## Confusion matrix

![Fine-Tuned Model Confusion Matrix](outputs/confusion_matrix.png)

The image file is also stored at `outputs/confusion_matrix.png`.

## Error analysis

### Broad judgment mistaken for analysis

> “Uruguay is always fools gold like Belgium is. Very talented players but that team washes out sooner or later”

- True label: `hot_take`
- Fine-tuned prediction: `analysis`

The wording sounds explanatory, but it is mainly a broad unsupported team judgment.

### Sarcasm mistaken for analysis

> “As a lifelong Germany fan for the past 90 minutes, this means so much”

- True label: `reaction`
- Fine-tuned prediction: `analysis`

This is sarcasm and humor. The model treated a longer sentence as serious commentary.

### Tactical explanation mistaken for a hot take

> “This will always happen to Spain until they learn to beat a low block”

- True label: `analysis`
- Fine-tuned prediction: `hot_take`

The comment makes a tactical claim about Spain's difficulty against compact defending, but its short confident wording resembles an opinion.

## Key findings

1. Groq's zero-shot baseline substantially outperformed fine-tuned DistilBERT on this small dataset.
2. The boundaries between concise tactics, opinions, sarcasm, and humor are subtle.
3. More labeled examples of sarcasm, wordplay, and short emotional comments would likely improve the fine-tuned model.
4. A classifier trained on 147 examples may not outperform a large instruction-following model on nuanced community-discussion labels.

## Repository structure

```text
ai201-project3-takemeter/
├── data/
│   └── world_cup_comments.csv
├── notebooks/
│   └── takemeter_final_experiment.ipynb
├── outputs/
│   ├── confusion_matrix.png
│   └── evaluation_results.json
├── planning.md
└── README.md
```

## Run the experiment

1. Open `notebooks/takemeter_final_experiment.ipynb` in Google Colab.
2. Select a **T4 GPU** runtime.
3. Upload `data/world_cup_comments.csv`.
4. Add `GROQ_API_KEY` through Colab Secrets.
5. Run the notebook cells in order.
6. Review the evaluation results, confusion matrix, and error-analysis examples.

## Ethical notes

- The dataset contains only public `r/soccer` comments.
- Usernames, voting information, advertisements, and unrelated thread content were excluded.
- Comments containing slurs or harmful content were excluded.
- This project is for educational analysis of public football discussion and is not intended to judge individual users.