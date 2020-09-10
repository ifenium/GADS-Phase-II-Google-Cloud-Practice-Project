
# LAB: Data to Insights: Unioning and Joining Datasets

## Objectives:

In this lab, you will learn how to perform the following tasks:

-   Practice Unioning and Joining Datasets
-   Practice Joining Tables
-   Practicing Working with NULLs

## Practice Unioning and Joining Datasets

### Steps:

1. Select the Project you choose to work with.
	```
	gcloud config set project <YOUR_PROJECT_ID>
	```
2. Create a dataset
	```
	bq mk <YOUR_DATASET_NAME>
	```
3. Write a Query that will count the number of tax filings by calendar year for all IRS Form 990 filings 
**Hint:** You will need to use Table Wildcards `*` with `_TABLE_SUFFIX`.

	```
	bq query --use_legacy_sql=false ' SELECT COUNT(*) as number_of_filings, _TABLE_SUFFIX AS year_filed FROM `bigquery-public-data.irs_990.irs_990_*` GROUP BY year_filed ORDER BY year_filed DESC'
	```
4. **Modify** the query you just wrote to only include the IRS tables with the following format: `irs_990_YYYY` (i.e. filter out pf, ez, ein).

	```
	bq --location=US query --use_legacy_sql=false 'SELECT   COUNT(*) as number_of_filings,   CONCAT("2",_TABLE_SUFFIX) AS year_filed FROM `bigquery-public-data.irs_990.irs_990_2*` GROUP BY year_filed ORDER BY year_filed DESC'
	```
5. Lastly, modify your query to only include tax filings from tables on or after 2013. Also include average  `totrevenue`  and average  `totfuncexpns`  as additional metrics.
**Hint:** Consider using  `_TABLE_SUFFIX`  in a filter.

	```
	bq --location=US query --use_legacy_sql=false 'SELECT CONCAT("20",_TABLE_SUFFIX) AS year_filed, COUNT(ein) AS nonprofit_count, AVG(totrevenue) AS avg_revenue, 		AVG(totfuncexpns) AS avg_expenses FROM `bigquery-public-data.irs_990.irs_990_20*` WHERE _TABLE_SUFFIX >= "13" GROUP BY year_filed ORDER BY year_filed DESC'
	```

## Practice Joining Tables

### Steps:
1. Find the Org Names of all EINs for 2015 with some revenue or expenses. You will need to join tax filing table data with the organization details table.
	```
	bq query --use_legacy_sql=false ' SELECT tax.ein AS tax_ein, org.ein AS org_ein, org.name, tax.totrevenue, tax.totfuncexpns FROM `bigquery-public-data.irs_990.irs_990_2015` AS tax JOIN `bigquery-public-data.irs_990.irs_990_ein` AS org ON tax.ein = org.ein WHERE tax.totrevenue + tax.totfuncexpns >  0 LIMIT 100;'
	```

## Practicing Working with NULLs
### Steps:
1. Write a query to find where tax records exist for 2015 but no corresponding Org Name.
	```
	bq query --use_legacy_sql=false ' SELECT tax.ein AS tax_ein, org.ein AS org_ein, org.name, tax.totrevenue, tax.totfuncexpns FROM `bigquery-public-data.irs_990.irs_990_2015` tax FULL JOIN `bigquery-public-data.irs_990.irs_990_ein` org ON tax.ein = org.ein WHERE org.ein IS NULL'
	```
2. How many tax filings occurred in 2015 but have no corresponding record in the Organization Details table? 

	**Answer:** 14,123 (your answer may be higher as new EIN numbers get added to the public base table)
	
3. Exit the console.

	```
	exit
	```
## Congratulations!

You have completed the UNIONING and JOINING Datasets lab.

### Learning Review

-	Use Union Wildcards to treat multiple tables as a single group
-	Use _TABLE_SUFFIX to filter wildcard tables and create calculated fields with the table name
-	FULL JOINs (also called FULL OUTER JOINs) include all records from each joined table regardless of whether there are matches on the join key
-	Having non-unique join keys can result in an unintentional CROSS JOIN (more output rows than input rows) which should be avoided
-	Use COUNT() and GROUP BY to determine if a key field is indeed unique
