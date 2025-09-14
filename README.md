# 🌍 Food Crisis Early Warning System

An AI-powered tool designed to help anticipate food insecurity by combining multilingual news analysis with geospatial risk modeling.

## 👀 What It Does

This system scans news articles in Arabic and English from across the Mashriq region, looking for signals related to food security. It then maps these signals to specific places, quantifies the level of risk, and produces clear risk indices that can support humanitarian decision-making.

In short: it helps turn scattered, unstructured information into actionable early warnings.

## ✨ Key Features

* **Multilingual Understanding**: Reads and processes both Arabic and English news articles using tailored NLP models.
* **Location-Aware**: Pinpoints risks down to administrative boundaries using smart geographic mapping.
* **Risk Scoring**: Transforms unstructured mentions into normalized, comparable risk indices.
* **Validation & Trust**: Benchmarks results against trusted sources like IPC, WFP, and FAO.
* **Clear Visuals**: Creates easy-to-read choropleth maps that highlight areas most at risk.

## 🔧 How It Works

### 1. News Article Processing

* Collects and filters multilingual articles
* Uses location gazetteers to narrow focus
* Cleans and tokenizes text for analysis

### 2. Risk Factor Extraction

* Filters for semantic similarity (threshold: 0.7)
* Applies zero-shot classification to detect food security risks
* Keeps only high-confidence results (threshold: 0.95)

### 3. Geographic Tagging

* Identifies location names with Named Entity Recognition (NER)
* Resolves references from sentence → article context
* Maps results to GADM administrative boundaries

### 4. Risk Index Construction

* Aggregates signals by location and theme on a daily basis
* Normalizes against publication volume
* Combines into a composite risk score

### 5. Validation & Mapping

* Validates against July 2024 ground truth (IPC phases)
* Produces choropleth maps for visual clarity
* Reports accuracy with confidence intervals

## 📂 Project Structure

```
├── data/
│   ├── 01_raw/              # Source data and shapefiles
│   ├── 02_processed/        # Cleaned articles
│   ├── 03_models/           # Risk mentions and geotagged data
│   ├── 04_feature/          # Risk indices (thematic & composite)
│   └── 05_reporting/        # Maps and validation outputs
├── notebooks/
│   └── 01_EDA.ipynb         # Exploratory Data Analysis 
│   └── solution_adam_przychodni.ipynb    # End-to-end pipeline
├── docs/                    # Technical documentation
└── requirements.txt         # Python dependencies
```

## 🚀 Getting Started

### Prerequisites

* Python 3.8+
* GPU (recommended for speed, CUDA-compatible)

### Installation

1. Clone the repository:

   ```bash
   git clone https://github.com/AdamPrzychodni/food-crisis-early-warning-system-research.git
   cd food-crisis-early-warning-system-research
   ```

2. Install dependencies:

   ```bash
   pip install -r requirements.txt
   ```

3. Verify installation:

   ```bash
   python -c "import torch; print(f'PyTorch: {torch.__version__}')"
   python -c "import transformers; print(f'Transformers: {transformers.__version__}')"
   ```

### Hardware Guidelines

* **Minimum**: 16GB RAM, CPU-only (slower)
* **Recommended**: 32GB RAM, NVIDIA GPU with 8GB+ VRAM
* **Storage**: \~10GB for data & models

## 🧩 Usage

All steps are available in `notebooks/solution_adam_przychodni.ipynb`:

1. Process multilingual news
2. Extract risk factors
3. Geotag articles
4. Build risk indices
5. Visualize with maps
6. Validate predictions

## ⚙️ Technical Stack

* **NLP**: DeBERTa (zero-shot), WikiNeuRaL (multilingual NER), Sentence Transformers
* **Geospatial**: GeoPandas, GADM boundaries
* **ML/Data**: Pandas, NumPy, scikit-learn
* **Visualization**: Matplotlib, Seaborn, choropleths

## 📊 Key Insights

The prototype system was able to:

* Flag Gaza as the highest-risk location (aligned with confirmed famine reports)
* Detect crisis-level conditions in Lebanon and Syria
* Achieve moderate success in predicting IPC phases
* Show promise as a humanitarian early warning tool

## 🚧 Limitations & Next Steps

Challenges include:

* Limited training data
* Gaps in geographic coverage
* Computational constraints

Future improvements:

* Domain-specific custom models
* Real-time data streams
* Stronger temporal forecasting

## 🎓 Context

This project was developed as part of a take-home assessment for AI research roles, showcasing expertise in multilingual NLP, geospatial modeling, and the humanitarian applications of AI.

---

👤 **Author**: Adam Przychodni
📧 **Email**: [adam.przychodni@gmail.com](mailto:adam.przychodni@gmail.com)
🌱 **Focus**: AI for social good & humanitarian impact