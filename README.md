# Insurance-Analytics
KAIM Week 3 Repository

## 1. Project Overview

This project focuses on a machine learning rating portfolio data to perform comprehensive **Exploratory Data Analysis (EDA)** and identify key risk drivers. The primary goal is to segment the portfolio based on profitability (Loss Ratio) across various dimensions (Geography, Vehicle Type, Cover Group) to inform strategic underwriting and pricing decisions.

### Key Deliverables:
* **Data Version Control (DVC):** Establish a versioned data pipeline for raw and processed data.
* **Data Quality Assessment:** Identify and treat missingness and type errors in the source data.
* **Risk Segmentation:** Calculate and visualize Loss Ratios by Province, Vehicle Type, and Cover Group.
* **Temporal Analysis:** Track trends in premium and claims over the 18-month period to identify critical turning points and seasonality.

---

## 2. Setup and Environment

This project relies on **Git** for code versioning and **DVC (Data Version Control)** for managing large datasets and ensuring reproducibility.

### Prerequisites

* Python 3.8+
* Git
* DVC (installed via `pip install dvc` or `pip install dvc[s3]` if using S3 remote)

### Installation

1.  **Clone the Repository:**
    ```bash
    git clone [YOUR_REPO_URL]
    cd [YOUR_PROJECT_FOLDER]
    ```

2.  **Set up Virtual Environment:**
    ```bash
    python -m venv venv
    source venv/bin/activate  # On Linux/macOS
    .\venv\Scripts\activate   # On Windows
    ```

3.  **Install Dependencies:**
    ```bash
    pip install -r requirements.txt
    ```
    *(Note: You will need to create a `requirements.txt` containing `pandas`, `numpy`, `matplotlib`, `seaborn`, and `dvc`.)*

4.  **Initialize DVC and Remote Storage:**
    This project uses a local DVC cache for storage (easily switchable to S3/GCP/Azure).
    ```bash
    dvc init
    dvc remote add -d localstorage [PATH_TO_YOUR_STORAGE] # e.g., /mnt/dvc_storage
    ```

---

## 3. Data Pipeline and Reproducibility

The source data is versioned using DVC. To ensure you have the correct data for the analysis, run the following command:

### Pulling Data (The Processed CSV)
The processed CSV file (`MachineLearningRating_processed.csv`) is managed by DVC.

1.  **Pull the data from the remote cache:**
    ```bash
    dvc pull data/processed/MachineLearningRating_processed.csv.dvc
    ```

2.  **Verify Data Status:**
    The actual data file should now be present in your local `data/processed/` folder.

### Data Flow

| Stage | Input | Output | Description |
| :--- | :--- | :--- | :--- |
| **00-Raw Data** | N/A | `data/raw/MachineLearningRating_v3.txt` | Raw input file (initial manual addition to DVC). |
| **01-Process** | `data/raw/...v3.txt` | `data/processed/...processed.csv` | Python script to clean, format types, handle missing values, and convert delimiters. |
| **02-EDA** | `data/processed/...csv` | Visualizations (.pngs) | Jupyter Notebook for EDA, risk assessment, and identifying key insights. |

---

## 4. Analysis and Key Findings

The main analysis is conducted within the **`02_Exploratory_Data_Analysis.ipynb`** Jupyter Notebook.

### 4.1. Data Quality Highlights
* **Missingness:** The `Gender` column is unusable for segmentation (approx. 90% "Not specified").
* **Type Fixes:** `TransactionMonth` and `VehicleIntroDate` were converted to `datetime` objects.
* **Concentration:** The portfolio is dominated by **Gauteng**, **Passenger Vehicles**, and **Monthly** policies.

### 4.2. Risk Segmentation Insights
* **Unprofitable Product:** **Comprehensive - Taxi** showed a Loss Ratio (LR) above 1.0 (claims > premium), signaling critical pricing failure.
* **Profitable Product:** **Credit Protection** showed a very low Loss Ratio, representing a high-margin product opportunity.
* **Claim Severity:** Non-zero claims exhibit a **multimodal distribution** with three distinct peaks, indicating separate severity bands (low, medium, high) that should be modeled independently.

---

## 5. Contribution and Next Steps

To contribute, ensure your DVC and Git commits are synchronized:
1.  Make code changes and commit to Git: `git commit -m "..."`
2.  Make data/metadata changes and push to DVC: `dvc push`

### Next Modeling Steps
1.  **Feature Engineering:** Calculate vehicle age and create binary flags for high missingness columns (`WrittenOff`, `CustomValueEstimate`).
2.  **Risk Modeling:** Develop a Generalized Linear Model (GLM) for predicting claim frequency and claim severity, possibly using a two-part model to account for the zero-claim spike.
