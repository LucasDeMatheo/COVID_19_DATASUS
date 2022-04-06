# COVID_19_DATASUS
This dataset was obtained from the SUS website - Brazilian Unified Health System. These are SUS records on COVI-19 in the state of Rio de Janeiro's hospitals.

# Objective: 
> Clean and structure the data for further analysis

In 2019, the spread of a coronavirus has begun which in 2020 received the status of a pandemic. At that time, many questions were raised about the disease spread, such as which symptoms were more likely to be manifested, severity, lethality among many others. In Brazil it was no different, we had many cases of the disease, which led researchers to race against time to understand more and more what could be done.

In this work, I had access to a Brazilian Ministry of Health dataset, published on a CSV file with 1,000,000 reported cases from admissions on Rio de Janeiro’s hospitals, between January 2020 and June 2021. In this dataset, several variables were collected for everyone, but how many of these are useful, and how many are correctly collected? Let's start this project by Cleaning the extracted data from https://covid.saude.gov.br/ . 
If it is no longer possible to access the original file in the link, it was uploaded to this repository as: data-rj-1.csv

**The code used for this process was uploaded as (DataPreprocessing - DataSUS - COVID19 - Rio de Janeiro.ipynb).**  
-  1 – The ID column served no purpose and was excluded. (No duplicated ID was found)  
-  2 – Birthday (dataNascimento) was not inserted and for this reason, it was excluded.  
-  3 – This dataset was labeled as from Rio de Janeiro, and for this reason, every other state found in the dataset was excluded. First integrity problem found. (Even if they came from other states, by hospital transfer, this information is not clear)  
-  4 – Every state has an ID number associated with them. All ID number was dropped. Every column with redundant information was excluded.   
-  5 – Every column with undefined values was excluded (Note: these are not a classic missing data problem, nor they are NaN numbers. Here, we face whole columns missing the information). Another integrity problem.  
-  6 – Unfortunately, a lot of information that could be relevant is not in the dataset.   

**Those were the summary of the first steps on Data Cleaning. Now many other processes will be applied.**   
  1 – All columns’ names left were translated to English.  
  2 – A missing data analysis was conducted for every column. Many contained at least 1 missing data. Some had more than 50% of the data missing.   
  3 – A individual analysis by columns hat to be conducted. Some were dropped, some were filled.  
  4 – A huge issue with patients’ age was detected. Some had more than 100 years, some much more (max = 390). Another integrity problem. Here we have two options, exclude those over 100 years or turn them to NaN and impose a value based on KNN algorithm. I chose to exclude, as we have a massive dataset. The other option can be tested. (204 records on this situation).  
  5 – Symptoms and Conditions were split into new columns, one column for each unique value found with them.   
  6 – A binary column with conditions was also generated (with or without previous health conditions).  
  7 – Every value was later translated to English.  
  8 – A new file was generated to be used in another notebook (EDA and Analytics)  

**56% of the original dataset remained after all procedures.**   
