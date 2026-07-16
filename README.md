# RALSS: Rhetorical Structure-Guided Hybrid Extractive–Abstractive Framework for Indian Legal Document Summarization

RALSS is a legal-domain summarization framework for Indian Supreme Court
judgments. It uses rhetorical-role information to select legally important
sentences and then generates abstractive summaries using a fine-tuned
DistilBART model.

This repository accompanies a manuscript submitted to *Expert Systems with
Applications* (ESWA). The manuscript is currently under review and has not
yet been accepted, published, or made available as an article in press.

The repository contains the main RALSS implementation, baseline models, and
additional robustness and length-wise evaluation experiments.

---

## Repository Structure

```text
.
├── github-ralss-code.ipynb
│   └── Main RALSS implementation and ablation experiments
│
├── baseline-models.ipynb
│   └── Extractive baseline models evaluated with the same generator
│
├── robustness-and-length-analysis.ipynb
│   └── Robustness tests and length-wise generalization analysis
│
└── README.md
```

---

## Manuscript Status

The paper associated with this repository has been submitted to
*Expert Systems with Applications* and is currently under review. The work
should not be described as accepted, published, or article-in-press unless
the journal decision changes.

## Main Idea

RALSS follows an extractive-abstractive summarization pipeline:

1. **Rhetorical-role aware sentence selection**
   - Uses sentence-level legal roles such as facts, issues, analysis,
     ratio, and final decision.
   - Gives higher priority to legally important reasoning and decision
     sentences.

2. **Hybrid importance scoring**
   - Combines lexical importance using TF-IDF.
   - Combines semantic relevance using SBERT sentence embeddings.
   - Applies positional and length-based weighting.

3. **Dynamic role-based evidence budgeting**
   - Allocates sentence-selection budget according to rhetorical roles.
   - Helps preserve legally important evidence from long judgments.

4. **Legal discourse ordering**
   - Reorders selected evidence according to the natural flow of legal
     reasoning.

5. **Chunk-based abstractive generation**
   - Selected evidence is passed to a fine-tuned DistilBART model.
   - Long selected inputs are processed in chunks to handle lengthy judgments.

---

## Dataset Format

The code expects a JSON dataset where each document contains the judgment
sentences, rhetorical roles, and reference headnote summary.

Example format:

```json
[
  {
    "case_id": "example_case",
    "judgement_roles": [
      {
        "sentence": "The appeal arises from the judgment of the High Court.",
        "role": "FAC"
      },
      {
        "sentence": "The main issue before the Court was whether...",
        "role": "ISSUE"
      }
    ],
    "headnote_sent": [
      "The Court considered the legal issue and delivered its decision."
    ]
  }
]
```

In the notebooks, update the following paths according to your own Kaggle,
Colab, or local setup:

```python
DATASET_PATH = "/path/to/rhetorical_role_dataset_500.json"
MODEL_PATH = "/path/to/distilbart-9500-output"
```

---

## Installation

The notebooks were prepared for Kaggle/Colab-style execution.

```bash
pip install transformers datasets evaluate sentencepiece
pip install torch
pip install sentence-transformers
pip install nltk spacy
pip install bert_score
pip install textstat
pip install scikit-learn scipy gensim networkx
python -m spacy download en_core_web_sm
```

For BARTScore:

```bash
git clone https://github.com/neulab/BARTScore.git
```

Then add the cloned folder to the Python path inside the notebook:

```python
import sys, os
sys.path.insert(0, os.path.abspath("/kaggle/working/BARTScore"))
```

---

## Main Dependencies

- Python
- PyTorch
- Hugging Face Transformers
- Evaluate
- SentenceTransformers
- scikit-learn
- spaCy
- NLTK
- BARTScore
- BERTScore
- FactCC

---

## How to Run

### 1. Run the main RALSS notebook

Use:

```text
github-ralss-code.ipynb
```

This notebook performs:

- dataset loading
- fine-tuned DistilBART loading
- rhetorical-role aware sentence selection
- structured role-tagged input construction
- chunk-based abstractive generation
- evaluation using ROUGE, BLEU, METEOR, BERTScore, BARTScore, and FactCC
- ablation experiments

---

### 2. Run baseline models

Use:

```text
baseline-models.ipynb
```

This notebook contains extractive baselines such as:

- Keyword Extraction
- POS Tagging
- Sentence Positioning
- Sentence Length
- LSA
- KL Divergence
- LDA
- JS Divergence
- NMF
- TextRank
- LexRank
- Legal-BERT Similarity
- SBERT + KMeans
- BERT-KMeans
- Doc2Vec + MMR
- Submodular Optimization
- RST-based selection
- Legal Concept Graph
- HTPosum
- RankSUM
- GreedSUM
- GETS
- HHGraphSum
- LegalSummNet
- SumHiS
- TermDiffuSumm
- InLegalBERT + KMeans

For fair comparison, all extractive baselines are evaluated using the same
fine-tuned DistilBART generation module.

---

### 3. Run robustness and length-wise analysis

Use:

```text
robustness-and-length-analysis.ipynb
```

This notebook evaluates RALSS under:

- sentence deletion noise
- sentence shuffling
- rhetorical-role perturbation
- irrelevant sentence insertion
- short, medium, and long judgment groups

---

## Evaluation Metrics

The framework is evaluated using lexical, semantic, generation-quality, and
factual-consistency metrics:

| Metric | Purpose |
|---|---|
| ROUGE-1 | unigram overlap |
| ROUGE-2 | bigram overlap |
| ROUGE-L | longest common subsequence overlap |
| BLEU | n-gram precision |
| METEOR | lexical and synonym-aware similarity |
| BERTScore | contextual semantic similarity |
| BARTScore | generation quality |
| FactCC | factual consistency |

---

## Results Reported in the Submitted Manuscript

The following RALSS scores are reported in the submitted manuscript on
500 Indian Supreme Court judgments. These results may be revised during
the peer-review process:

| Metric | Score |
|---|---:|
| ROUGE-1 | 0.5893 |
| ROUGE-2 | 0.3352 |
| ROUGE-L | 0.3034 |
| BLEU | 0.2217 |
| METEOR | 0.3634 |
| BERTScore | 0.8605 |
| BARTScore | -2.9195 |
| FactCC | 0.9848 |

---

## Robustness Results

Average percentage drop under perturbation tests:

| Test | Key Observation |
|---|---|
| Sentence deletion noise | small drop across metrics |
| Sentence shuffling | limited degradation in most metrics |
| Role noise | stable performance under role perturbation |
| Irrelevant sentence insertion | strong resistance to noisy input |

These results indicate that RALSS remains stable when input judgments are
noisy, reordered, or contain irrelevant legal text.

---

## Length-wise Generalization

RALSS is also evaluated on short, medium, and long judgments.

| Judgment Length | ROUGE-1 | ROUGE-2 | ROUGE-L | BLEU | METEOR | BERTScore | FactCC |
|---|---:|---:|---:|---:|---:|---:|---:|
| Short | 0.5803 | 0.3538 | 0.3388 | 0.2311 | 0.3768 | 0.8725 | 0.9854 |
| Medium | 0.6026 | 0.3449 | 0.3084 | 0.2338 | 0.3710 | 0.8605 | 0.9871 |
| Long | 0.5704 | 0.2970 | 0.2583 | 0.1874 | 0.3344 | 0.8489 | 0.9796 |

The results show that RALSS generalizes across different judgment lengths.

---

## Important Notes

- Update all dataset and model paths before running the notebooks.
- BARTScore requires cloning the external BARTScore repository.
- FactCC is loaded from Hugging Face using `manueldeprada/FactCC`.
- GPU execution is recommended because summarization and factual consistency
  evaluation are computationally expensive.
- The dataset and fine-tuned model checkpoint are not included in this
  repository unless explicitly uploaded by the author.
- The related ESWA manuscript is only submitted and under review. Please do
  not cite it as an accepted, published, or article-in-press paper.

---

## Suggested Citation

The related manuscript has been submitted to ESWA and is currently under
review. Until it is accepted or published, please cite this repository or
refer to the work as a submitted manuscript.

```bibtex
@misc{ralss2026,
  title        = {RALSS: Rhetorical Structure-Guided Hybrid Extractive–Abstractive Framework for Indian Legal Document Summarization},
  author       = {Author Name},
  year         = {2026},
  howpublished = {GitHub repository},
  note         = {Manuscript submitted to Expert Systems with Applications; under review}
}
```

---

## License

Add your preferred license here before making the repository public.
For example:

```text
MIT License
```

or

```text
For academic and research use only.
```
