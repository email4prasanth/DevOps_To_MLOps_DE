- Click on jobs & pipelines and click job, notebook, enter
```sh
Task name
Source
Path
compute
parmeters
```
- additional parameters
```sh
Schedules & Triggers
Tags
Job Notification (email)
Permissions
```
- create jobs for dim customer, products, price and facts for incremental data
- Run all individual file and later the pipeline.
- click on catalog volume click on **+** and upload to volume, upload files to volume, drag and drop the file, create volume give name and choose catalog & schema.
- Go to SQL editor and run the query `DESCRIBE TABLE fmcg.gold.fact_orders;`
```sh
COPY INTO fmcg.gold.fact_orders
FROM (
    SELECT
        date,
        product_code,
        customer_code,
        CAST(sold_quantity AS BIGINT) AS sold_quantity
    FROM '/Volumes/fmcg/gold/parent_incremental'
)
FILEFORMAT = CSV
FORMAT_OPTIONS (
    'header' = 'true'
);
```
- till 29:13:16