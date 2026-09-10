# Qatra: Decision Support System for Smart Agriculture

Business Intelligence and Machine Learning system that classifies farms as water-Efficient or Inefficient to recommend optimal irrigation strategies. The system combines a Talend ETL pipeline, a PostgreSQL star-schema data warehouse, a two-stage ML pipeline, and Power BI analytics dashboards.

<p>
  <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
  <img src="https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white"/>
  <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white"/>
  <img src="https://img.shields.io/badge/Talend%20Open%20Studio-FF6D70?style=for-the-badge&logo=talend&logoColor=white"/>
  <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
</p>

---

## Project Documentation

📄 **[View Full Project Report (PDF)](report%20PI.pdf)**  
📊 **[View Presentation Slides (PDF)](docs/presentation.pdf)**

---

## Problem Statement

Small-scale farmers in Tunisia frequently rely on manual or fixed-timer irrigation schedules. This operational approach results in:

* Excess water consumption and higher utility costs
* Soil degradation caused by over-watering or under-watering
* Reduced crop yields due to improper irrigation timing and volume

Existing tools lack localized data insights to guide farmers on precise irrigation timing, volume, and methods.

---

## Solution Overview

Qatra processes historical agricultural records across 5,000 farms to automate irrigation decision-making. The platform executes an end-to-end data workflow:

1. **Ingestion & ETL:** Cleans and structures raw farm records into a star-schema data warehouse.
2. **Two-Stage Machine Learning:** Groups unlabeled farms using unsupervised clustering, then trains a supervised classifier to predict efficiency for new records.
3. **Analytics & Dashboards:** Surfaces water usage trends, crop performance, and efficiency ratings through interactive Power BI views.

---

## Technical Architecture

```mermaid
flowchart LR
    A[("Raw Agricultural Data<br/>(CSV / Source DB)")] -->|Extract & Transform| B["Talend ETL Pipeline"]
    B -->|Load| C[("PostgreSQL<br/>Star-Schema<br/>Data Warehouse")]
    C -->|Feature Extraction| D["ML Pipeline<br/>Stage 1: K-Means<br/>Stage 2: Classifier"]
    D -->|Efficiency Labels| C
    C -->|Connect & Model| E["Power BI Dashboards"]

    style A fill:#8B5E3C,color:#fff
    style B fill:#FF6D70,color:#fff
    style C fill:#336791,color:#fff
    style D fill:#F7931E,color:#fff
    style E fill:#F2C811,color:#000
