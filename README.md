# COVID_19_DATASUS
This dataset was obtained from the SUS website - Brazilian Unified Health System. These are SUS records on COVI-19 in the state of Rio de Janeiro's hospitals.

# Objective: 
> Clean and structure the data for further analysis

In 2019, the spread of a coronavirus has begun which in 2020 received the status of a pandemic. At that time, many questions were raised about the disease spread, such as which symptoms were more likely to be manifested, severity, lethality among many others. In Brazil it was no different, we had many cases of the disease, which led researchers to race against time to understand more and more what could be done.

In this work, I had access to a Brazilian Ministry of Health dataset, published on a CSV file with 1,000,000 reported cases from admissions on Rio de Janeiro’s hospitals, between January 2020 and June 2021. In this dataset, several variables were collected for everyone, but how many of these are useful, and how many are correctly collected? Let's start this project by Cleaning the extracted data from https://covid.saude.gov.br/ . 
If it is no longer possible to access the original file in the link, it was uploaded to this repository as: data-rj-1.csv (https://drive.google.com/drive/folders/1CHaj5hqv3xd8p2J13YYjsr6xUnDvD8EP?usp=sharing)

**The code used for this process was uploaded as (DataPreprocessing - DataSUS - COVID19 - Rio de Janeiro.ipynb).**  
-  1 – The ID column served no purpose and was excluded. (No duplicated ID was found)  
-  2 – Birthday (dataNascimento) was not inserted and for this reason, it was excluded.  
-  3 – This dataset was labeled as from Rio de Janeiro, and for this reason, every other state found in the dataset was excluded. First integrity problem found. (Even if they came from other states, by hospital transfer, this information is not clear)  
-  4 – Every state has an ID number associated with them. All ID number was dropped. Every column with redundant information was excluded.   
-  5 – Every column with undefined values was excluded (Note: these are not a classic missing data problem, nor they are NaN numbers. Here, we face whole columns missing the information). Another integrity problem.  
-  6 – Unfortunately, a lot of information that could be relevant is not in the dataset.   

**Those were the summary of the first steps on Data Cleaning. Now many other processes will be applied.**   
-  1 – All columns’ names left were translated to English.  
-  2 – A missing data analysis was conducted for every column. Many contained at least 1 missing data. Some had more than 50% of the data missing.   
-  3 – A individual analysis by columns hat to be conducted. Some were dropped, some were filled.  
-  4 – A huge issue with patients’ age was detected. Some had more than 100 years, some much more (max = 390). Another integrity problem. Here we have two options, exclude those over 100 years or turn them to NaN and impose a value based on KNN algorithm. I chose to exclude, as we have a massive dataset. The other option can be tested. (204 records on this situation).  
-  5 – Symptoms and Conditions were split into new columns, one column for each unique value found with them.   
-  6 – A binary column with conditions was also generated (with or without previous health conditions).  
-  7 – Every value was later translated to English.  
-  8 – A new file was generated to be used in another notebook (EDA and Analytics)  

**56% of the original dataset remained after all procedures. - DataSUS_PreProcessed.csv**   

# EDA and First Insights

After having cleaned up the main issues found in the DataSet, let's start looking deeper into the data, finding more abnormalities or inconsistencies if they still exist, and getting the first observations and insights from the data contained therein. The DataSet used here is the same that was generated in the previous data cleaning step.   
The first important aspect of this DataSet is that there is no death outcome. The only outcome herein is the result of COVID-19 tests.  

An important assessment is to determine the balance of the data, as this has implications for the hypotheses raised and causes bias in machine learning algorithms. Here, it was observed that 38% of the results returned positive. Most of those tested were not identified with COVID-19. The data are from the beginning of the pandemic in Rio de Janeiro, which can raise hypotheses such as if the tests were handled widely in the population and/or if the methods used were adequate. We have information about the symptoms of the people tested and the types of tests, so we can test these hypotheses further.  

But first, let's look at how the results are distributed by the sample people’s age.  

![Imagem1](https://user-images.githubusercontent.com/10830272/164477460-219e839e-7533-40fa-9ba6-8efa9d658db5.png)

Apparently, there is a gap between positives and negatives that decreases with the aging of the population (correlation = -0.68).  

![Imagem2](https://user-images.githubusercontent.com/10830272/164477570-60f3f174-91a6-4b53-848d-3f602e4bd8a1.png)

Elderly people seek hospitals when they have evidence of the disease or are they more susceptible to it?  
As for the type of test used, we know that the type should not influence the result, but each test has its specification and accuracy, and, they have different operating protocols. In this case, an evaluation of the 3 main protocols used was performed.  

![Imagem3](https://user-images.githubusercontent.com/10830272/164477807-0b5245d0-612e-4ec4-a2ac-be8748e0734a.png)

Drawing attention to the Antigen test, which differs from the Antibody and RT-PCR tests. RT-PCR identified more positive cases. To better understand this, a time analysis can help.  

![Imagem4](https://user-images.githubusercontent.com/10830272/164477913-01401eaa-54f5-40fe-b38e-bf2645e412be.png)

The Antigen tests were applied at the end of the time series under analysis, with RT-PCR being applied more than the other two during almost the entire spectrum of the second wave recorded. This information raises some suspicion about Antigen Tests.  

![Imagem5](https://user-images.githubusercontent.com/10830272/164478031-2e45e060-7c77-4a6d-8b6f-ebaf9c595400.png)

By recalculating the proportions over the end of the time series, it was possible to observe that Antigen and Antibody converged to a similar ratio, while RT-PCR, once again, is highlighted. RT-PCR, with all the information we already have about this test, proves to be better at discriminating between positive and negative tests.  
Looking at the timeline again, one fact caught my attention.  
- There are two well-highlighted waves of tests and well correlated (97%)  
- Tests that return negative are almost always above positives  
- There is no constant baseline for negatives  
A non-constant negative baseline rises suspicious on a second factor not evidenced yet. (Are the tests or it handling that lack in discriminating COVID? Or is there an external factor that leads people to seek the hospital?)  

![Imagem6](https://user-images.githubusercontent.com/10830272/164478177-f50e46dc-cfd7-4f1e-904f-1d979c981726.png)

When analyzing the main symptoms, olfactory and gustatory disorders were the only symptoms that were more present in positive than negative cases (between them, they have a correlation of 72%). Using the Positive/Negative ratio as a cutting base, fever, dyspnea, cough, and headache are candidates for classifier symptoms of COVID. On the other hand, sore throat and runny nose are better to not associate with COVID. Asymptomatic are the most likely to be Negative cases.  

![Imagem7](https://user-images.githubusercontent.com/10830272/164478300-c561dfb4-6003-4d03-9ee5-c857ecb7c2f1.png)

Finally, attributes engineering was performed to create a column that shows the time elapsed between the notification of cases and the onset of symptoms, a variable that can influence the test result. Here it was shown that the more time passes, the more the chance of being a positive case.  

![Imagem8](https://user-images.githubusercontent.com/10830272/164478415-b5b3e676-0d46-4048-976a-4b5fa8715bfe.png)

Unfortunately, once again, discrepancies and integrity issues appeared. In summary about data integrity, much needs to be improved, this dataset will hardly be a good way to train an M.L. algorithm.  

A new file was generated with the DataSet modifications (DataSUS_PreProcessed_For_ML.csv)

# Final considerations

> A lot of data was incorrect, incomplete, lacking integrity. They needed strong cleaning, raising suspicions about the usability of the data to raise questions.  
> Some doubts and suspicions could not be evaluated with the DataSet, the incompleteness of the database hampered some analyses. Possibly it will be necessary to look for data sources that complete them. But in the face of so many structural problems, is this the best way?  









