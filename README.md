![GitHub](https://img.shields.io/github/license/connectsquares/AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA?style=flat)
![GitHub top language](https://img.shields.io/github/languages/top/connectsquares/AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA?style=flat)
![GitHub language count](https://img.shields.io/github/languages/count/connectsquares/AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA?style=flat)
![GitHub last commit](https://img.shields.io/github/last-commit/connectsquares/AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA?style=flat)
![ViewCount](https://views.whatilearened.today/views/github/connectsquares/AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA.svg?cache=remove)


# AWS-data-pipeline-using-AMR-SPARK-GLUE-ATHENA
AWS EMR ETL job using Apache spark 

Folks, in this excercise, We are going to use PySpark to interact with the Spark cluster. PySpark allows you to write Spark applications using Python APIs.

ETL (Extract, Transform, Load) pipelines are foundational for modern data infrastructure, building them can be complex due to the myriad of tools and frameworks available. However, leveraging Amazon EMR (Elastic MapReduce) in conjunction with Apache Spark can significantly streamline this process, offering scalability, flexibility, and efficiency for both batch and real-time data processing.

## Best Practices for EMR + Spark ETL Pipelines
To ensure your ETL pipelines are efficient and maintainable:

Optimize Data Formats: Use columnar storage formats like Parquet or ORC to enhance performance.

Partitioning: Partition your data based on frequently queried columns to speed up data retrieval.

Resource Management: Leverage EMR's auto-scaling feature to adjust resources dynamically based on workload.

Code Modularization: Structure your Spark code into reusable modules for better maintainability.

Error Handling: Implement robust error handling and logging mechanisms to quickly identify and address issues.

Security: Ensure data is encrypted in transit and at rest. Use IAM roles and policies to control access to your data and resources.

[Amazon EMR](https://docs.aws.amazon.com/whitepapers/latest/big-data-analytics-options/amazon-emr.html) (Elastic MapReduce) is a cloud-based big data platform provided by AWS that simplifies the processing of vast amounts of data using open-source frameworks like Apache Spark, Hadoop, Hive, and Presto. It enables organizations to efficiently run large-scale distributed data processing jobs, interactive SQL queries, and machine learning applications. 

# Key Features of Amazon EMR
Managed Big Data Frameworks: EMR supports a variety of open-source tools, including Apache Spark, Hadoop, Hive, HBase, Flink, and Presto, allowing users to handle diverse workloads efficiently. 

Scalability: Clusters can be scaled up or down based on data processing needs, with support for auto-scaling policies to manage compute resources dynamically. 
Amazon Web Services, Inc.

Cost-Effectiveness: EMR integrates with EC2 Spot Instances, which can significantly reduce the costs of running large data workloads. 
Medium

Flexible Deployment Options:

EMR on EC2: Provides control over cluster configuration and supports long-running clusters.

EMR on EKS: Allows running Spark jobs on Amazon Elastic Kubernetes Service (EKS) without provisioning EMR clusters.

EMR Serverless: Enables running big data applications without managing clusters or servers. 

Integration with AWS Ecosystem: Seamlessly integrates with services like Amazon S3 for storage, Amazon RDS and DynamoDB for databases, AWS Glue for data cataloging, and Amazon SageMaker for machine learning workflows. 

EMR Studio: An integrated development environment (IDE) that provides a collaborative workspace for data scientists and engineers to develop, visualize, and debug data engineering and data science applications. 

[Apache Spark](https://spark.apache.org/docs/latest/) is an open-source, unified analytics engine designed for large-scale data processing. It offers high-level APIs in Java, Scala, Python, and R, and supports a wide range of workloads, including batch processing, interactive queries, real-time analytics, machine learning, and graph processing.

# Key Features of Apache Spark
In-Memory Computing: Spark's ability to perform computations in memory significantly boosts processing speed, making it up to 100 times faster than traditional MapReduce for certain applications. 

Unified Engine: Spark provides a comprehensive suite of libraries, including:

Spark SQL: For structured data processing using SQL queries.

Spark Streaming: For real-time data processing.

MLlib: A machine learning library offering various algorithms and utilities.

GraphX: For graph processing and analysis.

Language Support: Developers can write applications in multiple languages, including Java, Scala, Python, and R, providing flexibility and ease of integration.


## What you will learn

We are going to use [PySpark](https://spark.apache.org/docs/latest/api/python/) to interact with the Spark cluster. PySpark allows you to write Spark applications using Python APIs.

In this guide, you will:
- Create and set up an Amazon EMR cluster
- Submit a PySpark job on EMR
- Integrate Amazon EMR with Amazon S3
- Integrate S3 bucket with Amazon Athena

## Use cases

Big Data Analytics: Run large-scale data processing and what-if analysis using statistical algorithms and predictive models to uncover hidden patterns, correlations, market trends, and customer preferences.

ETL Pipelines: Extract data from various sources, transform it, and load it into data warehouses or other storage systems.

Real-Time Data Processing: Analyze events from streaming data sources in real-time to create long-running, highly available, and fault-tolerant streaming data pipelines.

Machine Learning Workflows: Analyze data using open-source ML frameworks such as Apache Spark MLlib, TensorFlow, and Apache MXNet.

# Follow steps to start!

## Architecture

Configure the data pipeline using the [AWS EMR](https://aws.amazon.com/emr/) cluster using Spark as distributed processing engine. [AWS S3](https://aws.amazon.com/s3/) is used for the storing data.

<img src="images/etl.png" width=""/>

## EMR Cluster

To start with EMR cluster configuration, we need to create the [Key Pair](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-key-pairs.html) to access the EMR cluster's master node.

Let's create the key-pair to downalod and save the .pem file.

1. Login to your AWS account, and navigate to the EC2 console, and click on **Key Pairs** option on the left side menu. click on `Create Key Pair` and save to download the .pem file.

![emr spark keypair](images/key-pair.png)

2. Let's create the **EMR cluster** by navigating to the resource search bar on the top and type 'EMR' to AWS EMR console and click on `Create Cluster`

![emr spark cluster](images/emr-spark.png)

3. Provide some `Cluster name` to your EMR cluster, and select the lastest emr version with:

   - **Latest release** of EMR under **Software configuration** section
   - **Spark: Spark 3.5.5 on Hadoop 3.4.1 YARN** under **Application** bundle,
  
4. Select the right **EC2 key pair** (which you created in the previous step) under the **Security and access** section

![emr spark cluster keypair](images/emr-key-pair.png)

Keep everything else as default and click on **Create cluster**

5. After initiating the creation of a Databricks cluster, it may take a few minutes to provision the necessary resources. Once the cluster is fully operational, its status will appear as "Waiting". This indicates that the cluster is active and ready to execute ETL jobs.

![emr ready](images/emr-ready.png)

## Create and Configuree an Amazon S3 bucket

Now we will create an Amazon S3 bucket and shall create two sub-folders within that, which will be used for store `RAW` and `CLEANSED` data

1. Navigate to the Amazon S3 console and click on **Create Bucket** with name **emr-sales**

![create S3 bucket](images/emr-sales-s3-bucket.png)

2. Once buckets is created, create two folders within as per below

   - `cleansed-data`
   - `raw-data`
  
![S3 bucket folders](images/sales-bucket-folders.png)


3. Upload the CSV file(dataset/SalesData.csv) in the bucket under the folder `raw-data`

![raw csv data](images/S3-csv-upload.png)

4. Add inbound rule to security group for the master node. add new rule with SSH to  `my ip`

![emr master sg](images/emr-master-sg.png)

![emr master sg inbound](images/emr-master-sg-inbound.png)

## Submit PySpark job

We are now ready to submit the PySpark job to create pipeline job as we have the source data to clean usign the spark.

1. Navigate to the EMR console, select the `EMR cluster` which you created in earlier and click on **Connect to the Master Node Using SSH**

![emr connect](images//emr-connect.png)

2. SSH to the EMR cluster's Master node from your terminal

![Emr connect ssh](images/emr-connect-ssh.png)

![Emr ssh](images/emr-vm-ssh.png)


3. Copy the PySpark code [`etl-job.py`](/etl-job.py) and save it in the `Master Node` and make the following changes and save the file:

   - `S3_SOURCE_DATA_PATH` = 's3://<YOUR_BUCKET_NAME>/raw-data/SalesData.csv'
   - `S3_DEST_DATA_PATH` = 's3://<YOUR_BUCKET_NAME>/cleaned-raw/'
  
```Python

from pyspark.sql import SparkSession
from pyspark.sql import functions as F

S3_SOURCE_DATA_PATH = 's3://emr-sales/raw-data/SalesData.csv'
S3_DEST_DATA_PATH = 's3://emr-sales/cleansed-data/'


def main():
    spark = SparkSession.builder.appName("ETL sales App").getOrCreate()
    spark.sparkContext.setLogLevel('ERROR')

    # Spark Dataframe (Raw)- Transformation 
    df = spark.read.option("Header", True).option("InferSchema", True).csv(S3_SOURCE_DATA_PATH)
    
    replacements = {c:c.replace(' ','_') for c in df.columns if ' ' in c}
    selected_df = df.select([F.col(c).alias(replacements.get(c, c)) for c in df.columns])

    print('Total no. of records in the source data set is:%s' %selected_df.count())
    selected_df.write.mode('overwrite').parquet(S3_DEST_DATA_PATH)
    print('The cleaned data was successfully uploaded to S3:%s' %S3_DEST_DATA_PATH)


if __name__ == '__main__':
    main()

```

5. Submit the `PySpark job`

```bash
sudo spark-submit etl-job.py
```

![emr etl job](images/emr-etl-job.png)

6. As job completes, validate the S3 bucket under the folder `cleansed-data` to see the new transformed and processed data in `parquet` format

![s3 cleansed data](images/S3-cleansed-data.png)

## Validating the output using Amazon Athena

Now, the `cleansed` data is available in Amazon S3 in the form of `parquet` format, but to make it more consumable for data analysts or data scientists, and it would require to enable querying the data through SQL by making it available as a database table.

To make that integration, we can follow a two-step approach:

1. We need to run the Glue crawler to create a AWS Glue Data Catalog table on top of the S3 data.
2. Once that is done, we can run a query in Amazon Athena to validate the output

### Creating an AWS Glue Data Catalog

1. Navigate to the AWS Glue crawler console and click on **Create Crawler**

![glue crawlers](images/Glue-crawlers.png)

2. Provide name for crawler

![glue crawlers setup](images/Glue-crawlers-setup.png)

3. Add the **data source** as S3 bucket where you have your cleansed and processed data (`s3://emr-sales/cleansed-raw`)

![glue crawlers setup source](images/Glue-crawlers-setup-source.png)

4. Attach the **IAM role** 

![glue crawlers setup iam](images/Glue-crawlers-setup-iam.png)

5. Create a **database** by clicking on **Add database** and select the same from dropdown menu (`sales-db`)

![glue crawlers setup db](images/Glue-crawlers-setup-db.png)

6. Crawlers schedule can be on-demand. Review and verify all the details and click on **Create crawler**

![glue crawlers setup ondemand](images/Glue-crawlers-setup-ondemand.png)

7. Once the crawler is created, select the crawler and click on **Run**

![glue crawlers setup run](images/Glue-crawlers-setup-run.png)


Now that we have the Glue Data Catalog table created, we can navigate to Amazon Athena to query the data using SQL.

1. Navigate to Amazon Athena, click on database and select `emr-salesdb`

```sql
SELECT * FROM "emr-salesdb"."cleansed_data" limit 5;
```

![athena select query](images/Athena-select-query.png)

2. 2. Now, you can perform other SQL queries to analyze the data. For example, if we would like to know the `forcast_monthly_revenue` for each `region per segment wise`, we can run this:

```sql
SELECT
    region,
    segment,
    SUM(forecasted_monthly_revenue) as forcast_monthly_revenue
FROM "emr-salesdb"."cleansed_data"
GROUP BY segment, region;

```

![athena select group query](images/Athena-select-group-query.png)

## Conclusion

Good Work! This is the completion of the Creating an ETL Pipeline with Amazon EMR and Apache Spark tutorial. You can remove the resources by following the next steps section below.

## Clean up

Now that you’ve finished this exercise, you can delete all the following resources to avoid incurring unexpected costs

- Delete the **EMR Cluster**

- Delete the **Amazon S3 bucket**

```bash
aws s3 rb s3://emr-sales --force
```



