# **Research Design: An Implementation Plan**

## Introduction and Objective

This document operationalizes the **Research Model** by providing a detailed implementation plan for the Food Crisis Early Warning System. The objective is to translate the five-stage conceptual framework into a concrete set of data processing steps, model selections, and analytical techniques. This research design will serve as the direct blueprint for the accompanying Jupyter Notebooks, ensuring that the implementation is rigorous, reproducible, and directly aligned with the project's goals.

## Stage 1: Exploratory Data Analysis (EDA) and Data Preprocessing

Before implementing the main modeling pipeline, a foundational phase of EDA and preprocessing is required to understand and clean the data.

* **Exploratory Data Analysis:**
    * **Data Loading:** All provided data files—`news-articles-eng.csv`, `news-articles-ara.csv`, `risk-factors.xlsx`, `risk-factors-categories.xlsx`, and the two `.pkl` geographic taxonomies—will be loaded into `pandas` DataFrames and Python dictionaries.
    * **Initial Inspection:** The structure, data types, and presence of missing values in each dataset will be examined using `.info()` and `.isnull().sum()`. The date range of the news articles will be confirmed.
    * **Visualizations:** Key distributions will be visualized to understand the data's characteristics, including time-series plots of article volume, bar charts of articles per country, and word clouds to identify prominent terms.
    * **Advanced Analysis**: Deeper statistical, geospatial, and temporal analysis will be conducted, including risk factor frequency analysis, n-gram analysis, sentiment distribution plots, choropleth maps of data density, risk cluster correlation heatmaps, and autocorrelation tests on key time series.

* **Data Preprocessing:**
    * **News Article Cleaning:** A standardized text preprocessing function will be developed and applied to the `body` column of both news DataFrames. This function will remove HTML tags, normalize whitespace, and remove URLs/special characters.
    * **Sentence Tokenization:** The cleaned text of each article will be segmented into a list of sentences using a **custom regular expression**. This dependency-free approach ensures consistent tokenization across different environments by splitting the text based on common sentence-ending punctuation.

---

## Implementation of the Pipeline

### **Stage 2: Multilingual Risk Factor Extraction (Coarse-to-Fine)**

This stage is implemented as a two-step, "coarse-to-fine" process to ensure both efficiency and accuracy.

* **Step 2a: Coarse Semantic Filtering**
    * **Objective:** To intelligently reduce the dataset size by pre-selecting only the most relevant articles for the computationally intensive classification stage.
    * **Model:** A lightweight, multilingual sentence-embedding model, `paraphrase-multilingual-MiniLM-L12-v2`.
    * **Library:** The implementation will be handled using the `SentenceTransformer` library.
    * **Execution:** The 167 English risk factors and all article bodies will be encoded into a shared multilingual vector space. A high-speed `util.semantic_search` will identify articles with a semantic similarity score above a threshold of **0.25**, creating a smaller, high-relevance corpus.

* **Step 2b: Fine-Grained Zero-Shot Classification**
    * **Objective:** To perform a deep analysis on the filtered articles to extract specific risk factor mentions.
    * **Model Selection:** We will use the **`MoritzLaurer/mDeBERTa-v3-base-mnli-xnli`** model, a state-of-the-art multilingual model highly optimized for zero-shot classification tasks.
    * **Library:** The implementation will be handled through the `pipeline` function from the Hugging Face `transformers` library.
    * **Execution:** All sentences from the filtered articles will be processed in batches for efficiency. The pipeline will be run with the 167 risk factors as `candidate_labels`, and a confidence threshold of **0.80** will be required to classify a sentence as containing a risk mention.

* **Output:**
    * A `pandas` DataFrame with columns: `article_id`, `date`, `sentence_text`, `risk_factor`, and `confidence_score`.

### **Stage 3: Geospatial Intelligence and Hotspot Identification**

* **Geotagging Implementation:**
    * **Model:** `spaCy`'s pre-trained multilingual model, `xx_ent_wiki_sm`, will be used for Geographic Named Entity Recognition (GNER).
    * **Execution:** A function will process each article's text, extract all `GPE` entities, and match them against the location dictionaries to resolve each mention to a specific district key.

* **Hotspot Analysis Implementation:**
    * **Libraries:** The `geopandas` and `esda` libraries will be used.
    * **Execution:** Geolocated risk factor counts will be aggregated by district. The Getis-Ord Gi\* statistic will be calculated for primary thematic clusters to identify and visualize statistically significant hotspots.

### **Stage 4: Hierarchical Risk Index Construction**

* **Implementation:** All calculations will be performed using `pandas`.
    * **Aggregation:** The data will be grouped by `date` and `district_key` to produce daily raw counts for each of the 167 risk factors.
    * **Normalization:** Raw counts will be divided by the total article count per district per day to control for variations in news coverage.
    * **Index Calculation:** The normalized data will be merged with category mappings. A `groupby` operation will be used to calculate the mean score for each thematic index, followed by a final `groupby` to produce the Composite Risk Index (CRI).

### **Stage 5: Unsupervised Anomaly Detection**

* **Algorithm Selection:** A **moving average-based z-score** method will be implemented. This statistical approach is robust and well-suited for identifying significant deviations from a short-term baseline.
* **Implementation:**
    * A 7-day rolling window will be used to calculate the moving average and standard deviation for each district's CRI time series.
    * The z-score for each day will be calculated.
    * An anomaly will be flagged if the **z-score is greater than 2.0**.
* **Qualitative Validation:**
    * A function will be created to retrieve and display the headlines of news articles that contributed to any flagged anomaly, providing a direct narrative link between the quantitative alert and on-the-ground reporting.

---

## Technical Stack Summary

The implementation will rely on the following core Python libraries:

* **Data Manipulation:** `pandas`, `numpy`, `openpyxl`
* **Natural Language Processing:** `transformers` (Hugging Face), `sentence-transformers`, `spacy`, `regex`
* **Geospatial Analysis:** `geopandas`, `esda`
* **Machine Learning Infrastructure:** `torch`, `faiss-cpu`
* **Data Serialization:** `pickle`
* **Visualization:** `matplotlib`, `seaborn`, `wordcloud`