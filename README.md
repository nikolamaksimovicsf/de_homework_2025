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

