# STAG-RAG
### Spatially-Grounded, Topology-Aware Graph-RAG for Multi-Hop Reasoning over 3D Scene Graphs

STAG-RAG is a research framework for improving multi-hop spatial reasoning over 3D scene graphs. It focuses on selecting more relevant object relationships and retrieving connected evidence paths, enabling Large Language Models (LLMs) to answer spatial questions with more compact and structured context.

The project investigates two limitations in existing scene-graph RAG pipelines:

- Fixed distance thresholds for selecting candidate object pairs can overlook object scale, room size, and scene density.
- Similarity-based retrieval can return disconnected facts that fail to provide the complete relationship chain needed to answer multi-hop questions.

STAG-RAG addresses these problems through **adaptive spatial relevance** and **topology-aware multi-hop retrieval**.

---

## Research Overview

Consider a question such as:

> What is lying on the object that is standing on the table?

Answering this requires following multiple relationships in a 3D scene graph. Retrieving individually relevant objects is not enough; the system must identify a connected path of relationships leading from the anchor object to the answer.

STAG-RAG aims to retrieve that path while minimizing irrelevant context.

## Key Contributions

### 1. Adaptive Spatial Relevance

Instead of relying only on a fixed 0.5-meter distance threshold, STAG-RAG ranks candidate object pairs using spatial and geometric information.

The proposed relevance model incorporates:

- Scale-invariant geometric features.
- Object size and relative position.
- Surface contact and spatial relationships.
- Room scale and local scene density.
- A learned relevance scoring function.
- An object-class co-occurrence prior.

The objective is to select more useful object pairs under a constrained relationship-prediction budget.

### 2. Topology-Aware Multi-Hop Retrieval

STAG-RAG retrieves evidence by following the structure of the scene graph rather than relying only on independent semantic similarity scores.

Its retrieval process includes:

- Identifying an anchor object from the question.
- Decomposing the question into ordered relationship hops.
- Traversing the graph hop by hop using beam search.
- Using topology-only expansion when a relationship hop is ambiguous.
- Returning compact evidence associated with complete anchor-to-answer paths.

### 3. 3DSSG-MultiHopQA Benchmark

The project introduces **3DSSG-MultiHopQA**, a benchmark constructed from 3DSSG scene-graph annotations.

It contains:

- 10,679 automatically verified questions.
- 405 3DSSG scenes.
- Quantity, attribute, and relational questions.
- Multi-hop questions with verified answers.
- Minimal evidence chains supporting the answers.

The benchmark is designed to evaluate whether a retrieval method provides enough connected evidence to answer a question.

---

## System Pipeline

The framework consists of four major stages:

1. **Scene-Graph Construction:** Reconstructs object geometry and scene-graph information using the reproduced OSU-3DSG geometric pipeline.
2. **Adaptive Spatial Relevance:** Ranks candidate object pairs for relationship selection.
3. **Topology-Aware Retrieval:** Traverses the scene graph to retrieve compact, connected multi-hop evidence.
4. **LLM Reading and Evaluation:** Uses retrieved evidence to generate answers and evaluate answer accuracy, evidence quality, context size, and computational cost.

The project retains the relevant scene-graph construction approach of OSU-3DSG while replacing its fixed spatial candidate filter and similarity-based retrieval stages.

---

## Experimental Results

The report evaluates STAG-RAG against the reproduced OSU-3DSG baseline and other retrieval configurations.

| Evaluation | Reported result |
|---|---:|
| Multi-hop questions in benchmark | 10,679 |
| Scenes in benchmark | 405 |
| Answerability on unseen scenes | 96.9% |
| OSU-3DSG retrieval answerability | 84.9% |
| STAG-RAG context size | 88 tokens |
| Baseline context size | 375 tokens |
| Real LLM reader accuracy with STAG-RAG | 56.0% |
| Real LLM reader accuracy with baseline retrieval | 46.8% |
| Improvement in relationship recall at equal VLM budget | 25% more true relationships |
| End-to-end answerability with class prior | 50.8% |
| End-to-end answerability without class prior | 20.2% |

The report also finds that increasing the baseline retrieval context does not necessarily improve LLM accuracy.

*These figures are reproduced from the project's reported experiments. See the final-year design project report for the experimental setup, comparisons, and qualifications.*

---

## Technology Stack

The implementation uses:

- **Language:** Python 3.12
- **Data Processing:** NumPy, pandas, SciPy
- **Graph Processing:** NetworkX
- **Machine Learning:** scikit-learn, PyTorch
- **LLM Integration:** Hugging Face Transformers
- **Visualization:** Matplotlib
- **Experiment Environment:** Kaggle Notebooks
- **Data Formats:** JSON, CSV, PNG, Jupyter Notebook

The geometry experiments, spatial filtering, retrieval, and most evaluations run on CPU. The real LLM-reader experiment uses two NVIDIA Tesla T4 GPUs with 16 GB memory each.

---

## Repository Structure

```text
STAG-RAG/
│
├── README.md
├── LICENSE
├── .gitignore
├── requirements.txt
│
├── notebooks/
│   ├── 01_baseline_and_retrieval.ipynb
│   └── 02_path_coverage_and_llm.ipynb
│
├── src/
│   ├── spatial_relevance/
│   ├── graph_retrieval/
│   ├── benchmark/
│   └── evaluation/
│
├── data/
│   └── README.md
│
├── results/
│   ├── tables/
│   └── figures/
│
└── docs/
    └── methodology.png
```

*The structure above is a suggested organization. Adapt it to the actual implementation files included in this repository.*

---

## Reproducing the Experiments

The original experiments were conducted using Kaggle resources. The two experiment notebooks are organized as follows:

**Notebook 1: Baseline, Geometry, Benchmark, and Retrieval**

Covers Stage-1 reproduction, spatial filtering experiments, benchmark generation, retrieval experiments, and end-to-end evaluation.

**Notebook 2: Path-Coverage Training and LLM Reader**

Covers the learned path-coverage filter and the real LLM-reader experiment.

### Steps

1. Clone this repository:

   ```bash
   git clone https://github.com/rose777-ui/STAG-RAG.git
   cd STAG-RAG
   ```

2. Open the experiment notebooks in Jupyter or upload them to Kaggle.

3. Obtain the 3DSSG and required 3RScan data from their respective official sources, subject to their terms of use.

4. Configure the dataset paths or attach the corresponding private Kaggle datasets.

5. Run the notebooks in order.

6. Inspect the generated CSV tables, PNG figures, benchmark JSON, and evaluation outputs.

The original notebooks were designed to use libraries pre-installed in the Kaggle environment. A local environment may require compatible dependency versions and additional configuration.

---

## Dataset and Reproducibility Notes

This project uses:

- **3DSSG:** Object categories, attributes, and
