# Build an ML Pipeline for Short-Term Rental Prices in NYC

## Table of contents

- [Project Introduction](#project-introduction)
- [Set up](#set-up)
   - [Project Repository](#project-repository)
   - [Requirements](#requirements)
   - [Project Pipeline](#project-pipeline)
- [Project Commands](#how-to-use)
   - [Run Project](#run-project)
   - [EDA](#eda)
   - [Train Model](#train-model)
- [Links](#links)

# Project Introduction
Assume we are working for a property management company renting rooms and properties for short periods of 
time on various rental platforms. We need to estimate the typical price for a given property based 
on the price of similar properties. The company receives new data in bulk every week. The model needs 
to be retrained with the same cadence, necessitating an end-to-end pipeline that can be reused.

In this project We will build such a pipeline.


# Set up

## Project Repository
Firstly, this project can be cloned from my Git repository using the below command:

```
git clone https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices.git
```

and go into the repository:

```
cd build-ml-pipeline-for-short-term-rental-prices
```

## Requirements

In this project, following versions of libraries will be used, which is defined in `environment.yml` file:
```
name: nyc_airbnb_dev
channels:
  - conda-forge
  - defaults
dependencies:
  - mlflow=1.19.0
  - ipython=7.33.0
  - notebook=6.4.12
  - cookiecutter=1.7.2
  - hydra-core=1.1
  - matplotlib=3.3.4
  - pandas=1.2.3
  - git=2.30.2
  - pip=20.3.3
  - pip:
      - wandb==0.10.31

```

Make sure to have conda installed and ready, then create a new environment using the ``environment.yml``
file provided in the root of the repository and activate it:

```bash
> conda env create -f environment.yml
> conda activate nyc_airbnb_dev
```

## Project Pipeline
The ML Pipeline consists of following main steps:
   - `download`: Get and store sample dataset used for this project to Weight and Biases.
   - `basic_cleaning`: Perform pre-processing on raw data
   - `data_check`: Test cleaned data's quality versus defined threshold/assumptions
   - `data_split`: Split our cleaned data into trainval data and test data
   - `train_random_forest`: Train and Choose the best model
   - `test_regression_model`: Test trained model with a test dataset

The pipeline is triggered by `main.py` file while getting configuration from `config.yaml` through Hydra engine.

The overall pipeline can be illustrated as below from Wandb UI:
![img](images/wandb-pipeline-graph.png)

# Project Commands
## Run project
In order to run the pipeline when you are developing, you need to be in the root of the starter kit, then you can execute as usual:
```
mlflow run . 
```

However, we can also run each step separately, using the template:
```
mlflow run . -P steps=[step_name]
```

Or we can also run multi-steps:
```
mlflow run . -P steps=[step_name_1, step_name_2, step_name_2]
```
For example: `> mlflow run . -P steps=download,basic_cleaning`
## EDA
EDA step is not the main step but is very essential to understand data insights. To run this step, use:
```
mlflow run src/eda
```
## Train Model
For this project, the model is fine-tuned using the below command, feel free to adjust it to test your approach:
```
mlflow run . \
    -P steps=train_random_forest \
    -P hydra_options="modeling.max_tfidf_features=5,10,15 modeling.random_forest.n_estimators=200,300 modeling.random_forest.min_samples_split=4,6 -m"
```
By default, the pipeline use `components/get_data/data/sample1.csv` file for training. To use another sample for this pipeline, try:
```
mlflow run https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices.git \
    -v v1.0.2 \
    -P hydra_options="etl.sample='sample2.csv'"
```

**Note**:
To run the above command for `sample2.csv` successfully, the pipeline from W&B must meet these criteria:
- Artifact `clean_sample.csv` (Artifact type: `CLEAN_SAMPLE`) must have a version with tag `reference` (Required for step `data_check`)
- Artifact `random_forest_export` (Artifact type: `MODEL_EXPORT`) must have a version with tag `prod` (Required for step `test_regression_model`)

# Links
- Wandb Repository: [Link](https://wandb.ai/data-projects/nyc_airbnb?workspace=user-leminkhoa)
- Github Repository: [Link](https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices)
   - Released Versions: 
      - [v1.0.2](https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices/releases/tag/v1.0.2)
      - [v1.0.1](https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices/releases/tag/v1.0.1)
      - [v1.0.0](https://github.com/leminkhoa/build-ml-pipeline-for-short-term-rental-prices/releases/tag/v1.0.0) 

# License
This repository is forked from an [original repository](https://github.com/udacity/build-ml-pipeline-for-short-term-rental-prices) from `Udacity`. For further license information, please check:
[License](LICENSE.txt)
