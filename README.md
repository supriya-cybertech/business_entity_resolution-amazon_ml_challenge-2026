# 🏆 Amazon ML Challenge 2026: Business Entity Resolution

![Python](https://img.shields.io/badge/Python-3.9%2B-blue?style=for-the-badge&logo=python&logoColor=white)
![LightGBM](https://img.shields.io/badge/LightGBM-Gradient_Boosting-ff69b4?style=for-the-badge&logo=lightgbm)
![Pandas](https://img.shields.io/badge/Pandas-Data_Processing-150458?style=for-the-badge&logo=pandas)
![Status](https://img.shields.io/badge/Status-Completed-success?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

**Team:** Mark-85 Matrix  
**Event:** Amazon ML Challenge 2026  

An end-to-end, highly scalable Machine Learning pipeline built to resolve and deduplicate extremely noisy business records across multiple independent data sources. Designed strictly for high precision, this solution heavily optimizes the Macro $F_{0.5}$ metric.

---

## 📑 Table of Contents
- [Executive Summary](#-executive-summary)
- [System Architecture](#-system-architecture)
- [Methodology & Pipeline](#-methodology--pipeline)
- [Repository Structure](#-repository-structure)
- [How to Reproduce](#-how-to-reproduce)
- [Results & Key Insights](#-results--key-insights)

---

## 🚀 Executive Summary
In large-scale commercial platforms, determining which disparate records refer to the same real-world business (Entity Resolution) is computationally heavy and complex due to missing IDs, typos, and formatting noise. 

**Team Mark-85 Matrix** developed a two-stage deterministic blocking and gradient-boosting classification pipeline. By engineering a low-cost "Cheap Key" for initial candidate generation and utilizing advanced string similarity features (RapidFuzz), we reduced $O(N^2)$ time complexity to a manageable subset. A LightGBM classifier, heavily calibrated for precision ($>0.88$ probability threshold), handled the final entity merging.

---

## 🧠 System Architecture

The pipeline is divided into three distinct execution phases:

1. **Phase 1: Ingestion & Standardization** - Load raw `Source 1` (Reference), `Source 2`, and `Source 3` data.
   - Text normalization: Lowercasing, regex-based special character stripping, and NaN handling.
2. **Phase 2: Deterministic Blocking (Candidate Generation)**
   - **Cheap Key Formulation:** `[First 4 chars of Name] + [First 3 chars of Address]`
   - **Output:** A highly targeted candidate pool (`candidate_pairs.tsv`) that eliminates millions of impossible comparisons.
3. **Phase 3: High-Precision Classification**
   - **Feature Extraction:** Token Set Ratio, Token Sort Ratio, Jaro-Winkler distance, and Absolute Length differences.
   - **Inference:** LightGBM classifier with a strict decision boundary to avoid F-beta false-merge penalties. Singletons are explicitly isolated to capture 1.0 reward scores.

---

## 🔬 Methodology & Pipeline

### 1. Blocking Strategy
Comparing every record across datasets equates to billions of cross-joins. Our deterministic blocking strategy acts as a high-recall filter. By combining alphanumeric substrings of names and addresses, we successfully grouped entities bypassing transliteration errors and abbreviation noise (e.g., *Pvt* vs *Private*).

### 2. Feature Engineering
We extracted purely textual distances since external geocoding APIs were prohibited. 
* **RapidFuzz Implementations:** Used for blazingly fast Levenshtein and Jaro-Winkler calculations.
* **Token Matching:** Accounted for out-of-order words (e.g., "Acme Corp Bangalore" vs "Bangalore Acme Corporation").

### 3. $F_{0.5}$ Threshold Calibration
The evaluation metric, $F_{0.5} = (1.25 \times Precision \times Recall) / (0.25 \times Precision + Recall)$, penalizes false positives twice as harshly as false negatives. We explicitly shifted our LightGBM prediction threshold to $0.88$. Ambiguous pairs were rejected, ensuring high precision and maximizing points on singletons (entities with no matches).

---

## 📂 Repository Structure

```text
Amazon-ML-Challenge-2026/
├── code/
│   └── business_entity_resolution/
│       ├── src/
│       │   └── main.py                 # Core pipeline script (Blocking + ML Inference)
│       ├── requirements.txt            # Environment dependencies
│       └── README.md                   # Execution instructions
├── output/
│   ├── candidate_pairs.tsv             # Generated blocking candidates
│   └── matching_results.tsv            # Final submission predictions
└── README.md                           # Comprehensive project documentation



```

## ⚙️ How to Reproduce

1. **Clone the repository:**
   ```bash
   git clone [https://github.com/YourUsername/Amazon-ML-Challenge-2026.git](https://github.com/YourUsername/Amazon-ML-Challenge-2026.git)
   cd Amazon-ML-Challenge-2026/code/business_entity_resolution


   ```

  
