# Data Lineage System Documentation

## Table of Contents
1. Business Motivation
2. Introduction
3. High-Level Overview
4. Component Deep Dive
5. Model & Matching Logic
6. End-to-End Flow
7. Usage Guidelines
8. Best Practices
9. Case Study
10. Future Directions

---

## 1. Business Motivation

### 1.1 The Need for Automated Data Lineage
In modern enterprises, data is distributed across heterogeneous sources — CRM systems, ERPs, data warehouses, legacy databases, APIs, and third-party vendors. However, these sources often represent the same data using different column names, formats, and encodings. For example:

| Source A | Source B | Real Meaning |
|----------|----------|--------------|
| customer_id | cust_no | Customer ID |
| first_name | name | Customer First Name |
| amount_usd | price | Transaction Amount |

Manually resolving these inconsistencies, known as **Data Lineage**, is essential for achieving trustworthy data integration, reporting, and analysis.

### 1.2 Business Scenarios Where Lineage Matters

| Scenario | Role of Data Lineage |
|----------|----------------------|
| Mergers & Acquisitions | Harmonize and integrate systems from different organizations |
| Data Lakes | Build unified schema from inconsistent data sources |
| Cloud Migration | Seamlessly map legacy tables to new cloud structures |
| Regulatory Compliance | Trace data transformations for audit readiness |
| Enterprise Reporting | Ensure consistent KPIs across departments |

### 1.3 Challenges Without Automation
- Manual mapping can take **weeks or months**.
- Prone to human error and inconsistency.
- Difficult to scale to hundreds of tables.
- Expensive due to reliance on senior data engineers.

### 1.4 Business Value Delivered
- **90%+ Reduction** in time-to-lineage.
- **15-25% Increase** in mapping accuracy.
- **Fully repeatable** and consistent results.
- **Cost savings** by reducing manual effort.
- **Audit-friendly** outputs for governance.

---

## 2. Introduction

### 2.1 Purpose of the System
The Data Lineage System automates the task of identifying matching columns across two tables by learning from existing datasets. It provides business users, data engineers, and data scientists with reliable, scalable, and interpretable lineage outputs.

### 2.2 Conceptual Example

| Table A | Table B |
|---------|---------|
| customer_id | user_id |
| email | email_address |
| dob | date_of_birth |

The system automatically identifies these as matching columns based on **name similarity**, **data distribution**, and **semantic embedding**.

### 2.3 System Capabilities
- Works on CSV, JSON, JSONL tables.
- Supports different matching strategies (One-to-One, One-to-Many, Many-to-Many).
- Produces both **probability matrix** and **binary match matrix**.
- Uses a combination of statistical, textual, and AI-powered semantic features.

---

## 3. High-Level Overview

### 3.1 Overview of the Workflow
1. Input Tables
2. Data Preprocessing
3. Feature Extraction
   - Self Features (individual columns)
   - Relation Features (column pairs)
4. Feature Matrix Generation
5. Model Inference
6. Matching Strategy
7. Output Matrices

### 3.2 Output
- **Similarity Matrix** (probabilities)
- **Matched Pairs** (selected matches based on threshold and strategy)

---

## 4. Component Deep Dive

### 4.1 Self Feature Extraction
Extracts properties of each column individually:
- Detects Data Type (Numeric, Text, Date, URL)
- Computes statistical properties (mean, variance, uniqueness)
- Character distribution (punctuation %, numeric %, etc.)
- Embedding vector using a **Transformer-based model**

### 4.2 Relation Feature Extraction
For every possible pair of columns across two tables, the system computes:
- Name Similarity (BLEU, Edit Distance, LCS)
- Distribution Similarity (distributional distance)
- Embedding Similarity
- Instance Similarity (row-level content similarity)
- One-in-One Check (e.g., `dob` vs `date_of_birth`)

---

## 5. Model & Matching Logic

### 5.1 ML Model
- Type: **XGBoost** classifier
- Input: Extracted feature matrix
- Output: Probability of being a match

### 5.2 Matching Strategy
- **Many-to-Many** (default)
- **One-to-One**
- **One-to-Many**

### 5.3 Thresholding
- Uses default or user-specified threshold.
- Converts probabilities into 0/1 decisions.

---

## 6. End-to-End Flow
1. Input `Table1.csv` and `Table2.csv`.
2. Features are extracted.
3. Model infers similarity scores.
4. Strategy applies matching logic.
5. Outputs:
   - `similarity_matrix_value.csv`
   - `similarity_matrix_label.csv`
   - List of matching column pairs

---

## 7. Usage Guidelines

### Inference Command
```bash
python -m data_lineage -p /path/to/tables -s one-to-one
```

### Training Command
```bash
python -m data_lineage.relation_features
python -m data_lineage.train
```

---

## 8. Best Practices
- Clean data before running.
- Choose appropriate matching strategy.
- Review threshold tuning.
- For domain-specific projects, prefer custom training.

---

## 9. Case Study
A retail client integrated 8 systems post-acquisition.
- 37 source tables analyzed.
- Used **One-to-Many** strategy.
- Reduced manual mapping time from 6 weeks to 4 days.
- 93% matching accuracy.
- Enabled faster dashboard delivery.

---

## 10. Future Directions
- Multi-table simultaneous matching.
- Schema recommendation engine.
- Improved UI for business users.
- Better handling of free-text and semi-structured data.

