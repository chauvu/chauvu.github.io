# Data science portfolio by Chau Vu
This portfolio is a combination of notebooks I created for my data science projects.

## Web Scraping

**Scrape College Rankings from CollegeFactual.com**

Ranking list of American colleges was scraped from [College Factual](https://www.collegefactual.com/rankings/best-colleges/). Details about each college, such as academics, student diversity and tuition fees, are scraped as well. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/college_factual) | [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/scrape_CollegeFactual.ipynb)

**Scrape Manuscripts from PubMed.com**

Biomedical and life sciences references are scraped from [PubMed](https://pubmed.ncbi.nlm.nih.gov/). A total of *26000 references* were scraped from the [search results]('https://www.ncbi.nlm.nih.gov/pubmed?term=Study%5BText%20Word%5D') for the *'study'* text word. Each manuscript reference includes the unique identifier PubMed ID, title, list of co-authors, free status, keywords and abstracts. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed) | [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/scrape_PubMed.ipynb)

## Regression Analysis

**Poverty Prediction with Census Data**

This project utilizes data from the aggregate Census and Behavioral Risk Factor data from different counties in the United States from 2003-2018. A total of 9 datasets were downloaded from federal government websites and combined into a single dataset for analysis. [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/census) | [CLEANING](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/census_data_cleaning.ipynb) | [ANALYSIS](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/census_regression.ipynb)

## Classification Analysis

**Classification of Heart Disease**

This project utilizes the public UCI Machine Learning Dataset on Heart Disease. Most projects perform heart disease classification only on a single database from Cleveland Clinic; whereas this project combined 4 datasets from the Cleveland Clinic, Long Beach VA Hospital, and two hospitals from Switzerland and Hungary. Datasets can be downloaded from the [UCI Machine Learning website](https://archive.ics.uci.edu/ml/datasets/heart+disease). [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/heart_disease) | [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/heart_disease_classification.ipynb)

**Naive Bayes Classification of PubMed Manuscripts**

This project uses the PubMed dataset previously-scraped. The text abstract for each manuscript is used to classify the manuscript into the top 7 topics (such as cancer, inflammation, depression, etc.). [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed) | [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/manuscripts_classification_naivebayes.ipynb)

## Neural Network

**PubMed Abstracts Text Generation**

This project utilizes the previously-scraped PubMed dataset. A recurrent neural network, specifically an LSTM, receives a 100-character sequence input and outputs a following predicted sequence. [SCRIPT](https://github.com/chauvu/chauvu.github.io/blob/main/Notebooks/manuscripts_text_generation.ipynb) | [DATA](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed/abstracts_str.txt) | [WEIGHTS](https://github.com/chauvu/chauvu.github.io/tree/main/Data/pubmed/weights/)




