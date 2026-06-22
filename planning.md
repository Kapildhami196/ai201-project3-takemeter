

# TakeMeter Planning

## 1. Community

My project studies public comments from `r/soccer` about the 2026 FIFA World Cup.

I will collect English-language comments from public `r/soccer` discussions directly connected to actual 2026 World Cup matches or match-related moments.

The final dataset will include comments from:

- 2026 World Cup match threads
- 2026 World Cup post-match threads
- 2026 World Cup goal clips
- 2026 World Cup player or team performance discussions connected to a match
- 2026 World Cup referee decisions connected to a match

I will not collect comments from other subreddits, old World Cups, club football, pre-tournament friendlies, advertisements, promoted posts, usernames, vote counts, or unrelated political discussions.

## 2. Labels

### analysis

A comment is labeled `analysis` when it explains why a team, player, referee, or match event was good, bad, successful, or unsuccessful.

The comment must include meaningful reasoning based on tactics, match events, statistics, comparisons, or a clear cause-and-effect explanation.

Examples:

- “Portugal created nothing because their midfield kept passing sideways and no one made runs behind the defense.”
- “The referee gave no yellow cards to either side, so the missed card was consistent with how he managed the match.”

### hot_take

A comment is labeled `hot_take` when it gives a strong opinion, praise, criticism, ranking, or prediction without enough meaningful explanation.

Examples:

- “Ronaldo is finished and Portugal should never start him again.”
- “Brazil are the most overrated team in the tournament.”

### reaction

A comment is labeled `reaction` when it mainly expresses emotion, shock, excitement, anger, celebration, disappointment, humor, or disbelief without making a meaningful football argument.

Examples:

- “WHAT A GOAL!”
- “No way that referee call happened!”

## 3. Hard Edge Cases

Some comments may contain both an opinion and a small reason.

I will use `analysis` only when the explanation is specific and meaningful.

Examples:

- “Germany are awful because they lost.” → `hot_take`
- “Germany struggled because they left too much space behind their fullbacks.” → `analysis`

Very short emotional comments such as “Insane goal!” will be labeled `reaction`.

Very short claims such as “France are finished” will be labeled `hot_take`.

Comments that are only insults, unrelated politics, advertisements, usernames, or too dependent on missing context will not be collected.

## 4. Data Collection Plan

I will manually collect at least 210 public comments from `r/soccer`.

My goal is to collect about 70 comments for each label:

- about 70 `analysis` comments
- about 70 `hot_take` comments
- about 70 `reaction` comments

Each row in my dataset will contain:

- `id`: a simple row number
- `text`: the public comment text
- `label`: `analysis`, `hot_take`, or `reaction`
- `notes`: a short explanation only when a label is difficult

I will not save usernames, vote counts, advertisements, or promoted content.

I will review every final label manually and use the label rules consistently.

## 5. Evaluation Plan

I will split the dataset into training, validation, and test sets using the required 70/15/15 split.

I will compare:

1. A zero-shot baseline using Groq `llama-3.3-70b-versatile`
2. A fine-tuned DistilBERT classifier

I will evaluate both models using:

- overall accuracy
- precision, recall, and F1 score for each label
- a confusion matrix
- error analysis of at least three wrong predictions

The test set will remain locked until final evaluation.

## 6. Definition of Success

The project will be successful when the fine-tuned model performs better than the zero-shot baseline on the locked test set.

The model should meaningfully separate:

- `analysis` from `hot_take`
- `hot_take` from `reaction`

A strong result will include better overall accuracy than the baseline, understandable errors, and a clear explanation of where the model still struggles.

## 7. AI Tool Plan

I will use AI as a helper for label stress testing and ambiguous-case discussion, but I will manually review every final dataset label.

I will use AI to help identify unclear boundaries, such as whether a comment is a reasoned analysis or only a strong opinion.

I will not use AI-generated comments as training data.

After evaluation, I may use AI to help organize possible error patterns, but my final report will be based on the real model outputs and my own review of the mistakes.