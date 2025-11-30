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

Since files (_merchants.csv_ and _historical_transactions.parquet_) needed for the assignment cannot be uploaded to internal storage, and I dont trust process of importing files as metastore tables (since I don't have any control over parsing logic during import process), I decided to fetch files directly from the source, each time this notebook is run, until data is suficiantly cleaned for later use, and new data is saved in metastore.

Cleaning focus is done mainly on merchant data since this data is complementary to the transactions table, and duplications and inconsistent values in this dimension can cause issues later for the transaction table once both are joined.
Cleaning consists of the following steps:
1. finding duplicate merchant _id's and unifying them into one value (all rows with the same value are persisted, and others are removed since there is no way to determine which value is correct)
2. replacing nulls with 'Unknown' as category
3. fixing merchant_name (if value is null, it should copy merhcnant_id value, if this is still null, then 'Unknown' value is applied)
4. creating new _calc_ columns that unify inconsistent values for the same _merchant_id_
5. parsing purchase_date into separate columns that are needed later in analysis (month, year, year_month and hour)
6. dropping (most) columns not needed for further analysis - this is needed to lower file size because spark was having issues saving this table to its catalog

One thing to note here is that since there is no dbs workspace access to secrets, I created text widgets that are used to hide direct URL strings of the files used.
These URL strings for both files will be sent in an email, together with this github project.
