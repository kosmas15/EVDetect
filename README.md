# EVDetect: Orchestrated and AI-Powered Detection of Cyber-Attacks

This repository contains the source code, machine-learning workflows, serialized evaluation splits, and evaluation framework supporting the study **“EVDetect: A framework for orchestrated and AI-powered detection of cyber-attacks in EV charging networks,”** submitted to *Scientific Reports*.

**Archived version:** EVDetect v1.0.0, Zenodo  
**DOI:** [10.5281/zenodo.21476298](https://doi.org/10.5281/zenodo.21476298)

## Repository Structure

* `01_EDA.ipynb`: Exploratory Data Analysis and initial data preprocessing.
* `02_multiclass.ipynb`: Multiclass unsupervised exploration and attack-type classification pipelines (Benign, DoS, Recon, Backdoor, Cryptojacking).
* `03_binary.ipynb`: Binary unsupervised exploration and attack detection pipelines (Benign vs. Attack).
* `04_MLFLOW.ipynb`: MLflow experiment tracking implementation for model registry and hyperparameter logging.
* `05_bootstrap_cis.ipynb`: Statistical validation script that computes 95% Bootstrap Confidence Intervals (Accuracy, Precision, Recall, F1-macro) for the ensemble models and generates publication-ready LaTeX tables.
* `requirements.txt`: Core ML environment dependencies (Notebooks 01, 02, 03, 05).
* `requirements_mlflow.txt`: MLflow environment dependencies (Notebook 04).
* `data/`: Directory where the primary dataset (`EVSE-B-PowerCombined.csv`) must be placed (not distributed with this repository — see **Dataset** below).
* `models/`: Directory where trained models (`.pkl`, `.json`, `.keras`) and the exact serialized data splits (e.g., `TestSetsBinary/`, `TestSetsMultiCharging/`) are stored to guarantee reproducible evaluation.

## Dataset

This project uses the **CICEVSE2024** dataset (EVSE-B power consumption traces), published by Buedi et al.:

> Buedi, E.D., Ghorbani, A.A., Dadkhah, S., Ferreira, R.L.: *Enhancing EV charging station security using a multi-dimensional dataset: CICEVSE2024*. Data and Applications Security and Privacy XXXVIII, Springer (2024).

The dataset is **not redistributed in this repository**. Download it from the Canadian Institute for Cybersecurity dataset page (https://www.unb.ca/cic/datasets/evse-dataset-2024.html) and place the combined power-consumption file at:

```
data/EVSE-B-PowerCombined.csv
```

## Setup Instructions

The project was developed with **Python 3.11**. To guarantee clean dependencies and avoid conflicts between model training and experiment tracking, two separate virtual environments are used.

1. **Clone the repository**
   ```bash
   git clone https://github.com/kosmas15/EVDetect.git
   cd EVDetect
   ```

2. **Set up the Core ML Environment** (for Notebooks 01, 02, 03, 05)
   ```bash
   python -m venv evdetect_env
   source evdetect_env/bin/activate  # On Windows: evdetect_env\Scripts\activate
   pip install -r requirements.txt
   ```

3. **Set up the MLflow Environment** (for Notebook 04)
   ```bash
   python -m venv mlflow_env
   source mlflow_env/bin/activate    # On Windows: mlflow_env\Scripts\activate
   pip install -r requirements_mlflow.txt
   ```
   *Note: `requirements_mlflow.txt` was generated on Windows. On Linux/macOS, remove the Windows-only packages (`pywin32`, `pywinpty`, `pyreadline3`) from the file before installing.*

## Usage & Reproducibility

**Phase 1: Exploration & Training (`evdetect_env`)**
Activate `evdetect_env` and execute notebooks `01` through `03` to explore the physical feature space, apply SMOTE balancing, and train the baseline and ensemble models. The exact train/validation/test splits are serialized to the `models/` directory automatically. All random seeds are fixed (`random_state = 42`) for reproducibility.

*Note: Due to GitHub file size limits, the trained model files are not included in this repository. Running the Phase 1 notebooks will generate them in the working directory; move them into your local `models/` folder afterwards so that the evaluation sections and Notebooks 04–05 can load them.*

**Phase 2: MLflow Tracking (`mlflow_env`)**
Activate `mlflow_env`, start a local MLflow tracking server (`mlflow ui`, serving at `http://127.0.0.1:5000` by default), and execute notebook `04_MLFLOW.ipynb`. This logs hyperparameters, model artifacts, and functional/non-functional evaluation metrics in a registry-ready format.

**Phase 3: Statistical Evaluation (`evdetect_env`)**
Reactivate `evdetect_env` and execute notebook `05_bootstrap_cis.ipynb`. To prevent data leakage and stochastic variation, this notebook evaluates the trained models on the exact serialized test sets generated during Phase 1. It runs 1,000 percentile-bootstrap iterations to compute 95% Confidence Intervals for Accuracy, Precision, Recall, and F1-macro, and outputs publication-ready LaTeX tables.

## License

This project is licensed under the MIT License — see the [LICENSE](LICENSE) file for details.

## Citation

*(Placeholder: citation information will be updated upon publication.)*
