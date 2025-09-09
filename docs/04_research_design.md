# Research Design: An Implementation Plan

## 1. Introduction and Objective

This document operationalizes the **Research Model** by providing a detailed implementation plan for the Food Crisis Early Warning System. The objective is to translate the four-stage conceptual framework into a concrete set of data processing steps, model selections, and analytical techniques. This research design will serve as the direct blueprint for the accompanying Jupyter Notebook, ensuring that the implementation is rigorous, reproducible, and directly aligned with the project's goals.

## 2. Stage 0: Exploratory Data Analysis (EDA) and Data Preprocessing

Before implementing the main modeling pipeline, a foundational phase of EDA and preprocessing is required to understand and clean the data.

* **Exploratory Data Analysis:**
    * **Data Loading:** All provided data files—`news-articles-eng.csv`, `news-articles-ara.csv`, `risk-factors.xlsx`, `risk-factors-categories.xlsx`, and the two `.pkl` geographic taxonomies—will be loaded into `pandas` DataFrames and Python dictionaries.
    * **Initial Inspection:** The structure, data types, and presence of missing values in each dataset will be examined using `.info()` and `.isnull().sum()`. The date range of the news articles will be confirmed.
    * **Visualizations:** Key distributions will be visualized to understand the data's characteristics:
        * A time-series plot will show the volume of English vs. Arabic articles published per day.
        * A bar chart will display the total number of articles associated with each country in the Mashriq region.
        * Word clouds will be generated from the `title` and `body` of a sample of articles in each language to identify prominent terms.

* **Data Preprocessing:**
    * **News Article Cleaning:** A standardized text preprocessing function will be developed and applied to the `body` column of both news DataFrames. This function will:
        1.  Remove residual HTML tags using regular expressions.
        2.  Normalize whitespace (e.g., remove extra spaces, tabs, and newlines).
        3.  Remove URLs and special characters not relevant to the text's meaning.
    * **Sentence Tokenization:** The cleaned text of each article will be segmented into a list of sentences using the `nltk.sent_tokenize` library. This is a critical step, as the subsequent NLP model operates most effectively on individual sentences.

## 3. Implementation of the Four-Stage Pipeline

### Stage 1: Multilingual Risk Factor Extraction

* **Model Selection and Implementation:**
    * **Model:** We will use the `facebook/xlm-roberta-base` model, a powerful multilingual transformer proven to have strong performance on zero-shot classification tasks.
    * **Library:** The implementation will be handled through the `pipeline` function from the Hugging Face `transformers` library, specifically using the `zero-shot-classification` task.
    * **Execution:** For each sentence in the corpus, the pipeline will be run with the 167 English risk factors serving as the `candidate_labels`. To optimize performance on the large dataset, this process will be batched. A confidence threshold of **0.80** will be set; if the model returns a score above this threshold for a given risk factor, the sentence will be classified as containing a mention of that risk.

* **Output:**
    * The final output of this stage will be a `pandas` DataFrame with the following columns: `article_id`, `date`, `sentence_text`, `risk_factor`, and `confidence_score`.

### Stage 2: Geospatial Intelligence and Hotspot Identification

* **Geotagging Implementation:**
    * **Model:** `spaCy`'s pre-trained multilingual model, `xx_ent_wiki_sm`, will be used for Geographic Named Entity Recognition (GNER). This model is efficient and capable of identifying geopolitical entities (`GPE`) in both English and Arabic text.
    * **Execution:** A function will be developed to process each article's text. It will apply the `spaCy` model to extract all `GPE` entities. A simple but effective string-matching algorithm will then iterate through the extracted entities and compare them against the loaded location dictionaries to resolve each mention to a specific district key. If an article mentions multiple districts, it will be associated with all of them.

* **Hotspot Analysis Implementation:**
    * **Libraries:** The `geopandas` and `esda` (from the PySAL ecosystem) libraries will be used.
    * **Execution:** The geolocated risk factor counts will first be aggregated by district. A GeoDataFrame will then be constructed (requiring a public domain shapefile for the Mashriq region, for example from GADM). The Getis-Ord Gi* statistic will be calculated for the primary thematic clusters (e.g., "conflicts and violence," "economic issues"). The output will include a z-score and p-value for each district, allowing for the identification and visualization of statistically significant hotspots (high z-score, low p-value). 

### Stage 3: Hierarchical Risk Index Construction

* **Implementation:** All calculations in this stage will be performed using `pandas`.
    * **Aggregation:** The geolocated risk event data from Stage 2 will be grouped by `date` and `district_key` to produce daily raw counts for each of the 167 risk factors.
    * **Normalization:** A separate calculation will count the total number of articles geolocated to each district for each day. The raw risk factor counts will be divided by this total article count to produce a normalized frequency, controlling for variations in news coverage.
    * **Index Calculation:** The `risk-factors-categories.xlsx` file will be loaded. The normalized frequency data will be merged with the category mapping. A `groupby` operation on `date`, `district_key`, and `cluster` will be used to calculate the mean score for each thematic index. A final `groupby` on `date` and `district_key` will calculate the mean of the thematic scores to produce the final Composite Risk Index (CRI).

### Stage 4: Unsupervised Anomaly Detection

* **Algorithm Selection:** Given the short one-month time series, a complex forecasting model is not appropriate. A **moving average-based z-score** method will be implemented. This statistical approach is robust, highly interpretable, and well-suited for identifying significant deviations from a short-term baseline without requiring a training period.

* **Implementation:**
    * For each district's CRI time series, a 7-day rolling window will be used to calculate the moving average and moving standard deviation.
    * The z-score for each day will be calculated as: `(Actual CRI - Moving Average) / Moving Standard Deviation`.
    * An anomaly will be flagged if the **z-score is greater than 2.0**, indicating that the risk level on a given day is more than two standard deviations above the recent weekly norm.

* **Qualitative Validation:**
    * A function will be created to support the validation of any detected anomaly. When an anomaly is flagged for a specific district and date, this function will retrieve and display the headlines of the news articles that were geolocated to that district on that day and contained mentions of the risk factors that contributed most to the spike. This provides a direct, narrative link between the quantitative alert and the on-the-ground reporting.

## 4. Technical Stack Summary

The implementation will rely on the following core Python libraries:

* **Data Manipulation:** `pandas`, `numpy`, `openpyxl`
* **Natural Language Processing:** `transformers` (Hugging Face), `spacy`, `nltk`, `regex`
* **Geospatial Analysis:** `geopandas`, `esda`
* **Data Serialization:** `pickle`
* **Visualization:** `matplotlib`, `seaborn`, `wordcloud`