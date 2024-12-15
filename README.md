# Performing EDA on STRANGER THINGS dataset using Apache Spark

1. Open the course link provided at: https://courses.bigdatainrealworld.com/courses/enrolled/177985?authuser=0<br>
2. Register and go to course: Spark Starter Kit<br>
3. You'll receive email for "Spark for Windows" installation. Download from link.<br>
4. Select any dataset from Kaggle. I choose 'Stranger things Episode Ratings'. Link to dataset: https://www.kaggle.com/datasets/thedevastator/stranger-things-episode-ratings?resource=download<br>
5. Download Java for your system if you don't already have it installed. You can download it here: https://www.java.com/download/ie_manual.jsp<br>

## Table of Contents

- [Running Spark on your computer](#Running_Spark)
- [Load the Dataset in Spark Shell](#Load_Data)
- [Exploratory Data Analysis (EDA)](#eda)
- [Visualization](#visualization)

## A. Running Spark on your computer
1. Open command prompt on your PC and check the java version that got installed.If it displays an output, you have java installed.<br>
 ```bash
   java -version
```
2. Extract the Spark Starter Kit and make sure to have the files in your C:\ drive. Go back to the command prompt and type to change directory:<br>
 ```bash
   cd c:\spark-windows
```
 ```bash
   cd spark-2.1.1
```
 ```bash
   cd bin
```
3. Run the spark shell.<br>
```bash
   spark-shell
```
<img width="960" alt="image" src="https://github.com/user-attachments/assets/a1d4590a-ec73-4842-bbf7-1cfc2da9a8b7" />

## B. Load the Dataset in Spark Shell
1.Import necessary libraries <br>
```bash
import org.apache.spark.sql.SparkSession
```
2.Initialize SparkSession <br>
```bash
val spark = SparkSession.builder()
  .appName("StrangerThingsRatings")
  .master("local[*]")
  .getOrCreate()
  ```
<img width="585" alt="image" src="https://github.com/user-attachments/assets/fdb95e1c-cede-4642-8a8c-bf403180476a" />

3.Load the CSV file.<br>
```bash
val ratingsDF = spark.read
  .option("header", "true")  // Assumes the file has a header
  .option("inferSchema", "true")  // Automatically infer column types
  .csv("C:/data/Stranger_Things_Ratings.csv")
```
<img width="656" alt="image" src="https://github.com/user-attachments/assets/2d823a84-c336-41f2-b0eb-f21df4262b80" />

4.Show the first 10 rows just to make sure dataset is properly uploaded.<br>
```bash
ratingsDF.show(10)
```
<img width="649" alt="image" src="https://github.com/user-attachments/assets/86177212-98bf-492d-9ebe-a6c01a8ad5c6" />

## C. Exploratory Data Analysis (EDA)
1.Check the Schema. <br>
```bash
ratingsDF.printSchema()
```
<img width="233" alt="image" src="https://github.com/user-attachments/assets/044f5dcb-7f26-4add-81ff-48b065ebdc8e" />

2. Basic Statistics<br>
```bash
ratingsDF.describe().show()
```
<img width="959" alt="image" src="https://github.com/user-attachments/assets/630f8db6-e1c6-4b71-aba9-087934d6a62e" />

3.Finding null/missing values in each column.
```bash
import org.apache.spark.sql.functions._
ratingsDF.select(ratingsDF.columns.map(c => sum(col(c).isNull.cast("int")).alias(c)): _*).show()
```
<img width="517" alt="image" src="https://github.com/user-attachments/assets/a28fb4ab-94de-47d5-bc74-24d1e41b2615" />

4. To check the unique episodes:
```bash
ratingsDF.select("Episode").distinct().show()
ratingsDF.groupBy("Episode").count().show()
```
5. Perform grouping and calculate statistics for the "Episode" column: 'Average rating by Episode'
```bash
ratingsDF.groupBy("Episode")
         .agg(avg("Rating").alias("Avg_Rating"))
         .orderBy("Episode")
         .show()
```
Maximum and Minimum Rating per Episode
```bash
ratingsDF.groupBy("Episode")
         .agg(
           max("Rating").alias("Max_Rating"),
           min("Rating").alias("Min_Rating")
         )
         .orderBy("Episode")
         .show()
```
6. Check how the ratings are distributed. You can count how often each rating appears:
```bash
ratingsDF.groupBy("Rating").count().orderBy("Rating").show()
```
7. Filter rows based on certain conditions, e.g., episodes with ratings above a certain threshold:
```bash
ratingsDF.filter("Rating > 8.5").show()
```
8. Sort the dataset by "Rating" in descending order to see the top-rated episodes:
```bash
ratingsDF.orderBy(desc("Rating")).show()
```
10. You can create a new column based on existing data. For example:
```bash
ratingsDF.withColumn("Rating_Adjusted", col("Rating") * 1.1)
         .show()
```
## D. Visualizations
1. You can save a processed version of the DataFrame to CSV:
```bash
ratingsDF.write.option("header", "true").csv("C:/Processed_Stranger_Things.csv")
```
<img width="512" alt="image" src="https://github.com/user-attachments/assets/96bf662b-4241-4e66-9497-c863bdc13c44" />
<br>
### This is how we can perform EDA using Apache Spark on any dataset. Processed files from this setup have been uploaded to the repository.
