# **Food Crisis Early Warning System: Project Requirements**

This document outlines the requirements for the "Food Crisis Early Warning" take-home assessment. The goal is to build a system that can help understand and predict food insecurity using news articles and risk factors.

---

## **High-Level Goal**

The main objective is to address the challenge of understanding and potentially predicting food insecurity in data-scarce environments. This is an open-ended problem, so the focus is on your approach and problem-solving skills, not just a single "correct" solution. Your submission's depth and originality will be key factors for selection.

---

## **Deliverables**

You need to submit two items:

1.  **Jupyter Notebook**:
    * This should contain all your code, with clear documentation and comments explaining your design choices.
    * The notebook must be runnable on its own, with all cells executed and outputs saved.
    * Name your file in the format: `solution_{FIRSTNAME}_{LASTNAME}.ipynb`.
    * If you use any additional open-access data, either include it in your submission (e.g., in a ZIP file) or provide clear instructions on how to load it.

2.  **Markdown Report**:
    * A separate report detailing your approach to the research problem.
    * This should explain the models, methods, and any extra data you used.

---

## **Key Challenges to Address**

Your project should explore and address the following questions and challenges:

* **Geospatial Data**: What is the added value of using geospatial data in this context?
* **Lack of Ground Truth**: How can you work around the absence of ground-truth information on food insecurity levels?
* **Multilingual Data**: What are the benefits and challenges of working with both English and Arabic data?

---

## **Provided Data**

You will be working with four main data sources:

1.  **Risk Factors**:
    * A file with 167 risk factors (unigrams, bigrams, and trigrams) in English, with an empty column for Arabic keywords.
    * A separate file that maps these English risk factors to predefined thematic clusters.

2.  **News Articles**:
    * Two CSV files containing one month of news articles from the Mashriq region:
        * `news-articles-eng.csv` (English)
        * `news-articles-ara.csv` (Arabic)
    * You are allowed to work with a smaller sample of the data during development.

3.  **Geographic Taxonomy**:
    * A file containing the names of countries, provinces, and districts for the Mashriq countries covered in the news articles.
    * This data is provided as a dictionary, mapping a key to a geographic name. The keys have a specific structure:
        * **Countries**: A two-character abbreviation (e.g., 'iq' for 'Iraq').
        * **Provinces**: `{country_abbreviation}_{province_abbreviation}` (e.g., 'iq_bg' for Baghdad).
        * **Districts**: `{country_abbreviation}_{province_abbreviation}_{unique_number}` (e.g., 'iq_bg_1').

---
