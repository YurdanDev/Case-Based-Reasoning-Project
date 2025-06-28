# Case-Based Reasoning for Legal Case Retrieval and Prediction (Persecution)

- 202110370311452 - Ahmad Alif Dzaky F.
- 202110370311455 - Muhammad Yurdan Asy Shadzili


 This repository contains the implementation of a Case-Based Reasoning (CBR) system for retrieving and predicting legal case outcomes based on court decision documents from the Indonesian Supreme Court (Mahkamah Agung RI). The pipeline consists of five stages: Case Base Building, Case Representation, Case Retrieval, Solution Reuse, and Model Evaluation.


![case-based-reasoning-diagram](https://github.com/user-attachments/assets/a7f21edf-ce12-4eb5-ab76-5285fb79d03a)


## Table of Contents

- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Project Structure](#project-structure)
- [Pipeline Stages](#pipeline-stages)
  - [Stage 1: Case Base Building](#stage-1-case-base-building)
  - [Stage 2: Case Representation](#stage-2-case-representation)
  - [Stage 3: Case Retrieval](#stage-3-case-retrieval)
  - [Stage 4: Solution Reuse](#stage-4-solution-reuse)
  - [Stage 5: Model Evaluation](#stage-5-model-evaluation)
- [Running the Full Pipeline](#running-the-full-pipeline)
- [Example Commands](#example-commands)


## Prerequisites

Sebelum menjalankan proyek ini, pastikan Anda memiliki:

- **Python 3.8 atau lebih tinggi**
- **Akses internet** untuk mengunduh dependencies dan melakukan scraping putusan pengadilan
- **Minimal 8GB RAM** untuk pemrosesan embedding BERT
- **Opsional: GPU** untuk mempercepat komputasi embedding menggunakan IndoBERT


## Installation

1. **Clone the Repository:**
```bash
git clone https://github.com/DzKy962/Case-Based-Reasoning-Project.git
cd Case-Based-Reasoning-Project
```
2. **Create a Virtual Environment (recommended):**
```bash  
python -m venv venv
# On Linux/Mac:
source venv/bin/activate
# On Windows:
venv\Scripts\activate
```
3. **install Dependencies: The project requires several Python libraries listed in requirements.txt. Install them using:**
```bash
!pip install -r requirements.txt
```
```bash
pandas
numpy
requests
beautifulsoup4
pdfminer.six
Sastrawi
scikit-learn
transformers
torch
sentence-transformers
faiss-cpu
rank_bm25
matplotlib
```

4. **Download Pre-trained IndoBERT Model**
```bash
from transformers import AutoTokenizer, AutoModel

tokenizer = AutoTokenizer.from_pretrained("indobenchmark/indobert-base-p2")
model = AutoModel.from_pretrained("indobenchmark/indobert-base-p2")
```

## Project Structure

```text
CBR-Project/
├── data/
│   ├── eval/                # Metrics, queries, failure cases
│   ├── preprocessed/        # cleaned_putusan_hasil.csv
│   ├── processed/           # cases.csv, cases.json, embeddings.json
│   ├── raw/                 # Raw data: CSV, PDF, case_***.txt
│   └── result/              # Predictions
│
├── Tahap 1 – Membangun Case Base.ipynb         # Stage 1
├── Tahap 2 – Case Representation.ipynb         # Stage 2
├── Tahap 3 – Case Retrieval.ipynb              # Stage 3
├── Tahap 4 – Solution Reuse.ipynb              # Stage 4
├── Tahap 5 – Model Evaluation.ipynb            # Stage 5
│
├── logs/
│   └── cleaning.txt
│
├── logs.txt
├── requirements.txt
└── README.md
```

## Pipeline Stages

### Stage 1: Case Base Building

**Objective:** Scrape and clean court decision documents from the Indonesian Supreme Court website.

**Tasks:**

- Scrape ≥30 PDF documents for a specific case domain (e.g., *Penganiayaan PN Medan*)
- Convert PDFs to text using `pdfminer.six`
- Clean text (remove headers, footers, watermarks, normalize spaces/characters)
- Save cleaned text as `case_001.txt`, `case_002.txt`, etc., in `data/raw/`
- Generate `logs/cleaning.log` for the cleaning process (optional)

**Script:** `Tahap 1 – Membangun Case Base.ipynb `

---

### Stage 2: Case Representation

**Objective:** Represent cases in a structured format.

**Tasks:**

- Extract metadata (e.g., case number, date, parties, articles)
- Extract key content (e.g., case summary, legal arguments)
- Perform feature engineering (word count, TF-IDF, IndoBERT embeddings, QA-pairs)
- Save to `data/processed/cases.csv` and `data/processed/cases.json`

**Script:** `Tahap 2 – Case Representation.ipynb`

---

### Stage 3: Case Retrieval

**Objective:** Retrieve top-k similar cases for a new query.

**Tasks:**

- Generate TF-IDF and IndoBERT embeddings
- Split data into train/test (80:20 ratio)
- Implement retrieval using:
  - Naive Bayes (on TF-IDF)
  - IndoBERT (via FAISS)
  - BM25
- Define function: `retrieve(query: str, k: int = 5)`
- Prepare 5–10 test queries and save to `data/eval/queries.json`

**Script:** `Tahap 3 – Case Retrieval.ipynb`

---

### Stage 4: Solution Reuse

**Objective:** Predict outcomes for new cases based on similar past cases.

**Tasks:**

- Extract solutions (penalties) from top-k cases
- Apply majority vote or weighted similarity for prediction
- Implement function: `predict_outcome(query: str)`
- Test with 5 new queries and compare with ground truth
- Save results to `data/result/predictions.csv`

**Script:** `Tahap 4 – Solution Reuse.ipynb`

---

### Stage 5: Model Evaluation

**Objective:** Evaluate retrieval and prediction performance.

**Tasks:**

- Measure **Accuracy**, **Precision**, **Recall**, and **F1-score**
- Create metrics table comparing models (Naive Bayes, TF-IDF, BERT, BM25)
- Generate line chart for performance visualization
- Perform error analysis and save failure cases
- Save metrics to `data/eval/metrics.csv`

**Script:** `Tahap 5 – Model Evaluation.ipynb`



## 📦 Running the Full Pipeline

Untuk menjalankan seluruh pipeline secara berurutan, eksekusi setiap script tahap demi tahap. Pastikan output dari setiap tahap tersedia sebelum melanjutkan ke tahap berikutnya.

---

## 🛠️ 1. Prepare the Environment

Aktifkan environment dan install dependencies:

```bash
source venv/bin/activate  # Windows: venv\Scripts\activate
pip install -r requirements.txt
```
```bash
python Tahap 1 – Membangun Case Base.ipynb
python Tahap 2 – Case Representation.ipynb
python Tahap 3 – Case Retrieval.ipynb
python Tahap 4 – Solution Reuse.ipynb
python Tahap 5 – Model Evaluation.ipynb
```

Catatan: Pastikan output dari setiap tahap tersedia (misalnya data/raw/*.txt dari Tahap 1, data/processed/cases.csv dari Tahap 2) sebelum lanjut ke tahap berikutnya.

## Example Commands

Stage 1: Scrape and Clean Data:
```bash
python Tahap 1 – Membangun Case Base.ipynb
```
- `data/raw/case_*.txt, cleaned_putusan_hasil, logs/cleaning.log`

Stage 2: Represent Cases:
```bash
python Tahap 2 – Case Representation.ipynb
```
- `Output: data/processed/cases.csv, data/processed/cases.json, data/processed/embeddings.json`

Stage 3: Retrieve Similar Cases:
```bash
python Tahap 3 – Case Retrieval.ipynb
```
- `Output: data/eval/queries.json, data/eval/retrieval_metrics.csv, data/eval/failure_cases_retrieval_*.json`

Stage 4: Predict Outcomes:
```bash
python Tahap 4 – Solution Reuse.ipynb
```
- `Output: data/results/predictions.csv, logs.txt`

Stage 5: Evaluate Models:
```bash
python Tahap 5 – Model Evaluation.ipynb
```
- `Output: data/eval/metrics.csv, data/eval/metrics_line_chart.png, data/eval/failure_cases_*.json`

Example Query Prediction: Modify Tahap 4 – Solution Reuse.ipynb to test a custom query:

```bash
# Configuration
RETRIEVAL_METHOD = "tfidf"  # "nb" NaiveBayes, "tfidf" TF-IDF, "bert" IndoBERT, and "bm25" BM25
PREDICTION_STRATEGY = "majority"  # Weighted or majority for better scores
SIMILARITY_THRESHOLD = 0.1  # Minimum: 0.0 (includes all cases, regardless of similarity).Maximum: 1.0 (requires perfect similarity, rarely achieved).

# Define new queries with some known ground truths for evaluation
new_queries = [
    {"query_id": 1, "query": "Terdakwa melakukan penganiayaan ringan tanpa luka di tempat umum", "ground_truth": "penjara 1 tahun"},
    {"query_id": 2, "query": "Terdakwa bersama-sama melakukan pengeroyokan menyebabkan luka berat", "ground_truth": "penjara 3 tahun"},
    {"query_id": 3, "query": "Terdakwa melakukan penggelapan dalam jabatan senilai 10 juta", "ground_truth": "penjara 1 tahun"},
    {"query_id": 4, "query": "Terdakwa menghasut orang untuk melakukan kekerasan", "ground_truth": "penjara 2 tahun"},
    {"query_id": 5, "query": "Terdakwa melakukan kekerasan berat terhadap anak di bawah umur", "ground_truth": "penjara 5 tahun"},
    {"query_id": 6, "query": "Terdakwa membantu tetangga menyeberang jalan", "ground_truth": "None"}
]
```
bisa di custom querynya sesuai dengan klasifikasi pidana penganiayaan
