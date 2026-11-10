# Semantic Similarity and Plagiarism Detection

Experiential Learning Group Assignment, **Task 4**
Language Processing Using Machine Learning and LLMs for Audio, Image, Text and Other Applications

**Group 4**

| Name | Roll number |
|---|---|
| Oishani Majumder | 2330316 |
| Shubhradeep Paul | 2330337 |

## What this project does

Takes two documents, finds the sentences that mean the same thing, and labels each match as **Likely copied**, **Paraphrase / same idea** or **Not flagged**. It also shows, with an example, why high semantic similarity is **not** proof of plagiarism: the output is a pointer for a human reviewer, never a verdict.

## How it works

![Pipeline](outputs/pipeline.png)

1. Split both documents into sentences.
2. Embed each sentence with a pretrained Sentence Transformer (384 numbers per sentence).
3. Compare every sentence of A with every sentence of B using cosine similarity.
4. Keep the closest match in B for each sentence in A.
5. Measure word overlap (same words, same order) with `difflib`.
6. Label: meaning >= 0.65 and words >= 0.75 is *Likely copied*; meaning >= 0.65 with fewer shared words is *Paraphrase / same idea*; otherwise *Not flagged*.

## Models and datasets

| Item | Link |
|---|---|
| Model: all-MiniLM-L6-v2 | https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2 |
| Dataset: STS-B (test split) | https://huggingface.co/datasets/sentence-transformers/stsb |
| Dataset: MRPC (validation split) | https://huggingface.co/datasets/nyu-mll/glue (config `mrpc`) |

Nothing is trained. Both datasets are used only for evaluation. A TF-IDF + cosine baseline is used for comparison. The 18 hand-labelled sentence pairs used to test the final label are written in the notebook (section 5.5).

## Results (from `outputs/results.json`)

| Test | Sentence Transformer | Reference |
|---|---|---|
| STS-B Spearman correlation with human scores | 0.820 | TF-IDF 0.693 |
| MRPC **accuracy** (best threshold) | **74.0%** | TF-IDF 70.8%, always "paraphrase" 68.4% |
| MRPC F1 (best threshold) | 0.835 | TF-IDF 0.824, always "paraphrase" 0.812 |
| Three-way labeller **accuracy** (18 hand-labelled pairs) | **94.4%** (17 of 18) | Chance level 33.3% |

Read the MRPC gain as modest: the threshold was tuned on the same 408 pairs, and most MRPC pairs are paraphrases. The labeller test set is small and written by us, so it is a sanity check, not a benchmark. The one mistake was a real paraphrase whose meaning score fell under 0.65. See the report for details and limitations.

## How to run

**Google Colab (recommended)**
1. Open `Semantic_Similarity_Plagiarism_Project.ipynb` in Colab: **[add your Colab link here]**
2. Runtime > Run all (a CPU runtime is enough; the first run downloads the model and datasets).
3. To try your own text, edit `my_a` and `my_b` in section 5.4 and re-run that cell.

**Locally**
```bash
pip install sentence-transformers datasets scikit-learn scipy matplotlib pandas
jupyter notebook Semantic_Similarity_Plagiarism_Project.ipynb
```

## Repository contents

| Path | Contents |
|---|---|
| `Semantic_Similarity_Plagiarism_Project.ipynb` | Full implementation notebook |
| `sample_inputs/` | The two sample documents used in the demo |
| `outputs/` | Pipeline diagram, plots, and `results.json` with all measured numbers |
| `Group4_Project_Report.pdf` / `.docx` | Project report |
| `Group4_Project_Presentation.pptx` / `.pdf` | Presentation slides |
| Presentation video | **[add link here: GitHub Release or accessible video link]** |

## Contributions

- **Oishani Majumder:** model and dataset selection, STS-B and MRPC evaluation, results and limitations in the report.
- **Shubhradeep Paul:** document-comparison pipeline, sample documents, GitHub repository, presentation.

Both members wrote the report together and recorded the presentation together.

## Limitations

Thresholds depend on the text; short paraphrases can fall below the threshold; only the closest sentence is compared; English only; two documents at a time with no web search; the document-level test is small and hand-made.
