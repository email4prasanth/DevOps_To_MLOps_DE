# Databricks UI
- Create databricks free edition using email id will reach the UI.
## Catalog setup and data import
- click on workspace, create folder **consolidated pipeline**, create notebook `01_setup`
```sh
%sql
CREATE CATALOG IF NOT EXISTS fmcg;
USE fmcg;
```
- Open the catalog in new window under **My organisation** you can see fmcg catalog
- now create schema
```sh
%sql
create schema if not exists fmcg.gold;
create schema if not exists fmcg.silver;
create schema if not exists fmcg.bronze;
```
- now need to create table using upload the data either by using data ingesition under **data engineer**, click on create and modify table, select
    - create new table
    - catalog- fmcg
    - schema -  gold
    - table name - <name>
    - modify the column type if required
    - drop table <catalog.schema.table>

```sh
%sql
show tables from fmcg.gold;
select count(*) from fmcg.gold.dim_customers;
describe fmcg.gold.dim_customers
describe extended fmcg.gold.dim_customers
```
- Usually date table is created by using script during the table creation, open workspace consolidated pipeline upload the existing **project-de-fmcg-atlikon\1_codes\1_setup\dim_date_table_creation**, run the .ipynb file, now the table is created under gold schema.

###  Tables
- In a data warehouse (Star Schema or Snowflake Schema), 
    1. Fact tables store measurable business events contains numeric, Foreign keys etc.,
        - Facts: sold_quantity
        - Foreign keys: product_code, customer_code
    2. Dimension tables store descriptive information about those events, contains descriptive attributes used for filtering, grouping, and reporting.
        - dim_customers:	customer_code	customer	market	platform	channel
        - dim_gross_price:	product_code	price_inr	year		
        - dim_products:	product_code	division	category	product	variant

```sh
                 dim_customers
                      |
                      |
dim_date ---- fact_orders ---- dim_gross_price
                      |
                      |
                dim_products
```
        
