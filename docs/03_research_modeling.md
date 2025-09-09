# Research Modeling: A Conceptual Framework

## 1. Objective and Guiding Principles

The primary objective of this research model is to outline the conceptual architecture for an end-to-end pipeline that transforms raw, multilingual news articles into a dynamic, geographically-aware early warning system for food insecurity. This framework directly addresses the core challenges of the assessment—namely, the absence of ground-truth data, the use of multilingual text, and the need to demonstrate the value of geospatial analysis—by drawing upon established academic research.

Our approach is guided by two core principles derived from the literature:

1.  **Lexicon-Based Feature Extraction:** Following the methodology of **Balashankar, A., et al. (2023)**, the model's foundation will be the systematic extraction of the 167 predefined risk factors from news text to create high-frequency, quantitative "news indicators."

2.  **Unsupervised Anomaly Detection:** In line with the work of **van Wanrooij, C., et al. (2024)**, and to address the lack of ground-truth data, the final output will be an unsupervised system. The goal is not to predict a specific food insecurity phase, but to detect statistically significant anomalies in the volume and clustering of risk reporting, thereby flagging potential crises for further investigation.

## 2. The Four-Stage Conceptual Model

The proposed system is structured as a four-stage conceptual model, where the output of each stage serves as the input for the next. This modular design ensures clarity, interpretability, and a logical progression from raw data to actionable intelligence.

### Stage 1: Multilingual Risk Factor Extraction

* **Objective:** To accurately identify mentions of the 167 English risk factors within both the English and Arabic news corpora.
* **Challenge:** The `risk_factor_arabic` column is empty, precluding simple keyword matching for the Arabic text.
* **Methodology:** A **Zero-Shot Classification** model using a pre-trained multilingual transformer (e.g., XLM-RoBERTa) will be implemented. This state-of-the-art approach avoids the pitfalls of machine translation by identifying the semantic *concept* of a risk factor across languages. The task will be framed as Natural Language Inference (NLI), where each risk factor is converted into a hypothesis (e.g., "This text is about a price rise"), and the model evaluates whether sentences in any language entail that hypothesis.
* **Input:** Raw text from `news-articles-eng.csv` and `news-articles-ara.csv`.
* **Output:** A structured dataset of detected events, mapping each `article_id` to the specific `risk_factor_english` it contains.

### Stage 2: Geospatial Intelligence and Hotspot Identification

* **Objective:** To link the textual risk signals to specific geographic districts and identify statistically significant spatial clusters.
* **Challenge:** To demonstrate the added value of the provided geographic taxonomy.
* **Methodology:** A two-step geoparsing pipeline will be developed:
    1.  **Geographic Named Entity Recognition (GNER):** A multilingual NER model will extract location names (toponyms) from each news article.
    2.  **Toponym Resolution:** The extracted toponyms will be matched against the provided `id_english_location_name.pkl` and `id_arabic_location_name.pkl` dictionaries to link each risk mention to a precise district key (e.g., `iq_bg_1`).
* **Analysis:** With risks geolocated, a **spatial clustering statistic (e.g., Getis-Ord Gi\*)** will be applied. This spatial analysis technique will identify **hotspots**—districts where the concentration of risk-related news is significantly higher than expected—thereby providing actionable, localized intelligence.
* **Input:** The risk factor data from Stage 1 and the geographic taxonomy dictionaries.
* **Output:** A geolocated dataset of risk events and a list of identified hotspot districts for specific risk themes.

### Stage 3: Hierarchical Risk Index Construction

* **Objective:** To aggregate the discrete, geolocated risk events into a continuous, quantitative time-series metric for monitoring.
* **Challenge:** To create a single, interpretable score that reflects the overall risk level while retaining thematic detail.
* **Methodology:** A hierarchical index will be constructed:
    1.  **Aggregation & Normalization:** The number of mentions for each of the 167 risk factors will be counted per district, per day. These counts will be normalized by the total article volume for that district to control for reporting bias.
    2.  **Thematic Indices:** Using the `risk-factors-categories.xlsx` mapping, the normalized indicators will be averaged into their thematic clusters (e.g., "economic issues," "conflicts and violence").
    3.  **Composite Risk Index:** The thematic scores will be averaged into a single Composite Risk Index (CRI) for each district, for each day. Equal weighting will be used for transparency and robustness, a best practice in data-scarce environments.
* **Input:** The geolocated risk event data from Stage 2.
* **Output:** A time-series DataFrame where the index is the date, columns are district keys, and values are the Composite Risk Index scores.

### Stage 4: Unsupervised Anomaly Detection

* **Objective:** To demonstrate the system's utility as an early warning tool by automatically flagging unusual spikes in risk, thereby addressing the "no ground truth" challenge.
* **Challenge:** To validate the system's output without labeled data.
* **Methodology:** An **unsupervised, time-series anomaly detection model** will be applied to the Composite Risk Index for each district. The model will learn the baseline or 'normal' pattern of risk reporting for each location from the available data. It will then identify any data points that represent a statistically significant deviation from this established baseline. Such deviations will be flagged as **anomalies**, representing a potential surge in food insecurity risk.
* **Validation:** These flagged anomalies serve as potential early warnings. Their validity can be demonstrated qualitatively by retrieving the specific news articles from that day and district that contributed to the spike, creating a narrative that connects the quantitative alert back to real-world events.
* **Input:** The time-series data from Stage 3.
* **Output:** A list of alerts, each containing a date, a district key, and the reason for the alert (e.g., "Spike in Economic Issues cluster").

## 3. Conceptual Data Flow

The conceptual flow of the entire system can be visualized as follows:

`[Raw News (ENG/ARA)]` -> `[Stage 1: NLP Risk Extraction]` -> `[Stage 2: Geospatial Linking]` -> `[Stage 3: Risk Indexing]` -> `[Stage 4: Anomaly Detection]` -> `[Early Warning Alerts]`

## 4. Transition to Research Design

This document outlines the conceptual model. The subsequent **Research Design** phase will detail the specific implementation choices, including:

* Selection of specific libraries and pre-trained models.
* Detailed data preprocessing steps.
* Hyperparameter tuning and algorithm selection for anomaly detection.
* Quantitative metrics for evaluating model components where applicable.

This framework provides a clear, robust, and research-grounded path to developing a novel Food Crisis Early Warning System that meets all the requirements of the assessment.