## **Research Modeling: A Conceptual Framework (Updated)**

### 1. Objective and Guiding Principles

The primary objective of this research model is to outline the conceptual architecture for an end-to-end pipeline that transforms raw, multilingual news articles into a dynamic, geographically-aware early warning system for food insecurity. This framework directly addresses the core challenges of the assessment—namely, the absence of ground-truth data, the use of multilingual text, and the need to demonstrate the value of geospatial analysis—by drawing upon established academic research.

Our approach is guided by two core principles derived from the literature:

1.  **Lexicon-Based Feature Extraction:** Following the methodology of **Balashankar, A., et al. (2023)**, the model's foundation will be the systematic extraction of the 167 predefined risk factors from news text to create high-frequency, quantitative "news indicators."
2.  **Unsupervised Anomaly Detection:** In line with the work of **van Wanrooij, C., et al. (2024)**, and to address the lack of ground-truth data, the final output will be an unsupervised system. The goal is not to predict a specific food insecurity phase, but to detect statistically significant anomalies in the volume and clustering of risk reporting, thereby flagging potential crises for further investigation.

---

### 2. The Five-Stage Conceptual Model

The proposed system is structured as a **five-stage conceptual model**, where the output of each stage serves as the input for the next. This modular design, which directly mirrors the project's notebook structure, ensures clarity, interpretability, and a logical progression from raw data to actionable intelligence.

#### **Stage 1: Exploratory Data Analysis (EDA) and Preprocessing**

* **Objective**: To understand the fundamental statistical, temporal, and geospatial properties of the datasets and to clean the text for subsequent analysis.
* **Methodology**: This foundational stage involves inspecting all data sources to identify patterns and quality issues. Text data is cleaned by removing irrelevant characters and segmented into sentences to prepare it for NLP models.
* **Input**: All raw data files (`.csv`, `.xlsx`, `.pkl`).
* **Output**: Clean, preprocessed, sentence-tokenized text data for the entire article corpus.

#### **Stage 2: Two-Stage Risk Factor Extraction**

* **Objective**: To efficiently and accurately identify specific risk factor mentions within the entire corpus using a two-stage, coarse-to-fine filtering process at the sentence level.
* **Methodology**: This stage performs a deep analysis on **all sentences** from the preprocessed corpus.
    1.  **Coarse Filtering (Semantic Pre-selection):** A lightweight model (`paraphrase-multilingual-MiniLM-L12-v2`) performs a high-speed semantic search, pre-selecting a smaller subset of sentences that are conceptually related to the risk factors.
    2.  **Fine Classification (Deep Analysis):** This smaller, relevant subset of sentences is then processed by a powerful, cross-lingual zero-shot classification model (`MoritzLaurer/deberta-v3-xsmall-zeroshot-v1.1-all-33`), which identifies and classifies specific risk factor mentions with high accuracy.
* **Input**: The complete, sentence-tokenized text data from Stage 1.
* **Output**: A structured dataset of high-confidence, unique risk events, mapping each `article_id` to the specific `risk_factor` it contains.

#### **Stage 3: Geospatial Intelligence and Hotspot Identification**

* **Objective**: To link the textual risk signals to specific geographic districts and identify statistically significant spatial clusters.
* **Methodology**: A multilingual Named Entity Recognition (NER) model extracts location names, which are then resolved against geographic taxonomies. A **spatial clustering statistic (e.g., Getis-Ord Gi\*)** is then applied to identify **hotspots**.
* **Input**: The risk factor data from Stage 2 and the geographic taxonomy dictionaries.
* **Output**: A geolocated dataset of risk events and a list of identified hotspot districts.

#### **Stage 4: Hierarchical Risk Index Construction**

* **Objective**: To aggregate the discrete, geolocated risk events into a continuous, quantitative time-series metric for monitoring.
* **Methodology**: A hierarchical index is constructed by counting risk factor mentions per district per day, normalizing these counts by article volume, and then averaging them into thematic and composite risk indices.
* **Input**: The geolocated risk event data from Stage 3.
* **Output**: A time-series DataFrame with a Composite Risk Index (CRI) score for each district for each day.

#### **Stage 5: Unsupervised Anomaly Detection**

* **Objective**: To automatically flag unusual spikes in risk, demonstrating the system's utility as an early warning tool.
* **Methodology**: An **unsupervised, time-series anomaly detection model** is applied to the Composite Risk Index for each district to identify statistically significant deviations from a learned baseline.
* **Input**: The time-series data from Stage 4.
* **Output**: A list of alerts, each containing a date, a district, and the reason for the alert.

---

### 3. Conceptual Data Flow

The refined conceptual flow of the entire system, now reflecting a more robust five-stage process, can be visualized as follows:

`[Raw News]` -> `[Stage 1: Preprocessing]` -> `[Stage 2: Risk Extraction]` -> `[Stage 3: Geospatial Linking]` -> `[Stage 4: Risk Indexing]` -> `[Stage 5: Anomaly Detection]` -> `[Early Warning Alerts]`