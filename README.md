# MLOps Spam Detection Pipeline
**MLOps Fundamentals - LTAT.02.038**

End-to-end Machine Learning pipeline for **Spam Detection** built using **MLOps practices**.  
The workflow is automated using **DVC** and experiments are tracked using **dvclive**.

The goal of this project is to build a **reproducible ML pipeline** that automates the entire workflow from raw data to model evaluation while enabling easy experiment tracking. The system classifies SMS messages as **spam or ham**.

## GitHub Repository
https://github.com/Nohka/03_mlops_pipeline/tree/main

---

# Pipeline Architecture

The pipeline follows the standard ML workflow:

```
Raw Data
   ↓
Data Ingestion
   ↓
Data Preprocessing
   ↓
Feature Engineering
   ↓
Model Training
   ↓
Model Evaluation
```

Each stage is implemented as a **separate Python module** and orchestrated using **DVC**.

---

# Pipeline Stages

## 1. Data Ingestion

This stage loads the dataset and prepares it for training.

Tasks performed:

* Load spam dataset from GitHub
* Remove unnecessary columns
* Rename columns
* Split data into training and testing sets

Output:

```
data/raw/train.csv
data/raw/test.csv
```

---

## 2. Data Preprocessing

The text data is cleaned and normalized.

Steps performed:

* Label encoding of target variable
* Removing duplicate rows
* Lowercasing text
* Tokenization
* Stopword removal
* Stemming using **PorterStemmer**

Output:

```
data/interim/train_processed.csv
data/interim/test_processed.csv
```

---

## 3. Feature Engineering

Text messages are converted into numerical features using **TF-IDF vectorization**.

Parameter used:

```
max_features = 50 (default)
```

Output:

```
data/processed/train_tfidf.csv
data/processed/test_tfidf.csv
```

---

## 4. Model Building

A **Random Forest classifier** is trained on the processed dataset.

Parameters:

```
n_estimators = 25 (default)
random_state = 2 (default)
```

The trained model is stored as:

```
models/model.pkl
```

---

## 5. Model Evaluation

The trained model is evaluated on the test dataset using:

* Accuracy
* Precision
* Recall
* AUC

Metrics are saved to:

```
reports/metrics.json
```

---

# Automating the Pipeline with DVC

After setting up the files, the entire ML workflow is automated using **DVC pipelines**.

The pipeline stages are defined in:

```
dvc.yaml
```

Example structure:

```
stages:
  data_ingestion
  data_preprocessing
  feature_engineering
  model_building
  model_evaluation
```

Each stage defines:

* command to execute
* dependencies
* outputs

To run the full pipeline:

```
dvc repro
```

DVC automatically reruns only the stages affected by changes.

---

# Configurable Parameters (params.yaml)

In order not to change parameters in several files, we defined a YAML file to store the parameters to be changed and integrated reading the parameters in in all of the corresponding stages' scripts. Pipeline parameters are stored in:

```
params.yaml
```

Example configuration we started with (default):

```
data_ingestion:
  test_size: 0.30

feature_engineering:
  max_features: 50

model_building:
  n_estimators: 25
  random_state: 2
```

Using a parameter file allows experiments to be run **without modifying code**.

Changing parameters and running the pipeline generates new experiments.

---

# Experiment Tracking with DVC

Experiments are tracked using **DVC experiments and dvclive**.

Commands used:

```
dvc exp run
dvc exp show
```

Each experiment records:

* parameters
* evaluation metrics
* model results

This makes it easy to compare different model configurations.

---

# Best Experiment

Best configuration:

```
data_ingestion:
  test_size: 0.19

feature_engineering:
  max_features: 55

model_building:
  n_estimators: 22
  random_state: 2
```

Results:

| Metric    | Score |
| --------- | ----- |
| Accuracy  | 0.955 |
| Precision | 0.917 |
| Recall    | 0.75  |
| AUC       | 0.931 |

---

# Technologies Used

* Python
* Scikit-learn
* NLTK
* DVC
* dvclive

---

# Key Learnings

* Building modular machine learning pipelines
* Automating ML workflows with **DVC**
* Using **params.yaml** for configurable experiments
* Tracking experiments with **dvclive**
* Ensuring reproducibility in ML projects
* Storing DVC info remotely in S3
* Also, knowing when to do dvc add/commit/push and when git add/commit/push
