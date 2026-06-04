- Create a view that show all the columns of dim and facts in one table
```sh
CREATE OR REPLACE VIEW fmcg.gold.vw_fact_orders_enriched AS (
    SELECT 
        fo.date,
        fo.product_code,
        fo.customer_code,

        -- Date attributes
        dd.date_key,
        dd.year,
        dd.month_name,
        dd.month_short_name,
        dd.quarter,
        dd.year_quarter,

        -- Customer attributes
        dc.customer,
        dc.market,
        dc.platform,
        dc.channel,

        -- Product attributes
        dp.division,
        dp.category,
        dp.product,
        dp.variant,

        -- Metrics
        fo.sold_quantity,
        gp.price_inr,

        -- Derived Metric: Amount
        (fo.sold_quantity * gp.price_inr) AS total_amount_inr
    
    FROM fmcg.gold.fact_orders fo

    -- Join with Date Dimension
    LEFT JOIN fmcg.gold.dim_date dd
           ON TO_DATE(fo.date, 'dd-MM-yyyy') = dd.month_start_date

    -- Join with Customers
    LEFT JOIN fmcg.gold.dim_customers dc 
           ON fo.customer_code = dc.customer_code

    -- Join with Products
    LEFT JOIN fmcg.gold.dim_products dp 
           ON fo.product_code = dp.product_code

    -- Join with Price (year-based)
    LEFT JOIN fmcg.gold.dim_gross_price gp 
           ON fo.product_code = gp.product_code
          AND YEAR(TO_DATE(fo.date, 'dd-MM-yyyy')) = gp.year
);


-- Preview
SELECT * FROM fmcg.gold.vw_fact_orders_enriched;
```
- add geine, go geine space and add fmcg.gold.vw_fact_orders_enriched


CREATE OR REPLACE VIEW fmcg.gold.vw_fact_orders_enriched AS
SELECT
    fo.date,
    fo.product_code,
    fo.customer_code,

    -- Date attributes
    dd.date_key,
    dd.year,
    dd.month_name,
    dd.month_short_name,
    dd.quarter,
    dd.year_quarter,

    -- Customer attributes
    dc.customer,
    dc.market,
    dc.platform,
    dc.channel,

    -- Product attributes
    dp.division,
    dp.category,
    dp.product,
    dp.variant,

    -- Metrics
    fo.sold_quantity,
    gp.price_inr,

    -- Derived Metric
    (fo.sold_quantity * gp.price_inr) AS total_amount_inr

FROM fmcg.gold.fact_orders fo

-- Date Dimension Join
LEFT JOIN fmcg.gold.dim_date dd
    ON COALESCE(
           TO_DATE(fo.date, 'dd-MM-yyyy'),
           TO_DATE(fo.date, 'yyyy-MM-dd')
       ) = dd.month_start_date

-- Customer Join
LEFT JOIN fmcg.gold.dim_customers dc
    ON fo.customer_code = dc.customer_code

-- Product Join
LEFT JOIN fmcg.gold.dim_products dp
    ON fo.product_code = dp.product_code

-- Gross Price Join
LEFT JOIN fmcg.gold.dim_gross_price gp
    ON fo.product_code = gp.product_code
   AND YEAR(
           COALESCE(
               TO_DATE(fo.date, 'dd-MM-yyyy'),
               TO_DATE(fo.date, 'yyyy-MM-dd')
           )
       ) = gp.year;