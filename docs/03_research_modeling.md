# **Research Modeling: A Conceptual Framework**

## 1. Objective and Guiding Principles

The primary objective of this research model is to outline the conceptual architecture for an end-to-end pipeline that transforms raw, multilingual news articles into a dynamic, geographically-aware early warning system for food insecurity. This framework directly addresses the core challenges of the assessment—namely, the absence of ground-truth data, the use of multilingual text, and the need to demonstrate the value of geospatial analysis—by drawing upon established academic research.

Our approach is guided by two core principles derived from the literature:

1.  **Lexicon-Based Feature Extraction:** Following the methodology of **Balashankar, A., et al. (2023)**, the model's foundation will be the systematic extraction of the 167 predefined risk factors from news text to create high-frequency, quantitative "news indicators."
2.  **Unsupervised Anomaly Detection:** In line with the work of **van Wanrooij, C., et al. (2024)**, and to address the lack of ground-truth data, the final output will be an unsupervised system. The goal is not to predict a specific food insecurity phase, but to detect statistically significant anomalies in the volume and clustering of risk reporting, thereby flagging potential crises for further investigation.

---

## 2. The Six-Stage Conceptual Model

The proposed system is structured as a **six-stage conceptual model**, where the output of each stage serves as the input for the next. This modular design, which directly mirrors the project's notebook structure, ensures clarity, interpretability, and a logical progression from raw data to actionable intelligence.

### **Stage 1: Exploratory Data Analysis (EDA) and Preprocessing**

* **Objective**: To understand the fundamental statistical, temporal, and geospatial properties of the datasets and to clean the text for subsequent analysis.
* **Methodology**: This foundational stage involves inspecting all data sources to identify patterns and quality issues. Text data is cleaned by removing irrelevant characters and segmented into sentences to prepare it for NLP models.
* **Input**: All raw data files (`.csv`, `.xlsx`, `.pkl`).
* **Output**: Clean, preprocessed, sentence-tokenized text data.

### **Stage 2: Feature Engineering (Semantic Filtering)**

* **Objective:** To efficiently reduce the dataset to a smaller, high-relevance subset of articles, enabling the use of more powerful models in the next stage.
* **Methodology:** This "coarse" filtering stage uses a lightweight, multilingual sentence-embedding model (`paraphrase-multilingual-MiniLM-L12-v2`) to perform a high-speed semantic search. This step intelligently filters the full corpus, retaining only articles that are conceptually related to the 167 risk factors.
* **Input:** Cleaned sentence-level text from Stage 1.
* **Output:** A filtered, smaller subset of the most relevant articles.

### **Stage 3: Multilingual Risk Factor Extraction**

* **Objective:** To accurately identify mentions of the 167 English risk factors within the filtered English and Arabic news corpora.
* **Methodology:** This "fine-grained" stage processes the filtered articles with a powerful, state-of-the-art zero-shot classification model, **`MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`**. This model performs a deep semantic analysis to identify and classify specific risk factor mentions within sentences with high accuracy.
* **Input:** The filtered article data from Stage 2.
* **Output:** A structured dataset of detected events, mapping each `article_id` to the specific `risk_factor` it contains.

### **Stage 4: Geospatial Intelligence and Hotspot Identification**

* **Objective:** To link the textual risk signals to specific geographic districts and identify statistically significant spatial clusters.
* **Methodology:** A multilingual Named Entity Recognition (NER) model extracts location names, which are then resolved against geographic taxonomies. A **spatial clustering statistic (e.g., Getis-Ord Gi\*)** is then applied to identify **hotspots**.
* **Input:** The risk factor data from Stage 3 and the geographic taxonomy dictionaries.
* **Output:** A geolocated dataset of risk events and a list of identified hotspot districts.

### **Stage 5: Hierarchical Risk Index Construction**

* **Objective:** To aggregate the discrete, geolocated risk events into a continuous, quantitative time-series metric for monitoring.
* **Methodology:** A hierarchical index is constructed by counting risk factor mentions per district per day, normalizing these counts by article volume, and then averaging them into thematic and composite risk indices.
* **Input:** The geolocated risk event data from Stage 4.
* **Output:** A time-series DataFrame with a Composite Risk Index (CRI) score for each district for each day.

### **Stage 6: Unsupervised Anomaly Detection**

* **Objective:** To automatically flag unusual spikes in risk, demonstrating the system's utility as an early warning tool.
* **Methodology:** An **unsupervised, time-series anomaly detection model** is applied to the Composite Risk Index for each district to identify statistically significant deviations from a learned baseline.
* **Input:** The time-series data from Stage 5.
* **Output:** A list of alerts, each containing a date, a district, and the reason for the alert.

---

## 3. Conceptual Data Flow

The refined conceptual flow of the entire system, now reflecting a six-stage process, can be visualized as follows:

`[Raw News]` -> `[Stage 1: Preprocessing]` -> `[Stage 2: Semantic Filtering]` -> `[Stage 3: Risk Extraction]` -> `[Stage 4: Geospatial Linking]` -> `[Stage 5: Risk Indexing]` -> `[Stage 6: Anomaly Detection]` -> `[Early Warning Alerts]`