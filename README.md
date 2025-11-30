# My DE Project

This project is created in order to present answers to the questions asked in the assignment and to show the thought process behind the calculation of these results.

The project is split into two main scripts:
1. _preparation notebook_ - data imports, cleaning, and joining into one enriched transaction table
2. _presentation notebook_ - data answers and steps that produced them


In order to do this assignment, I am using _Databricks free edition_ [link](https://docs.databricks.com/aws/en/getting-started/free-edition).

Limitations of using this version (that I noticed during development) are:
* Cluster is not configurable, and only the smallest (2X Small) serverless compute is available to be used with very limited memory
* I am locked out of using/accessing DBFS storage, so directly importing files that would be used in calculations is not possible, and creation of permanent or global (temp) views is also not possible
* Creation of secret scopes is not possible
* Any direct spark configurations are not possible

## Preparation:

Since files (_merchants.csv_ and _historical_transactions.parquet_) needed for the assignment cannot be uploaded to internal storage, and I don't trust process of importing files as metastore tables (since I don't have any control over parsing logic during import process), I decided to fetch files directly from the source each time this notebook is run, until data is suficiantly cleaned for later use, and new data is saved in metastore.

Cleaning focus is done mainly on merchant data since this data is complementary to the transactions table, and duplications and inconsistent values in this dimension can cause issues later for the transaction table once both are joined.
Cleaning consists of the following steps:
1. finding duplicate merchant _id's and unifying them into one value (all rows with the same value are persisted, and others are removed since there is no way to determine which value is correct)
2. replacing nulls with 'Unknown' as category
3. fixing merchant_name (if value is null, it should copy merhcnant_id value, if this is still null, then 'Unknown' value is applied)
4. creating new _calc_ columns that unify inconsistent values for the same _merchant_id_
5. parsing purchase_date into separate columns that are needed later in analysis (month, year, year_month and hour)
6. dropping (most) columns not needed for further analysis - this is needed to lower the file size because spark was having issues saving this table to its catalog

One thing to note here is that since there is no dbs workspace access to secrets, I created text widgets that are used to hide direct URL strings of the files used.
These URL strings for both files will be sent in an email, together with this github project.

## Presentation:

This notebook is cleaner and more prepared to display answers to the asked questions, along with the steps I took to calculate them.
Since dbs notebook cell outputs can't be displayed here, I will also display answers here (with attached results).

### question 1: Generate the top 5 merchants by purchase_amount for each month in the dataset for each city in the dataset?
#### answer 1: see attached file for results 
[q1_answer.csv](https://github.com/user-attachments/files/23841296/q1_answer.csv)



### question 2: What is the average sale amount (purchase_amount) of each merchant in each state? 
#### answer 2: see attached file for results 
[q2_answer.csv](https://github.com/user-attachments/files/23841303/q2_answer.csv)

### question 3: Identify the top 3 hours where the largest amount of sales (purchase_amount) are recorded for each product category (category). 
#### answer 3: see attached file for results 
[q3_answer.csv](https://github.com/user-attachments/files/23841335/q3_answer.csv)

### question 4: In which cities are the most popular merchants located? Is there a correlation between the location (city_id) and the categories (category) the merchant sells?
#### answer 4.1: most popualar cities are _69_, _1_, and _333_. See attached file for more detailed results 
[q41_answer.csv](https://github.com/user-attachments/files/23841341/q41_answer.csv)

#### answer 4.2: looking at (attached) contingency table, there doesn't seem to be any obvious correlation between location and categories 
[q42_answer.csv](https://github.com/user-attachments/files/23841342/q42_answer.csv)

### A new merchant is coming in to do business and you have been assigned to give advice based strictly on the historical transactions.
### question 5a: Which cities would you advise them to focus on and why?
#### answer 5a: looking at results, it seems that city 1 and  city 69 have the biggest revenue per merchant, so I would focus on these.
[q5a_answer.csv](https://github.com/user-attachments/files/23841336/q5a_answer.csv)

### question 5b: Which categories would you recommend they sell?
#### answer 5b: looking at the results, its best to focus on category A if _city 69_ is selected, and category B if _city 1_ is selected, since they produce the biggest revenue and have the highest transaction frequency
[q5b69_answer.csv](https://github.com/user-attachments/files/23841338/q5b69_answer.csv)
[q5b1_answer.csv](https://github.com/user-attachments/files/23841337/q5b1_answer.csv)

### question 5c: Are there particular periods (months) that have interesting sales behaviors?
#### answer 5c: looking at the results, _December_ was a month with the biggest sales. This is expected since this is famously the biggest shopping month in the whole world. One interesting thing here is strong sales increase starting in _July / August_, which can't be explained easily. My expectation are that the sales would be lower in these months since people are traveling and spending less?
[q5c_answer.csv](https://github.com/user-attachments/files/23841339/q5c_answer.csv)

### question 5d: What hours would you recommend they open and close for the day?
#### answer 5d: looking at the results, for weekdays starting from 08:00 and up until 23:00 we have lots of traffic, and for the weekends starting from 10:00 and then until 20:00
[q5d_answer.csv](https://github.com/user-attachments/files/23841340/q5d_answer.csv)

### question 5e: Would you recommend accepting payments in installments? Assume a credit default rate of 22.9% per month. For this question, consider the “installments” header in the historical transactions and the impact it may have, if any, on merchant sales (merchant sales in terms of purchase_amounts). We are making a simplistic assumption that 25% of sales is gross profit to merchants, there are equal installments and everyone who defaulted did so after making half payment.
#### answer 5e: looking at the results, we should NOT accept installments, and it's because we are expected to take a profit loss on average, and also there doesn't seem to be any noticeable improvements in sales when installments are used as an option: _average cash order: 20105_ vs _average installment order: 20100_

Average cash order: _20105.8_

Average installment order: _20100.1_


Avg installment duration: _3.7 months_

Expected profit (Cash): _5026.5_

Expected profit (Installments): _-1181.1_
