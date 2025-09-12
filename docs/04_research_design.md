You are absolutely correct. Based on our iterative optimization, the research design has become more efficient and robust. The separate "Feature Engineering" step is no longer necessary because the main extraction pipeline now incorporates a superior, sentence-level "coarse-to-fine" filtering mechanism.

This is a significant improvement to the research design. It's simpler, faster, and, most importantly, reduces the risk of missing critical signals.

Here is the updated research design that reflects the final, optimized code.

***

## **Updated Research Design: An Implementation Plan**

### Introduction and Objective

This document operationalizes the **Research Model** by providing a detailed implementation plan for the Food Crisis Early Warning System. The objective is to translate the **six-stage conceptual framework** into a concrete set of data processing steps, model selections, and analytical techniques. This research design will serve as the direct blueprint for the accompanying Jupyter Notebooks, ensuring that the implementation is rigorous, reproducible, and directly aligned with the project's goals.

---

### **Stage 1: Exploratory Data Analysis (EDA) and Data Preprocessing**

* **Associated Notebook:** `01_EDA.ipynb`, `02_data_preprocessing.ipynb`
* **Objective:** To understand the data's fundamental properties and to perform initial text cleaning and sentence tokenization.
* **Implementation:**
    * **EDA:** All data files will be loaded into `pandas` DataFrames. A comprehensive analysis will be performed to inspect data structures, check for missing values, and visualize key distributions.
    * **Preprocessing:** A standardized function will be applied to clean the text by removing HTML tags, URLs, and extra whitespace. A custom regular expression will then be used to segment the cleaned text into sentences. The output is a clean, sentence-tokenized dataset for all articles.

---

### **Stage 2: Two-Stage Risk Factor Extraction**

* **Associated Notebook:** `03_risk_factor_extraction.ipynb`
* **Objective:** To efficiently and accurately identify specific risk factor mentions within the entire corpus using a two-stage, coarse-to-fine filtering process at the sentence level.
* **Execution:** This stage replaces the previous Feature Engineering and Model Selection steps with a single, highly optimized pipeline:
    1.  **Coarse Filtering (Semantic Pre-selection):** A lightweight, multilingual model (`paraphrase-multilingual-MiniLM-L12-v2`) encodes all sentences from all articles. A high-speed `util.semantic_search` compares these sentences against the 167 risk factors, keeping only those with a similarity score above a tuned threshold of **0.55**.
    2.  **Fine Classification (Deep Analysis):** The smaller subset of relevant sentences is then processed by a powerful cross-lingual zero-shot classification model (`MoritzLaurer/deberta-v3-xsmall-zeroshot-v1.1-all-33`). This model classifies sentences against the risk factors, requiring a high confidence threshold of **0.90** for a positive match.
    3.  **Post-Processing (Refinement):** To ensure a clean signal, a final step selects only the single, highest-confidence risk factor for each unique sentence.
* **Output:** A refined `pandas` DataFrame with columns: `article_id`, `date`, `sentence_text`, `risk_factor`, and `confidence_score`, representing high-confidence, unique risk mentions.



---

### **Stage 3: Geospatial Intelligence and Hotspot Identification**

* **Objective:** To link textual risk signals to specific geographic districts and identify statistically significant spatial clusters.
* **Model & Libraries:** `spaCy`'s `xx_ent_wiki_sm` model for Geographic Named Entity Recognition (GNER), along with `geopandas` and `esda` for analysis.
* **Execution:** `GPE` entities will be extracted from articles and resolved against location dictionaries. The Getis-Ord Gi\* statistic will then be calculated on the geolocated risk counts to identify and visualize hotspots.

---

### **Stage 4: Hierarchical Risk Index Construction**

* **Objective:** To aggregate the discrete risk events into a continuous, quantitative time-series metric for monitoring.
* **Implementation (`pandas`):**
    * **Aggregation:** Data will be grouped by `date` and `district_key` to get daily raw counts of risk factors.
    * **Normalization:** Raw counts will be normalized by the total article count per district per day.
    * **Index Calculation:** Thematic and Composite Risk Indices (CRI) will be calculated by averaging the normalized scores based on category mappings.

---

### **Stage 5: Unsupervised Anomaly Detection**

* **Objective:** To automatically flag unusual spikes in risk, demonstrating the system's utility as an early warning tool.
* **Algorithm:** A **moving average-based z-score** method.
* **Implementation:** A 7-day rolling window will be used to calculate the moving average and standard deviation for each district's CRI. An anomaly will be flagged if the **z-score is greater than 2.0**.