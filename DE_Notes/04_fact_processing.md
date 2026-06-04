### full load
1. import necessary libraries
2. Uitilities layer
3. widgets, base_path, landing_path, processed_path in s3
4. create dataframe add header, timestamp, metadata check the count and display
6. create bronze table in append mode
7. move the files from landing to processed in s3
8. apply transformation
    - remove null in order quantity
    - Clean customer_id → keep numeric, else set to 999999
    - Remove weekday name from the date text
    - Parse order_placement_date using multiple possible formats
    - Drop duplicates and convert product id to string
9. now check the max and min date.
10. Match and include product_code form fmcg.silver.products
11. now create a sivler_table = fmcg.silver.orders if not exists, else apply deltaTable to update
12. now create a gold table with all columns for child company ignoring sold_quantity child data is on daily level
13. merge with parent company make all dates to start of month, sum all the sold_quantity and rename
14. now create gold_parent_delta, check the data type of both dataframes gold_parent_delta, df_monthly allign them as per requirment.
- upto 2:02:13
### incremental load
1. accept a new data is coming and we need to append in child bronze layer and staging_bronze layer, 
- open fmcg.bronze.orders run and fmcg.bronze.staging_orders check the latest.
- In s3 move the files from landing to processed folder, and confirm the same
2. staging_bronze layer will undergo transfromation and create staging_silver layer and also append to silver layer, check 'fmcg.silver.orders' table.
- create fmcg.silver.staging_orders
3. staging_silver layer will append to sb_gold layer where the data is in daily basis.
- check 'fmcg.gold.sb_orders'
4. parent+child table gold orders need to update in montly basis.
- verify the column type of both child dataframe df_monthly_recalc and parent fmcg.gold.fact_orders
- upto 2:26:00