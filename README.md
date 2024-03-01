# Anonify: Decentralized Dual-level Anonymity for Medical Data Donation
>**Abstract:** Medical data donation involves voluntarily sharing medical data with research institutions, which is crucial for advancing healthcare research. However, the sensitive nature of medical data poses privacy and security challenges. The primary concern is the risk of de-anonymization, where users can be linked to their donated data through background knowledge or communication metadata (e.g., user’s location). In this paper, we introduce Anonify, a decentralized anonymity protocol offering strong user protection during data donation without reliance on a single entity. It achieves dual-level anonymity protection, covering both communication and data aspects by leveraging Distributed Point Functions, and incorporating 𝑘-anonymity and stratified sampling within a secret-sharing-based setting. Anonify ensures that the donated data is in a form that affords flexibility for researchers in their analyses. Our evaluation demonstrates the efficiency of Anonify in preserving privacy and optimizing data utility. Furthermore, the performance of machine learning algorithms on the anonymized datasets generated by the protocol shows high accuracy and precision.

## Description
This repository contains code written to assess the performance of the Anonify protocol in terms of data utility and anonymity. The reasons for not considering the evaluation of the anonymous communication aspect are discussed in our paper.
The repository includes the implementation for stratified sampling. Additionally, it provides the code required to replicate the experiments outlined in the paper. These experiments cover the following areas: 

- Measuring data utility in anonymized datasets using the Normalized Certainty Penalty (NCP) metric.  
- Examining the data distribution characteristics post-application of our protocol. 
- Assessing the performance of machine learning classification algorithms on the anonymized datasets produced by our protocol. 
- Evaluating re-identification risks in datasets anonymized by our protocol using the Journalist Risk and Certainty metrics. 

In all our sampling experiments, we ran each experiment 10 times and computed the average results. This is necessary because the selection of records to be released within each equivalence class is performed randomly.

## General usage
This section contains information and instructions on using stratified sampling. See below for replicating the experiments in the paper.
### Setup

#### Prerequisites
- Python 3.10+
#### Installation
- Clone this repository: ``git clone https://github.com/lng-ng/anonify.git``
- Setup a virtual environment using virtualenv/conda. For running the experiments, conda was used.
- Run ``pip install -r requirements.txt`` for the dependencies.

### K-Anonymization
In our experiments, we used the [anonymization library](https://github.com/fhstp/k-AnonML) by Slijepčević et al. and the [ARX anonymization tool](https://github.com/arx-deidentifier/arx/).

### Stratified Sampling
Run ``python stratified_sampling.py conf.json`` to produce a sampled version of the input $k$-anonymized dataset.
#### Input
The input dataset has to be $k$-anonymized beforehand. It needs to have the correct format: a ``.csv`` file, where each row represents a record and each column represents an attribute. The separator for the CSV is ``,``. An example dataset can look like this:
| PID | Sex | Age | Test result |
| ---- | ----- | -----| -----|
| 3 | F | 32 | Positive |
| 5 | F | 26 | Negative |
| 8 | M | 45 | Positive |
| 9 | M | 37 | Negative |
| 12 | M | 42 | Negative | 
| 16 | F | 28 | Negative |

Any $k$-anonymization method should work so long as the above requirements for the dataset are fulfilled.

#### Configuration file
Options are given in the JSON configuration file. Below are descriptions of the fields.
| Field | Description
| :--- | :----------
| `id` | name of the sampling process. This field is part of the output name.
| `input` | path to the input dataset.
| `qids` | list of all attributes which are quasi-identifiers of the input dataset.
| `deletion_percentage` | determines the percentage of records being **deleted** from the dataset. E.g if you want a sampling percentage of 70%, then the deletion percentage would be 30% or 0.3. The percentage value is in the range of $[0,1]$.
| `num_experiments` | number of times the sampling process will be run.

Sampled datasets are produced as the output. The output of the $i$-th run follows the naming convention ``{id}_sampled_p{deletion_percentage*100}_t{i}.csv``

## Replicating the experiments
For replicating the experiments in the paper, Jupyter notebooks are provided.
### Dataset
In our assessment, we simulate user records sent to the aggregator using the Diabetes Prediction dataset, available [here](https://www.kaggle.com/datasets/iammustafatz/diabetes-prediction-dataset). This dataset includes 100,000 records, each representing a distinct individual, featuring medical and demographic data alongside diabetes status (i.e., whether individuals have diabetes or not). In our evaluation, the entire dataset is employed, resulting in a user base size of 100,000 individuals. We designate age, gender, and BMI as quasi-identifiers (QIDs). The remaining attributes, encompassing crucial health-related information, are considered as sensitive attributes (SAs).

### Running on Google Colab
We recommend running the notebooks using Google Colab to skip the hassle of setting up an environment.
#### Information Loss Assessment (Section 6.2.1 in the paper)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/lng-ng/anonify/blob/main/info_loss.ipynb)
#### Data Distribution Assessment (Section 6.2.2 in the paper)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/lng-ng/anonify/blob/main/data_distribution.ipynb)
#### Machine Learning Classifiers’ Performance Assessment (Section 6.2.3 in the paper)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/lng-ng/anonify/blob/main/classifiers.ipynb)

Note that this experiment takes a while to finish (~20 minutes)
#### Data Anonymity Assessment (Section 6.3 in the paper)
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/lng-ng/anonify/blob/main/data_anonymity.ipynb)

### Running locally
First, follow the setup instructions from the section above (General usage).  
Then, to run the notebooks locally, additional requirements other than those for general usage have to be installed.  
```
pip install notebook
pip install -r expr_requirements.txt
pip install -r requirements.txt
```
Open Jupyter Notebook at the local repository:
```
jupyter notebook
```
Then open a notebook, depending on which experiments you want to replicate.
| Notebook | Experiment
| :--- | :----------
| `info_loss.ipynb` | Information Loss Assessment
| `data_distribution.ipynb` | Data Distribution Assessment
| `classifiers.ipynb` |  Machine Learning Classifiers’ Performance Assessment
| `data_anonymity.ipynb` | Data Anonymity Assessment

When running the notebooks locally, ignore the Environment Setup section because they are only needed for Colab.

### Downloading necessary data
Each experiment requires having certain datasets as input.
We provide a script ``utils/download.py`` to automatically download the necessary datasets for each experiment.
```
python utils/download.py [infoloss | dist_data | ml_data | anon_data]
```
Choose a parameter based on which experiment you want to run, e.g., for Information Loss Assessment ``python utils/download.py infoloss``.
Note that in the notebook for each experiment, this step is already included so that you do not have to execute this yourself when running the notebook. This section is here mainly to provide additional information. However, the data can also be downloaded manually:
- for Information Loss Assessment: [k-anonymized datasets at different values for k](https://drive.google.com/drive/folders/1G-7anLLgO9bZbg7fL_dAuxHhqf_VK67Y?usp=drive_link) (it can take a while to download the data)
- for Data Distribution Assessment: [k-anonymized dataset using ARX, k=250](https://drive.google.com/file/d/1SRogEdk7E8REmXmt9CwpWyFF2AL_e37b/view?usp=drive_link) and [original dataset](https://drive.google.com/file/d/1Mpsr0XfQ-yAyQzarbfGEnu34Li0zVtOU/view?usp=drive_link)
- for Machine Learning Classifiers’ Performance Assessment: [k-anonymized dataset using ARX, k=250](https://drive.google.com/file/d/1SRogEdk7E8REmXmt9CwpWyFF2AL_e37b/view?usp=drive_link) and [original dataset](https://drive.google.com/file/d/1Mpsr0XfQ-yAyQzarbfGEnu34Li0zVtOU/view?usp=drive_link)
- for Data Anonymity Assessment: [k-anonymized dataset using ARX, k=250](https://drive.google.com/file/d/1SRogEdk7E8REmXmt9CwpWyFF2AL_e37b/view?usp=drive_link)

Alternatively, you can also create the data yourself:
- Download the original _Diabetes_ dataset from [Kaggle](https://www.kaggle.com/datasets/iammustafatz/diabetes-prediction-dataset).
- Install [ARX](https://github.com/arx-deidentifier/arx/) and the [anonymization library](https://github.com/fhstp/k-AnonML) by Slijepčević et al., then use them to anonymize the dataset.

## Citation
If you use our code, please cite our paper:

[Currently, this information is omitted for anonymity.]



