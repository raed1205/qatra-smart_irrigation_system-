  #  Qatra — Decision Support System for Smart Agriculture
  
  > A full-stack Business Intelligence + Machine Learning system that classifies farms as water-**Efficient** or **Inefficient** and recommends optimal irrigation decisions — built on a Talend ETL pipeline, a PostgreSQL star-schema data warehouse, a two-stage ML pipeline, and Power BI dashboards.
  
  <p>
    <img src="https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white"/>
    <img src="https://img.shields.io/badge/scikit--learn-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white"/>
    <img src="https://img.shields.io/badge/PostgreSQL-4169E1?style=for-the-badge&logo=postgresql&logoColor=white"/>
    <img src="https://img.shields.io/badge/Talend%20Open%20Studio-FF6D70?style=for-the-badge&logo=talend&logoColor=white"/>
    <img src="https://img.shields.io/badge/Power%20BI-F2C811?style=for-the-badge&logo=powerbi&logoColor=black"/>
  </p>
  
  ---
  
  ##  Problem Statement
  
  Farmers in Tunisia — particularly small-scale farmers — largely rely on **manual or timer-based irrigation**. This leads to:
  
  -  Significant **water waste**
  -  **Soil degradation** from over- or under-watering
  -  **Reduced crop yields** from suboptimal irrigation timing and volume
  
  No data-driven system currently exists to help small farmers decide *when*, *how*, and *how much* to irrigate based on actual farm conditions.
  
  ##  Solution Overview
  
  **Qatra** ("drop" in Arabic) is a Decision Support System that ingests real agricultural data, classifies each farm as **Efficient** or **Inefficient** using a two-stage Machine Learning pipeline, and surfaces the results — alongside water usage, yield, and irrigation analytics — in interactive Power BI dashboards. The goal is a single pipeline a farm advisory service or agri-tech platform could run end-to-end: from raw records to a recommendation.
  
  **Trained and validated on 5,000 farm records.**
  
  ---
  
  ##  Technical Architecture
  
  ```mermaid
  flowchart LR
      A[("Raw Agricultural Data<br/>(CSV / Source DB)")] -->|Extract & Transform| B["Talend ETL Pipeline"]
      B -->|Load| C[("PostgreSQL<br/>Star-Schema<br/>Data Warehouse")]
      C -->|Feature Extraction| D["ML Pipeline<br/>Stage 1: K-Means Clustering<br/>Stage 2: Supervised Classifier"]
      D -->|Efficient / Inefficient Labels| C
      C -->|Connect & Model| E["Power BI Dashboards"]
  
      style A fill:#8B5E3C,color:#fff
      style B fill:#FF6D70,color:#fff
      style C fill:#336791,color:#fff
      style D fill:#F7931E,color:#fff
      style E fill:#F2C811,color:#000
  ```
  
  **Flow:** `Raw Data → Talend ETL → Star Schema Data Warehouse → ML Pipeline → Power BI Dashboards`
  
  | Layer | Tool | Role |
  |---|---|---|
  | Ingestion | Talend Open Studio | Extracts and transforms raw farm records into a clean, loadable format |
  | Data Warehouse | PostgreSQL (star schema) | Central store for farm, crop, irrigation, and seasonal dimensions + performance facts |
  | ML Pipeline | Python (scikit-learn) | Clusters farms, then classifies each as Efficient / Inefficient |
  | Visualization | Power BI Desktop | Dashboards on water usage, yield, and irrigation performance |
  
  ---
  
  ## ⚙️ ETL Pipeline
  
  Talend Open Studio handles the extract-transform-load layer, turning raw farm records into a warehouse-ready format:
  
  - **Cleaning** — handling missing values, standardizing units (e.g., acre vs. hectare), correcting inconsistent categorical labels
  - **Enrichment** — deriving `area_category` (Small / Medium / Large) and `crop_category` (Cereal, Vegetable, Legume, Fiber, Cash Crop) from raw crop and acreage fields
  - **Loading** — populating the star-schema dimensions and the central fact table in PostgreSQL
  
  ---
  
  ##  Data Warehouse  Star Schema
  
  Designed around farm performance as the central fact, surrounded by conformed dimensions for crop, area, irrigation method, and season:
  
  ```mermaid
  erDiagram
      dim_crop ||--o{ fact_farm_performance : "crop_id"
      dim_area ||--o{ fact_farm_performance : "area_id"
      dim_irrigation ||--o{ fact_farm_performance : "irrigation_id"
      dim_season ||--o{ fact_farm_performance : "season_id"
  
      fact_farm_performance {
          int farm_id PK
          int crop_id FK
          int area_id FK
          int irrigation_id FK
          int season_id FK
          decimal water_usage_volume
          decimal water_per_acre
          decimal yield_amount
          decimal yield_per_acre
          string efficiency_label
      }
      dim_crop {
          int crop_id PK
          string crop_type
          string crop_category
      }
      dim_area {
          int area_id PK
          string area_category
      }
      dim_irrigation {
          int irrigation_id PK
          string irrigation_type
      }
      dim_season {
          int season_id PK
          string season_name
          int quarter
      }
  ```
  
  > Schema inferred from the dashboard's dimensions (crop, area, irrigation type, season/quarter) and measures (water usage, yield). Update table/column names above to match your actual `CREATE TABLE` statements before publishing.
  
  ---
  
  ## 🤖 Machine Learning Pipeline
  
  A **two-stage pipeline** rather than a single classifier, chosen because "efficient" isn't a label that exists in raw farm data — it has to be derived first.
  
  ### Stage 1 — K-Means Clustering (Unsupervised)
  
  Groups the 5,000 farm records into clusters based on water usage, yield, and irrigation features, revealing natural groupings in farming behavior before any labels exist.
  
  ```python
  from sklearn.cluster import KMeans
  from sklearn.preprocessing import StandardScaler
  
  X_scaled = StandardScaler().fit_transform(X)
  kmeans = KMeans(n_clusters=k, random_state=42)
  clusters = kmeans.fit_predict(X_scaled)
  ```
  
  The resulting clusters are analyzed and mapped to an **Efficient / Inefficient** label based on their water-usage-to-yield characteristics — this becomes the target variable for Stage 2.
  
  ### Stage 2 — Supervised Classification
  
  A supervised classifier is trained on the cluster-derived labels to generalize the Efficient/Inefficient decision to new, unseen farm records — this is the model that runs in production, since it doesn't require re-clustering every time.
  
  | Step | Detail |
  |---|---|
  | Candidate models evaluated | `[e.g. Logistic Regression, Decision Tree, Random Forest, Gradient Boosting]` |
  | Selection method | Benchmarked on accuracy / F1-score via train-test split (and/or cross-validation) |
  | **Selected model** | `[fill in — the best-performing classifier from your benchmark]` |
  | Reported metrics | `[accuracy: __%, F1-score: __, precision: __, recall: __]` |
  
  > 🔧 **Fill in the bracketed fields** with your actual benchmark results before publishing — this table is the model-selection story you'll be asked about in an interview, so it needs to match what you actually ran.
  
  ---
  
  ## 📊 Power BI Dashboards
  
  Two connected dashboard pages, both filterable by **Quarter**:
  
  **Water Consumption Analysis**
  - Total water usage volume & water usage per acre (KPI cards)
  - Water usage by crop type (treemap)
  - Water usage by area category — Small / Medium / Large (treemap)
  - Water usage by irrigation type — Drip, Rain-fed, Manual, Sprinkler, Flood (bar chart)
  - Water usage by season — Kharif, Zaid, Rabi (bar chart)
  - Water usage by crop category — Cereal, Vegetable, Legume, Fiber, Cash Crop (pie chart)
  
  **Yield & Productivity Analysis**
  - Total yield amount & yield per acre (KPI cards)
  - Yield per acre by area category (pie chart)
  - Yield per acre by crop type (treemap)
  - Yield per acre and yield amount by crop type (combo chart)
  - Yield amount trend by month (line chart)
  - Yield per acre by irrigation type (bar chart)
  
  ---
  
  ## 🚀 Installation & Setup
  
  ### 1. Clone the repository
  
  ```bash
  git clone https://github.com/RaedMeddeb/qatra-irrigation-dss.git
  cd qatra-irrigation-dss
  ```
  
  ### 2. Set up the database
  
  ```bash
  # Create the source and warehouse databases in PostgreSQL / PgAdmin 4
  psql -U postgres -f database/source_schema.sql
  psql -U postgres -f database/dw_schema.sql
  ```
  
  ### 3. Run the Talend ETL jobs
  
  Open the `etl/` jobs in **Talend Open Studio for Data Integration** and run them in order to populate the star schema from the raw source data.
  
  ### 4. Set up the Python environment for the ML pipeline
  
  ```bash
  python -m venv venv
  source venv/bin/activate   # on Windows: venv\Scripts\activate
  pip install -r requirements.txt
  ```
  
  ### 5. Run the ML pipeline
  
  ```bash
  python ml/train_clustering.py     # Stage 1 — K-Means clustering
  python ml/train_classifier.py     # Stage 2 — supervised classification
  ```
  
  ### 6. Open the dashboards
  
  Open `dashboards/Qatra_Dashboard.pbix` in **Power BI Desktop** and point the data source connection at your `pizza_dw`-style warehouse (update connection settings under *Transform Data → Data Source Settings*).
  
  ---
  
  ## 📦 requirements.txt
  
  ```txt
  pandas>=2.0
  numpy>=1.24
  scikit-learn>=1.3
  matplotlib>=3.7
  seaborn>=0.12
  ```
  
  ---
  
  ## 📁 Repository Structure
  
  ```
  qatra-irrigation-dss/
  ├── data/
  │   └── farm_records.csv          # 5,000 raw farm records
  ├── database/
  │   ├── source_schema.sql
  │   └── dw_schema.sql             # star schema (fact + 4 dimensions)
  ├── etl/
  │   ├── Load_DIM_CROP.item
  │   ├── Load_DIM_AREA.item
  │   ├── Load_DIM_IRRIGATION.item
  │   ├── Load_DIM_SEASON.item
  │   └── Load_FACT_FARM_PERFORMANCE.item
  ├── ml/
  │   ├── train_clustering.py       # Stage 1 — K-Means
  │   ├── train_classifier.py       # Stage 2 — supervised model
  │   ├── evaluate.py               # model benchmarking / metrics
  │   └── model.pkl                 # serialized final model
  ├── dashboards/
  │   └── Qatra_Dashboard.pbix
  ├── docs/
  │   ├── architecture_diagram.png
  │   └── project_report.pdf
  ├── requirements.txt
  └── README.md
  ```
  
  ---
  
  ## 👥 Contributors
  
  Built as an academic Business Intelligence / Machine Learning project (Team of 5).
  **Azza Jouini** Project Architect and Team Leader
  **Firas Ben Salem**
  **Hanna Hmouda**
  - **Raed Meddeb** — Benchmarked and selected the classification algorithm for Stage 2 of the ML pipeline; contributed to the Power BI dashboard design
  **Salsabil Ben Elhadj**
  
  
  
  
  
  
  
  
  ---
  
  ## 🔭 Possible Next Steps
  
  - Deploy the trained classifier behind a lightweight API for real-time farm scoring
  - Add a recommendation layer suggesting specific irrigation schedule changes, not just an Efficient/Inefficient label
  - Incorporate weather forecast data to make recommendations forward-looking rather than purely historical
