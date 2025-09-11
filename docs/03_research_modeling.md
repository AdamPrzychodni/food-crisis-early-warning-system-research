# **Research Modeling: A Conceptual Framework**

## 1. Objective and Guiding Principles

The primary objective of this research model is to outline the conceptual architecture for an end-to-end pipeline that transforms raw, multilingual news articles into a dynamic, geographically-aware early warning system for food insecurity. This framework directly addresses the core challenges of the assessment—namely, the absence of ground-truth data, the use of multilingual text, and the need to demonstrate the value of geospatial analysis—by drawing upon established academic research.

Our approach is guided by two core principles derived from the literature:

1.  **Lexicon-Based Feature Extraction:** Following the methodology of **Balashankar, A., et al. (2023)**, the model's foundation will be the systematic extraction of the 167 predefined risk factors from news text to create high-frequency, quantitative "news indicators."
2.  **Unsupervised Anomaly Detection:** In line with the work of **van Wanrooij, C., et al. (2024)**, and to address the lack of ground-truth data, the final output will be an unsupervised system. The goal is not to predict a specific food insecurity phase, but to detect statistically significant anomalies in the volume and clustering of risk reporting, thereby flagging potential crises for further investigation.

---

## 2. The Five-Stage Conceptual Model

The proposed system is structured as a **five-stage conceptual model**, where the output of each stage serves as the input for the next. This modular design ensures clarity, interpretability, and a logical progression from raw data to actionable intelligence.

### **Stage 1: Exploratory Data Analysis (EDA) and Preprocessing**

* **Objective**: To understand the fundamental statistical, temporal, and geospatial properties of the datasets and to clean the text for subsequent analysis.
* **Methodology**: This foundational stage involves a comprehensive inspection of all data sources to identify patterns, distributions, and potential data quality issues. Key activities include analyzing the frequency of news articles over time and across different geographic regions and examining the prevalence of predefined risk factors. Text data is cleaned by removing irrelevant characters and segmented into sentences to prepare it for NLP models.
* **Input**: All raw data files (`.csv`, `.xlsx`, `.pkl`).
* **Output**: Clean, preprocessed, sentence-tokenized text data.

### **Stage 2: Multilingual Risk Factor Extraction using a Coarse-to-Fine Approach**

* **Objective:** To accurately and efficiently identify mentions of the 167 English risk factors within both the English and Arabic news corpora.
* **Methodology:** A two-step, "coarse-to-fine" strategy is employed to manage the large dataset and computational resources effectively.
    1.  **Coarse Semantic Filtering:** First, a lightweight, multilingual sentence-embedding model (`paraphrase-multilingual-MiniLM-L12-v2`) is used to perform a high-speed semantic search. This step intelligently filters the full corpus, retaining only the articles that are conceptually related to the 167 risk factors, significantly reducing the volume of data for the next stage.
    2.  **Fine-Grained Zero-Shot Classification:** The smaller, relevant subset of articles is then processed by a powerful, state-of-the-art zero-shot classification model, **`MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`**. This model performs a deeper semantic analysis to identify and classify specific risk factor mentions within sentences with high accuracy. The final results are unified under a common English schema for consistent downstream analysis.
* **Input:** Cleaned sentence-level text from Stage 1.
* **Output:** A structured dataset of detected events, mapping each `article_id` to the specific unified `risk_factor` it contains.

### **Stage 3: Geospatial Intelligence and Hotspot Identification**

* **Objective:** To link the textual risk signals to specific geographic districts and identify statistically significant spatial clusters.
* **Methodology:** A two-step geoparsing pipeline will be developed using a multilingual Named Entity Recognition (NER) model to extract location names and then resolve them against the provided geographic taxonomies. A **spatial clustering statistic (e.g., Getis-Ord Gi\*)** will then be applied to identify **hotspots**.
* **Input:** The risk factor data from Stage 2 and the geographic taxonomy dictionaries.
* **Output:** A geolocated dataset of risk events and a list of identified hotspot districts.

### **Stage 4: Hierarchical Risk Index Construction**

* **Objective:** To aggregate the discrete, geolocated risk events into a continuous, quantitative time-series metric for monitoring.
* **Methodology:** A hierarchical index will be constructed by counting risk factor mentions per district per day, normalizing these counts by article volume, and then averaging them into thematic and composite risk indices.
* **Input:** The geolocated risk event data from Stage 3.
* **Output:** A time-series DataFrame with a Composite Risk Index (CRI) score for each district for each day.

### **Stage 5: Unsupervised Anomaly Detection**

* **Objective:** To automatically flag unusual spikes in risk, demonstrating the system's utility as an early warning tool.
* **Methodology:** An **unsupervised, time-series anomaly detection model** will be applied to the Composite Risk Index for each district to identify statistically significant deviations from a learned baseline.
* **Input:** The time-series data from Stage 4.
* **Output:** A list of alerts, each containing a date, a district, and the reason for the alert.

---

## 3. Conceptual Data Flow

The refined conceptual flow of the entire system, incorporating the efficiency-focused filtering stage, can be visualized as follows:

`[Raw News]` -> `[Stage 1: EDA & Preprocessing]` -> **`[Stage 2a: Semantic Filtering (Coarse)]`** -> **`[Stage 2b: Zero-Shot Classification (Fine)]`** -> `[Stage 3: Geospatial Linking]` -> `[Stage 4: Risk Indexing]` -> `[Stage 5: Anomaly Detection]` -> `[Early Warning Alerts]`