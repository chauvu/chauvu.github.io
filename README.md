# Data science portfolio by Chau Vu
This portfolio is a combination of notebooks I have created for my data science projects.

## Web Scraping

**Scrape College Rankings from CollegeFactual.com**

Ranking list of American colleges was scraped from [College Factual](https://www.collegefactual.com/rankings/best-colleges/). Details about each college, such as academics, student diversity and tuition fees, are scraped as well. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/college_factual), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/scrape_CollegeFactual.ipynb)

**Scrape Manuscripts from PubMed.com**

Biomedical and life sciences references are scraped from [PubMed](https://pubmed.ncbi.nlm.nih.gov/). A total of *26000 references* were scraped from the [search results]('https://www.ncbi.nlm.nih.gov/pubmed?term=Study%5BText%20Word%5D') for the *'study'* text word. Each manuscript reference includes the unique identifier PubMed ID, title, list of co-authors, free status, keywords and abstracts. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/scrape_PubMed.ipynb)

## Regression Analysis

**Poverty Prediction with Census Data**

This project utilizes data from the aggregate Census and Behavioral Risk Factor data from different counties in the United States from 2003-2018. A total of 9 datasets were downloaded from federal government websites and combined into a single dataset for analysis. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/census), [CLEANING](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/census_data_cleaning.ipynb), [ANALYSIS](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/census_regression.ipynb)

**MRI Oximetry Calibration with Bootstrapping**

This project utilizes the MRI T2 oximetry data from [this paper](https://pubmed.ncbi.nlm.nih.gov/33719133/). MRI data was acquired on 16 subjects. Linear regression was performed between the `A` and `B` coefficients with hematocrit to determine the oximetry calibration model for sickle cell disease subjects. Bootstrapping (regular and non-parametric) was performed to determine the standard errors and confidence intervals for the linear model coefficients. [ANALYSIS](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/calibration_bootstrap.ipynb)

## Classification Analysis

**Classification of Heart Disease**

This project utilizes the public UCI Machine Learning Dataset on Heart Disease. Most projects perform heart disease classification only on a single database from Cleveland Clinic; whereas this project combined 4 datasets from the Cleveland Clinic, Long Beach VA Hospital, and two hospitals from Switzerland and Hungary. Datasets can be downloaded from the [UCI Machine Learning website](https://archive.ics.uci.edu/ml/datasets/heart+disease). [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/heart_disease),  [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/heart_disease_classification.ipynb)

**Naive Bayes Classification of PubMed Manuscripts**

This project uses the PubMed dataset previously-scraped. The text abstract for each manuscript is used to classify the manuscript into the top 7 topics (such as cancer, inflammation, depression, etc.). [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/manuscripts_classification_naivebayes.ipynb)

## Clustering

**Clustering of U.S. Colleges**

This project clusters 1600 colleges into different groups using unsupervised clustering algorithms; data was previously scraped from College Factual. Colleges were grouped into three distinct clusters: private national universities, state-funded 4-year colleges, major-specific colleges (including health sciences, maritime science, music and design). Clustering was performed using free-tiered AWS t2.micro, and Tableau was used for visualization. [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/colleges_clustering.ipynb)

## Neural Network

**MRI Image Orientation Classification**

This project uses MRI T2-weighted image datasets acquired on 60 subjects. A convolutional neural network is trained to classify 2-dimensional images into three orientation categories: axial, sagittal and coronal. Due to HIPAA, the subject datasets are not made available publicly; an example dataset acquired on the author is available as illustration. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/mri_t2/CV_T2.nii), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/mri_orientation_classification.ipynb)

**MRI Lesion Segmentation**

This project uses MRI T2-weighted images and Faster Region-based CNN using IceVision library to identify bounding boxes of white matter lesions in the brain. Downstream application includes choosing a seed within these bounding boxes as input into a seed-based segmentation technique to generate masks for each lesion. Training data is not available publicly. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/lesions/), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/lesions_bbox.ipynb)

**Knowledge Graph Node Classification and Link Prediction**

This project aims to perform node classification and link prediction on a biomedical knowledge graph, containing relations between different types of entities, such as drugs and diseases. This work focuses on the Hetionet knowledge graph and performs node classification using Graph Convolutional Network (GCN) through the Deep Graph Library (DGL). Link prediction is also performed using GCN, in which low-dimensional embedding representations for each node are generated and an edge is scored by the dot product of the two nodes' embeddings. [NODE SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/graph_node_classification.ipynb), [LINK SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/graph_link_prediction.ipynb)

**PubMed Abstracts Text Generation**

This project utilizes the previously-scraped PubMed dataset. A character-level recurrent neural network, specifically an LSTM, receives a 100-character sequence input and outputs a following predicted sequence. The subsequent portion of this project explores word-level text generation, in which the LSTM receives 100-word sequence and predicts the next word. This implementation also includes an additional *attention* component as well as *beam search* for improving predictions. [SCRIPT CHARACTER-LEVEL](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/manuscripts_text_generation.ipynb), [SCRIPT WORD-LEVEL](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/manuscripts_text_generation_word.ipynb), [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed/abstracts_str.txt), [WEIGHTS](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed/weights/)

**Sentence Paraphraser**

This project uses BART transformer from the simpletransformers library to paraphrase sentences. The BART model was downloaded from huggingface.co and retrained using the training dataset. The paraphraser was tested on the test dataset and will be used for another downstream task in the future. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/paraphrase), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/sentence_paraphrase.ipynb)

## Data Visualization

**College Factual Data Visualization**

This project uses the CollegeFactual dataset and visualizes using Plotly package in Python. Dash package is used to create an interactive dashboard from this dataset. Note that somehow the .png and .gif images are NOT displaying correctly; I am working to fix this. The images can be viewed outside of Jupyter Notebook in the Data folder. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/college_factual/dashboard_png), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/colleges_dashboard.ipynb)

## R Code

**Mental Health in Tech**

This project uses the 2014 [OSMI](https://osmihelp.org/research) survey on Mental Health in the Tech industry [link](https://www.kaggle.com/osmi/mental-health-in-tech-survey). Instead of Python, this project uses **R** to clean and perform exploratory analysis on this dataset. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/mental_tech/survey.csv), [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/mental_health_tech.md)

