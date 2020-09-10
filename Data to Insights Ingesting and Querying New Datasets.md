
# LAB: Data to Insights: Ingesting and Querying New Datasets

## Objectives:

In this lab, you will learn how to perform the following tasks:
-	Ingest Data from Google Cloud Storage into Google BigQuery
-   Query from a CSV in Cloud Storage directly as an External Data Source

##  Ingesting Data from Google Cloud Storage

### Steps:

1. Select the Project you choose to work with.
	```
	gcloud config set project <YOUR_PROJECT_ID>
	```
	
2. Download this CSV [NAICS_digit_2017_codes.csv](https://storage.googleapis.com/data-insights-course/labs/lab5-ingesting-and-querying/NAICS_digit_2017_codes.csv) into your current directory.
	```
	wget https://storage.googleapis.com/data-insights-course/labs/lab5-ingesting-and-querying/NAICS_digit_2017_codes.csv
	```
	
3. Create a Bucket and pick a **Unique Name**, store your  data in a **Multi-region(us)** with a **Standard**  storage class.
	```
	gsutil mb -c standard -l us gs://<UNIQUE_BUCKET_NAME>
	```
	
4. Confirm if the Bucket has been created. You should a Bucket with Unique name you choose.
	```
	gsutil ls
	```
	
5. Copy the CSV file you downloaded from Step 2 and upload it to the bucket.
	```
	gsutil cp NAICS_digit_2017_codes.csv gs://<UNIQUE_BUCKET_NAME>
	```
	
6. Check the bucket for the file NAICS_digit_2017_codes.csv you just copied.
	```
	gsutil ls gs://<UNIQUE_BUCKET_NAME>
	```

## Ingesting a CSV into a Google BigQuery Table

### Steps:
1. Create a dataset named `irs_990`in the data location `US`
	```
	bq mk irs_990
	bq --location=US mk -d irs_990
	```
2. Create a table named `naics_digit_2017_codes`
	```
	bq mk -t irs_990.naics_digit_2017_codes
	```
3. Create the `naics_digit_2017_codes` table from the bucket you created earlier and set the schema to Auto Detect . 
	Replace **<UNIQUE_BUCKET_NAME>**  with your buckets name.
	```
	bq load --autodetect --source_format=CSV irs_990.naics_digit_2017_codes gs://<UNIQUE_BUCKET_NAME>/NAICS_digit_2017_codes.csv
	```	
	#### Reading a CSV as an External Data Source in Google BigQuery Table
	Instead of ingesting and storing the CSV data table in Google BigQuery, you decide you want to query the underlying data source directly.

	The process is essentially the same as before except for changing the Table Type
	#### Steps:
	1. Create a table named `irs990_code_lookup` from an external source ( Google Cloud Storage) `gs://data-insights-course/labs/lab5-ingesting-and-querying/irs990_code_lookup.csv`. 
	Set the **Header rows to skip** to 1.
	Populate the **Schema** as follows:

		|    Name            |Type                          |Mode                         |
		|----------------|-------------------------------|-----------------------------|
		|        irs_990_field |STRING      |NULLABLE           |
		|      code          |STRING      |NULLABLE          	|
		|       description   |STRING		|NULLABLE			|

	```
	# Creates an empty tablw with the specified Schema
	
	bq mk -t irs_990.irs990_code_lookup irs_990_field:STRING,code:STRING,description:STRING
	```
	```
	# Loads the empty table from an external source
	
	bq load --noautodetect --skip_leading_rows=1 --source_format=CSV irs_990.irs990_code_lookup gs://data-insights-course/labs/lab5-ingesting-and-querying/irs990_code_lookup.csv irs_990_field:STRING,code:STRING,description:STRING
	```
4. Run the following query and change `Project-name` to your own.
	```
	bq --location=US query --use_legacy_sql=false 'SELECT irs_990_field, code, description FROM `ethereal-anthem-286107.irs_990.irs990_code_lookup` # change WHERE irs_990_field IN ('elf','subcd')'
	```
5. **Insights**: Read through the query results.

	##### What does the field  `elf`  mean?
	`elf` denotes how the return was filed: E for Electronic, P for Paper.

	##### Are the subsection (subcd) codes unique?
	No, they are not. Code 3 is used multiple times to denote 8 possible 				different Organization types (Charitable Corporation, Educational 	Organization, etc..). This insight will become particularly important when we use this as a lookup value for our individual filings. We will learn how to handle this in our upcoming labs.
		
	**Performance Pitfall:** Creating and querying from External Data Sources directly (e.g. CSVs stored on Google Cloud Storage) has performance impacts as Google BigQuery has less control over data outside of its fully-managed data warehouse.

6. Exit the console.

	```
	exit
	```

## Congratulations!

You have completed the second part of the  **BigQuery Data Ingestion**  lab.

### Learning Review

-   Google BigQuery supports ingesting data from many sources. Popular ones are Google Cloud Storage, CSV, JSON, ARVO, Cloud BigTable and more.
    
-   You can query External Data Sources directly but there are limitations (particularly around performance)
    
-   Auto-detecting the data schema when Creating a Table may lead to fields you did not want to include in the dataset. Consider manually spelling out the schema for more control.
    

### **References**

-   [Loading Data from Cloud Storage](https://cloud.google.com/bigquery/docs/loading-data-cloud-storage)
-   [Querying External Data Sources](https://cloud.google.com/bigquery/external-data-sources)
