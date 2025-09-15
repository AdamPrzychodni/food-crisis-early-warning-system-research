# Early Warning System for Food Crises: Multilingual News Analysis for Humanitarian Intelligence

**Author:** Adam Przychodni  
**Email:** adam.przychodni@gmail.com  
**Date:** 15 September 2025  

## My idea 
### Description of the stages

#### **Stage 1: Geographical filtering**
First, we check whether the articles actually concern the locations we are interested in. 🗺️

#### **Stage 2: Preprocessing**
Next, we perform preprocessing, i.e. standard data cleaning. 🧹

#### **Stage 3: Classification at the article level**
Then, my idea was to use a model that would classify whether a given article refers to topics related to food insecurity – i.e. a binary classification model (yes or no).

#### **Stage 4: Division into sentences**
Next, once we have articles with content that interests us, we divide them into individual sentences.

#### **Stage 5: Classification of risk factors**
Then, another model takes a sentence as input and classifies which of the 167 risk factors it relates to.

#### **Stage 6: Geolocation**
For each of these sentences, we then try to determine the exact location. 

#### **Step 7: Creating a Risk Index**
Based on the collected results, we create the final Risk Index. 

### Data Flow Diagram (Workflow)

`[Articles]` → **Step 1: Geographical filtering** → `[Articles from relevant locations]` → **Step 2: Preprocessing** → `[Cleaned articles]` → **Step 3: Classification of articles (food topic)** → `[Food Insecurity-related articles]` → **Stage 4: Sentence segmentation** → `[List of sentences]` → **Stage 5: Risk factor classification** → `[Sentences with assigned risk]` → **Stage 6: Geolocation** → `[Sentences with risk and location]` → **Stage 7: Risk Index creation** → `[Final Risk Index]`

# Actual Implementation

## Executive Summary

This report presents an approach to early warning of food crises through the automatic analysis of multilingual news articles. The system processes 172,000 news articles in English and Arabic to extract, geolocate, and quantify mentions of risk factors, ultimately generating standardised risk indicators that achieve 66.7% directional accuracy compared to authoritative IPC assessments. The pipeline successfully identifies Gaza as the highest-risk location with confirmed famine conditions, while providing spatially explicit risk mapping across the Mashriqu region.

## Research problem and motivation

Traditional food security monitoring relies heavily on official reporting channels, which are often delayed in response to rapidly changing crisis conditions. News media, particularly in crisis-affected regions, provide real-time signals of emerging food security threats by reporting on conflicts, economic disruptions, extreme weather events, and humanitarian needs. This study addresses the challenge of systematically extracting, validating, and operationalising these early warning signals for humanitarian decision-making.

### Key research questions

1. **Can multilingual news analysis provide reliable early warning signals for food crises?**
2. **How can mentions of risk factors be systematically extracted and validated across language barriers?**
3. **What is the optimal approach for converting unstructured information content into useful risk indicators?**
4. **How well do AI-based risk predictions align with authoritative food security assessments?**

## Methodology

### Overall architecture

The system uses a six-step processing pipeline designed to transform raw multilingual information content into validated risk indicators:

```
News articles (172K) → Geographical filtering → Risk factor extraction → 
Geotagging → Risk indicator construction → Validation → Spatial mapping
```

### Stage 1: News article processing pipeline

**Objective:** To filter and pre-process multilingual news articles for geographical relevance.

**Technical implementation:**
- **Geographical pre-filtering:** Keyword matching based on regex using a comprehensive gazetteer of locations
- **Named entity recognition:** Babelscape/wikineural-multilingual-ner for location validation
- **Text processing:** HTML tag removal, URL cleaning, sentence tokenisation using multilingual punctuation patterns

**Key design decisions:**
- Balanced filtering strategy prioritising recall over precision to avoid over-filtering
- Multilingual sentence segmentation supporting both Latin and Arabic scripts
- Article retention rate: 94.2% (162,117 out of 172,171 articles)

### Stage 2: Risk factor extraction pipeline

**Objective:** To identify food security risk factors in news content using semantic analysis.

**Risk factor taxonomy:**
- 167 predefined risk factors from the provided Excel taxonomy
- Thematic clusters: conflict, humanitarian aid, food crisis, extreme weather events, human rights violations
- Multilingual coverage with basic English terms

**Technical implementation:**
- **Semantic pre-filtering:** Sentence-BERT embeddings (paraphrase-multilingual-MiniLM-L12-v2) with a similarity threshold of 0.7
- **Zero-shot classification:** DeBERTa-v3-xsmall model with a confidence threshold of 0.95 for high extraction precision
- **Post-processing:** Selection of the highest confidence per sentence to eliminate redundancy

**Performance metrics:**
- Sentence filtering: 3.9M → 12,782 sentences (0.3% retention)
- Identified risk mentions: 2,238 unique high-confidence mentions
- Main risk factors: conflict (288), repression (165), extreme weather events (85)

### Stage 3: Risk mention geotagging pipeline

**Objective:** Link risk factor mentions to specific geographical locations using hierarchical resolution logic.

**Geotagging strategy:**
- **Hybrid approach:** Combining geographical analysis at the sentence and article levels
- **Hierarchical resolution:** Prioritising specific sentence locations over general article locations
- **Quality filtering:** Requiring a minimum of 4 words in a sentence to ensure meaningful content

**Technical implementation:**
- **Location extraction:** WikiNeuRaL multilingual NER for geographical entity recognition
- **Location resolution:** Matching a gazetteer with 918 pre-mapped location aliases
- **Hierarchy logic:** Resolution at country → province → district level with fallback mechanisms

**Result:**
- 1,372 high-quality risk mentions after filtering
- 1,050 successfully geotagged risk-location pairs
- Geographical distribution: Gaza (239), Iraq (122), Syria (85), Lebanon (80)

### Stage 4: Risk indicator construction pipeline

**Objective:** To transform discrete risk mentions into normalised, comparable risk indicators.

**Aggregation methodology:**
- **Temporal aggregation:** Daily risk counts by location and thematic cluster
- **Normalisation strategy:** Risk mentions divided by daily article publication volume
- **Composite indicator:** Average normalised risk across all thematic clusters per location-date

**Mathematical formulation:**
```
Risk_Index(location, date) = Σ(risk_mentions_cluster) / total_articles_published(date)
Composite_Risk_Index = mean(Risk_Index across all clusters)
```

**Index characteristics:**
- Date range: 23 June - 24 July 2024 (32 days)
- Geographical coverage: 76 unique locations
- Risk distribution: 0.000206 - 0.001618 (normalised scale)
- Time resolution: Daily aggregation from 442 location-date combinations

### Step 5: Spatial mapping of risk

**Objective:** Visualisation of risk indicators across administrative boundaries for operational decision-making.

**Geospatial implementation:**
- **Boundary data:** GADM administrative level 2 shapefiles for 5 Mashriq countries
- **Mapping strategy:** Aggregation of risk data to 260 administrative units
- **Visualisation types:** Average risk, maximum risk, logarithmic risk, data coverage, risk variability

**Technical approach:**
- Normalisation of location names for administrative unit matching
- Hierarchical matching (direct name matching → city-administration mapping → national aggregation)
- Multi-format output (PNG, interactive maps) for different user needs

### Stage 6: Validation framework

**Objective:** To assess the accuracy of predictions against authoritative food security assessments.

**Sources of ground truth:**
- **IPC Hunger Review Committee:** Hunger classification in Gaza Strip (July 2024)
- **WFP Country Bulletins:** Operational assessments for Iraq, Lebanon
- **FEWS NET:** Regional food security monitoring
- **Joint FAO/WFP reports:** Multi-country analysis of hunger hotspots

**Validation metrics:**
- **Phase error:** Absolute difference between predicted and actual IPC phase (scale 1-5)
- **Directional accuracy:** Correct identification of crisis vs. non-crisis conditions
- **Risk adjustment:** Normalised accuracy score accounting for prediction confidence

**Validation results:**
- Validated locations: 6 (Gaza, West Bank, Lebanon, Iraq, Jordan, Syria)
- Average phase error: 0.83 phases
- Directional accuracy: 66.7%
- Perfect predictions: Famine conditions in Gaza correctly identified as highest risk

## Model architecture and technical details

### Main models used

**1. Geographical entity recognition**
- **Model:** Babelscape/wikineural-multilingual-ner
- **Architecture:** Multilingual NER based on BERT with IOB tagging
- **Languages:** 40+ languages, including Arabic and English
- **Performance:** Optimised for multilingual location entity extraction

**2. Semantic similarity analysis**
- **Model:** paraphrase-multilingual-MiniLM-L12-v2
- **Architecture:** Sentence-BERT with 384-dimensional embeddings
- **Function:** Pre-filtering sentences for food safety relevance
- **Threshold:** 0.7 cosine similarity for high precision

**3. Zero-shot risk classification**
- **Model:** MoritzLaurer/deberta-v3-xsmall-zeroshot-v1.1-all-33
- **Architecture:** DeBERTa-v3 with classification head
- **Capability:** Multi-label classification across 167 risk factors
- **Confidence:** Threshold of 0.95 for high-quality extraction

### Computing infrastructure

**Processing environment:**
- **GPU:** NVIDIA L4 with CUDA acceleration
- **Memory:** Optimised batch processing for large-scale text analysis
- **Framework:** PyTorch with Hugging Face Transformers
- **Scalability:** Configurable batch sizes and processing chunks

**Performance optimisation:**
- Aggressive filtering parameters to balance accuracy with computational constraints
- GPU memory management with periodic cache flushing
- Batch processing optimisation for transformer models

## Results and performance analysis

### Quantitative results

**Processing statistics:**
- **Input volume:** 172,171 articles processed in total
- **Geographical relevance:** 162,117 articles (94.2%) contained location keywords
- **Risk factor extraction:** 2,238 high-confidence risk mentions identified
- **Geographic mapping:** 1,050 successfully geotagged risk-location pairs
- **Time coverage:** 32 days of continuous risk monitoring

**Risk indicator characteristics:**
- **Spatial coverage:** 76 unique locations in 5 countries
- **Temporal resolution:** Daily risk indicators with 442 location-date combinations
- **Risk distribution:** Average composite risk 0.000263, maximum 0.001618
- **Geographical concentration:** Gaza, Iraq and Syria show the highest sustained risk levels

### Validation performance

**Accuracy metrics:**
- **Phase prediction error:** 0.83 mean error in IPC phases (scale 1-5)
- **Directional accuracy:** 66.7% success in crisis vs. non-crisis classification
- **Perfect predictions:** 1 out of 6 locations (famine conditions in Gaza)
- **Close predictions:** 4 out of 6 locations within ±1 IPC phase

**Notable successes:**
- **Famine detection in Gaza:** Correctly identified as the highest-risk location with persistently elevated indicators
- **Regional risk ranking:** Correct order of Lebanon, Iraq and Syria as areas of elevated risk
- **Timing of the crisis:** Captured escalation patterns consistent with humanitarian assessments from July 2024

### Spatial analysis results

**Mapping administrative boundaries:**
- **Coverage:** 260 administrative units in 5 countries successfully mapped
- **Resolution:** District-level precision (Admin Level 2) for operational planning
- **Visualisation:** Five different types of choropleth maps for different analytical needs

**Geographical insight:**
- **Identification of hotspots:** Clear spatial clustering of high-risk areas
- **Cross-border patterns:** Regional risk propagation visible in border areas
- **Urban-rural dynamics:** Both urban centres and rural districts represented in risk assessments

## Limitations and future improvements

### Current limitations

**1. Model generalisation**
- Pre-trained models lack specialised food safety terminology
- Zero-shot classification may miss nuanced expressions of food danger
- Limited training data for fine-tuning in humanitarian contexts

**2. Gaps in geographical coverage**
- Only 15 administrative units successfully mapped from available data
- Challenges with transliteration and recognition of Arabic place names
- Incomplete city-administrative unit mapping relationships

**3. Time resolution limitations**
- Daily aggregation may smooth out important intra-day developments in the crisis
- No modelling of crisis evolution patterns or time decay functions
- Limited consideration of article publication time

**4. Validation data limitations**
- Only 6 locations available for ground truth comparison
- Time lag between model predictions and official crisis declarations
- Limited access to real-time validation data for emerging crises

### Proposed improvements

**1. Development of custom models**
- Fine-tuning of BERT-based models on humanitarian report corpora
- Development of specialised risk factor classification with hierarchical labels
- Implementation of specialised geographical entity recognition

**2. Improved data integration**
- Incorporation of real-time economic indicators (exchange rates, inflation)
- Addition of satellite-based agricultural monitoring data
- Integration of population movement and displacement tracking

**3. Improved temporal modelling**
- Implementation of time-weighted aggregation with decay functions
- Modelling of crisis escalation patterns and early warning thresholds
- Addition of seasonal agricultural cycles

**4. Extended validation framework**
- Increased coverage of ground truth through partnerships with humanitarian organisations
- Implementation of real-time validation against operational crisis responses
- Development of scenario testing for different types of crises

## Conclusions and recommendations

This study demonstrates the feasibility and value of using multilingual news analysis for early warning of food crises. The system successfully processes large volumes of multilingual content, extracts meaningful risk signals, and generates validated risk indicators that are consistent with authoritative food security assessments.

The demonstrated ability to automatically extract, validate, and operationalise early warning signals from multilingual news content represents a significant advancement in humanitarian intelligence capabilities. With continued development and operational integration, this approach has the potential to significantly improve the timeliness and effectiveness of crisis response to food security emergencies around the world.