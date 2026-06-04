- Suppose company A (Parent) and company B(child) are going to merge
    1. Aggreagting the analytics in single dashboard and should be reilable
    2. The learning curve should be simple
    3. Should be scalable 
- Company A contains OLTP with data enginnering system using databricks bronze(raw), silver(Cleaned & Validated), gold (Business Reports & Analytics), with databrick dashboard and genie
- Company B contains OLTP but directly build on excel sheet.

### Idea:
- Store data of company B in S3 bucket and use data engineering technique (bronze, silver, gold) and directly merge to Company A gold Analytics.
- The live is going on Nov 30, 2025. so the data is split into full_data (historical backfill) and incremental data for both the companies A & B.
- Historical backfill batch processing = processing old historical data in bulk to populate or correct a system's data history (load the old sales records into the warehouse.).

### Data Model
- Check the data for both companies are prepare an excell sheet with the columns and analyse

### How to apply transformation
1. Use a common method
    - find duplicates, use df.dropDuplicates()
    - remove nulls, use isNull()
    - title case fix, use initcap()
    - typo mistake, use distint()
    - reduce number of partition, use coalesce() return no NULL value EX: (Null, 5 ,-4) -->5
    - Consult business manager and get the column rules for transformation
2. Apply the above transformation build a pipeline and get it in gold layer dashboard,  get the feedback from the data analystics fix the errors and deploy.

### Fact data processing
