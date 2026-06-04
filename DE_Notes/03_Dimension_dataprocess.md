- create a new folder with **2_data_dimension_processing**, and create a note for 1_customer_data_processing
- The main aim is child raw data should allign properly with parent gold layer.
1. import require python library
```sh
from pyspark.sql import functions as F
from delta.tables import DeltaTable 
```
2. define schema bronze, silver and gold, the best practice is create utilitis and call the schema
- open 1_setup and create a notebook with utilies, or create a ipynb file and import the file.
- copy the full path and point it to the 2_data_dimension_processing file

3. creating widgets, this will be prompt the controls on the top
```sh
dbutils.widgets.text("name", "default_value")

catalog = dbutils.widgets.text("catalog", "fmcg", "Catalog")
data_source = dbutils.widgets.text("data_source", "customers")

# s3 bucket path
data_path = s3://dataengineersportsbar/{data_source}/*.csv
```
4. Now we need to create a data frame from the base path
```sh
df =  spark.
```
5. we need to make this into proper header format which helpfull in linage and debugging
```sh
spark.read.format("csv").load(base_path).select("_metadata.*").show()

df = (
    spark.read
    .format("csv")
    .option("header", True)
    .option("inferSchema", True)
    .load(base_path)
    .withColumn("readtime", F.current_timestamp())
    .select("*", "_metadata.*")
)
display(df.limit(5))

df.printSchema()
```
- Now write the raw data to bronze layer
```sh
df.write \
    .mode("overwrite") \
    .format("delta") \
    .option("delta.enableChangeDataFeed", "true") \
    .saveAsTable(f"{catalog}.{bronze_schema}.{data_source}")
```
- ChangeDataFeed is usefull for audit and logs.
6.  Move to silver processing
- Find the flaws in bronze data
1. customer_name contains duplicates, leading and trailing space, different case letters.
2. Find distint city names replace incorrect names using `isin` and null
3. check the datatype of the column

- Once it is done compare the column of gold Company A dim_customer with silver Company B  
```sh
print(df_silver.columns)
['customer_id', 'customer_name', 'city', 'read_timestamp', 'file_name', 'file_size']

column_names = [
    row.col_name
    for row in spark.sql("DESCRIBE fmcg.gold.dim_customers")
                    .select("col_name")
                    .filter("col_name NOT LIKE '#%'")
                    .collect()
]

print(column_names)
['customer_code', 'customer', 'market', 'platform', 'channel']
```
1. Need to modify the company B that allign with Parent company A
    - customer_id ---> customer_code
    - customer_name+city ---> customer
2. Add extra columns with static values
    - 'market : india', 
    - 'platform: Sports Bar',
    - 'channel: Acquisition'.
- Now the silver layer is ready we need to move to gold layer

```sh
df_gold = df_silver.select(['customer_code', 'customer', 'market', 'platform', 'channel'])
df_gold.write \
    .mode("overwrite") \
    .format("delta") \
    .option("delta.enableChangeDataFeed", True) \
    .saveAsTable(f"{catalog}.{gold_schema}.sb_dim_{data_source}")
```
- Now we need to merge parent company gold table **dim_customers** and child company gold table **sb_dim_customers** use upsert
```sh
delta_table =  DeltaTable.forName(spark,"fmcg.gold.dim_customers")
df_child_customer = spark.table("fmcg.gold.sb_dim_customers").select("customer_code", "customer", "market", "platform", "channel")

(
    delta_table.alias("t")
    .merge(
        source=df_child_customer.alias("s"),
        condition="t.customer_code = s.customer_code"
    )
    .whenMatchedUpdateAll()
    .whenNotMatchedInsertAll()
    .execute()
)

%sql
select * from fmcg.gold.dim_customers
```
### Now the transformation is going to apply on products file
- once the merge is done, open dim_products under sample data **select all records** run the query
```sh
SELECT * FROM `fmcg`.`gold`.`dim_products`;
```
### Now the transformation is going to apply on gross file
1. product_id should rename to product_code
2. allign month field, use distint() and coalesce()
3. gross_price replaced to price_inr and replace negative and zero values
4. join product code taken from silver.products
5. write in silver, take desired filed and move to gold layer.
6. Child layer contains several months with different values, so use **is_zero**, window and rank then merge with parent gold layer.
- this is upto 1:40:13
- topics need to see 
    1. regular expression
    2. window sql

    
