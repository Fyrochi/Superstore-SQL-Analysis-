# Superstore Sales Analysis (SQL in Snowflake)

**Goal:**  

I explored the Superstore dataset to understand how sales and profit change over time, across different products and regions.  
The main aim was to use SQL in Snowflake to uncover trends that show where the business is performing well and where profit margins could be improved.


##  Dataset
- Source: [Kaggle – Superstore Dataset](https://www.kaggle.com/datasets/vivek468/superstore-dataset-final)
- Rows: ~10,000
- Columns: Order Date, Ship Date, Sales, Profit, Category, Region, Segment, etc.

---

##  Key Business Questions

1. How have **sales and profit changed over time**, and are there any clear seasonal trends from quarter to quarter?  
2. Which **product categories and sub-categories** contribute the most to total sales and profit, and which ones are underperforming?  
3. Which **products stand out as top performers** within each category based on total sales?  
4. How does **profitability vary across regions**, and which areas are the strongest or weakest performers?  
5. Overall, where are the **biggest opportunities to improve profit margins**, through pricing, cost efficiency, or product focus?

---

### 🗓️ Quarterly Sales and Profit Trends

**Goal:**  
Examine quarterly sales and profit patterns across multiple years to spot recurring seasonal trends and understand how profitability shifts during high-demand periods.

**SQL Query:**  
```sql
SELECT 
    YEAR(Order_Date) AS order_year,
    QUARTER(Order_Date) AS order_quarter,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit) / SUM(Sales) * 100, 2) AS profit_margin
FROM SUPERSTORE.PUBLIC.SUPERSTORE
GROUP BY order_year, order_quarter
ORDER BY order_year, order_quarter;
```

#### Results

| Year | Quarter | Total Sales (USD) | Total Profit (USD) | Profit Margin (%) |
|------|----------|------------------:|------------------:|------------------:|
| 2014 | Q1 | 74,447.80 | 3,811.23 | 5.12 |
| 2014 | Q2 | 86,538.76 | 11,204.07 | 12.95 |
| 2014 | Q3 | 143,633.21 | 12,804.72 | 8.91 |
| 2014 | Q4 | 179,627.73 | 21,723.95 | 12.09 |
| 2015 | Q1 | 68,851.74 | 9,264.94 | 13.46 |
| 2015 | Q2 | 89,124.19 | 12,190.92 | 13.68 |
| 2015 | Q3 | 130,259.58 | 16,853.62 | 12.94 |
| 2015 | Q4 | 182,297.01 | 23,309.12 | 12.79 |
| 2016 | Q1 | 93,237.18 | 11,441.37 | 12.27 |
| 2016 | Q2 | 136,082.30 | 16,390.34 | 12.04 |
| 2016 | Q3 | 143,787.36 | 15,823.60 | 11.00 |
| 2016 | Q4 | 236,098.75 | 38,139.86 | 16.15 |
| 2017 | Q1 | 123,144.86 | 23,506.20 | 19.09 |
| 2017 | Q2 | 133,764.37 | 15,499.21 | 11.59 |
| 2017 | Q3 | 196,251.96 | 26,985.13 | 13.75 |
| 2017 | Q4 | 280,054.07 | 27,448.73 | 9.80 |

###  Summary

- Sales consistently peak in Q4, showing clear seasonality, likely tied to holiday spending.
- Profit margins often dip in Q4, suggesting the impact of discounts and higher operating costs.
- Between 2014–2017, total sales grew steadily from ~$480K to ~$730K, while margins stayed fairly stable (10–13%).  
- Q4 2016 combined strong sales and a 16% margin, suggesting a well-managed quarter.
- Q4 2017 saw record sales but thinner margins (~10%), pointing to growth driven more by volume than profit.

###  Business Implications

- Plan stock and campaigns ahead of Q4 to handle seasonal demand.  
- Revisit discount strategies to avoid shrinking profit margins.
- Learn from 2016’s Q4 efficiency, replicate what worked operationally that period.

 ---

### Top Product Categories

```sql
SELECT 
    Category,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit) / SUM(Sales) * 100, 2) AS profit_margin
FROM SUPERSTORE.PUBLIC.SUPERSTORE
GROUP BY Category
ORDER BY total_sales DESC;
```

**Results:**

| Category        | Total Sales (USD) | Total Profit (USD) | Profit Margin (%) |
|-----------------|------------------:|------------------:|------------------:|
| Technology      | 836,154.03        | 145,454.95        | **17.40%** |
| Furniture       | 741,999.80        | 18,451.27         | **2.49%** |
| Office Supplies | 719,047.03        | 122,490.80        | **17.04%** |

###  Summary

- Technology drives both strong sales and healthy margins (~17%), making it the top performer.
- Furniture earns similar revenue but struggles with profitability (~2.5%), possibly due to delivery or production costs.
- Office Supplies offers consistency with a reliable 17% margin — lower in value, but steady in performance.

###  Business Implications

- Review Furniture logistics and pricing for cost inefficiencies.
- Keep focusing on Technology — strong demand and margins suggest room for growth.
- Office Supplies provide stability and balance to overall sales.

---

###  Top Sub-Categories

**Business Question:**  
After analysing the top category, I wanted to break down sales and profit by sub-category to see which specific product areas are driving performance and which ones might be holding the business back.

```sql
SELECT 
    "Sub-Category" AS sub_category,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit) / SUM(Sales) * 100, 2) AS profit_margin
FROM SUPERSTORE.PUBLIC.SUPERSTORE
GROUP BY sub_category
ORDER BY total_sales DESC;
```

**Results:**

| Sub-Category | Total Sales (USD) | Total Profit (USD) | Profit Margin (%) |
|---------------|------------------:|------------------:|------------------:|
| Phones | 330,007.05 | 44,515.73 | **13.49%** |
| Chairs | 328,449.10 | 26,590.17 | **8.10%** |
| Storage | 223,843.61 | 21,278.83 | **9.51%** |
| Tables | 206,965.53 | -17,725.48 | **-8.56%** |
| Binders | 203,412.73 | 30,221.76 | **14.86%** |
| Machines | 189,238.63 | 3,384.76 | **1.79%** |
| Accessories | 167,380.32 | 41,936.64 | **25.05%** |
| Copiers | 149,528.03 | 55,617.82 | **37.20%** |
| Bookcases | 114,880.00 | -3,472.56 | **-3.02%** |
| Appliances | 107,532.16 | 18,138.01 | **16.87%** |
| Furnishings | 91,705.16 | 13,059.14 | **14.24%** |
| Paper | 78,479.21 | 34,053.57 | **43.39%** |
| Supplies | 46,673.54 | -1,189.10 | **-2.55%** |
| Art | 27,118.79 | 6,527.79 | **24.07%** |
| Envelopes | 16,476.40 | 6,964.18 | **42.27%** |
| Labels | 12,486.31 | 5,546.25 | **44.42%** |
| Fasteners | 3,024.28 | 949.52 | **31.40%** |

### Summary

- Phones and Chairs are top sellers, driving total revenue.
- Copiers and Paper have exceptional margins, despite smaller sales volumes.
- Tables show strong demand but negative margins. A clear cost or pricing issue.

### Business Implications

- Address loss-making categories like Tables to avoid profit leakage.
- Promote high-margin products (Copiers, Paper) for better overall profitability.
- Small margin improvements in Phones and Chairs could significantly boost total profit.

---

### Top 5 Products per Category

**Business Question:**  
I wanted to find out which specific products perform best within each category. This helps identify which items drive most of the revenue and highlights potential best-sellers to prioritize.

```sql
lWITH ranked_products AS (
    SELECT
        Category,
        PRODUCT_NAME AS product_name,
        ROUND(SUM(Sales), 2) AS total_sales,
        ROW_NUMBER() OVER (
            PARTITION BY Category
            ORDER BY SUM(Sales) DESC
        ) AS rank_in_category
    FROM SUPERSTORE.PUBLIC.SUPERSTORE
    GROUP BY Category, PRODUCT_NAME
)
SELECT 
    Category,
    product_name,
    total_sales,
    rank_in_category
FROM ranked_products
WHERE rank_in_category <= 5
ORDER BY Category, rank_in_category;
```

| Category | Product Name | Total Sales (USD) | Rank in Category |
|-----------|---------------|------------------:|-----------------:|
| **Furniture** | HON 5400 Series Task Chairs for Big and Tall | 21,870.58 | 1 |
|  | Riverside Palais Royal Lawyers Bookcase, Royale Cherry Finish | 15,610.97 | 2 |
|  | Bretford Rectangular Conference Table Tops | 12,995.29 | 3 |
|  | Global Troy Executive Leather Low-Back Tilter | 12,975.38 | 4 |
|  | SAFCO Arco Folding Chair | 11,572.78 | 5 |
| **Office Supplies** | Fellowes PB500 Electric Punch Plastic Comb Binding Machine with Manual Bind | 27,453.38 | 1 |
|  | GBC DocuBind TL300 Electric Binding System | 19,823.48 | 2 |
|  | GBC Ibimaster 500 Manual ProClick Binding System | 19,024.50 | 3 |
|  | GBC DocuBind P400 Electric Binding System | 17,965.07 | 4 |
|  | High Speed Automatic Electric Letter Opener | 17,030.31 | 5 |
| **Technology** | Canon imageCLASS 2200 Advanced Copier | 61,599.82 | 1 |
|  | Cisco TelePresence System EX90 Videoconferencing Unit | 22,638.48 | 2 |
|  | Hewlett Packard LaserJet 3310 Copier | 18,839.69 | 3 |
|  | HP Designjet T520 Inkjet Large Format Printer - 24" Color | 18,374.90 | 4 |
|  | Lexmark MX611dhe Monochrome Laser Printer | 16,829.90 | 5 |

### Summary

- Furniture: Task chairs and bookcases are reliable sellers, consistent but not very high-margin.
- Office Supplies: Binding and document machines lead sales, likely from business orders.
- Technology: The Canon Copier dominates sales by a wide margin, showing strong market demand.
- Overall, top-selling items tend to be higher-value office equipment, not smaller consumables.

### Business Implications

- Keep high-performing items like Canon Copiers and HON Task Chairs well-stocked.
- Promote top products in marketing and bundles.
- Tailor category strategies. Technology and Furniture rely on a few big sellers, while Office Supplies have more balance.

---

### Customer Segment Performance

**Business Question:**  
I wanted to understand which customer segments generate the most sales and profit, and how their margins compare. This helps show where the business is strongest and which types of customers bring the best return.


| Segment | Total Sales (USD) | Total Profit (USD) | Profit Margin (%) |
|----------|------------------:|------------------:|------------------:|
| Consumer | 1,161,401.35 | 134,119.21 | **11.55%** |
| Corporate | 706,146.37 | 91,979.13 | **13.03%** |
| Home Office | 429,653.15 | 60,298.68 | **14.03%** |

### Summary

- Consumers generate the highest total sales, showing strong demand from individual buyers.  
- Corporate customers have slightly higher margins (~13%), suggesting business orders are handled more efficiently or involve fewer discounts.  
- Home Office makes up the smallest share of sales but achieves the best margin (~14%), meaning these customers are the most profitable on average.


### Business Implications

- Consumers: Remain the biggest driver of revenue — maintaining engagement through service and loyalty offers will help sustain this growth.  
- Corporate: Strong balance of volume and margin — bulk or recurring order programs could help expand this segment further.  
- Home Office: Small but highly profitable — focusing on this group could lift overall margins without needing large sales increases.

---


### Regional Performance

**Business Question:**  

I wanted to compare sales and profit across regions to see which parts of the business are performing the strongest and where profitability could be improved.

```sql
SELECT 
    Region,
    ROUND(SUM(Sales), 2) AS total_sales,
    ROUND(SUM(Profit), 2) AS total_profit,
    ROUND(SUM(Profit) / SUM(Sales) * 100, 2) AS profit_margin
FROM SUPERSTORE.PUBLIC.SUPERSTORE
GROUP BY Region
ORDER BY total_sales DESC;
```

**Results:**

| Region  | Total Sales (USD) | Total Profit (USD) | Profit Margin (%) |
|----------|------------------:|------------------:|------------------:|
| West     | 725,457.82        | 108,418.45        | **14.94%** |
| East     | 678,781.24        | 91,522.78         | **13.48%** |
| Central  | 501,239.89        | 39,706.36         | **7.92%** |
| South    | 391,721.91        | 46,749.43         | **11.93%** |

### Summary 

- West leads in both sales and profit, showing strong market demand.
- East performs similarly with stable margins.
- South has smaller sales but solid margins, suggesting efficient operations.
- Central trails in both sales and profitability — potential area for review.

### Business Implications

- Focus marketing and inventory efforts in the West and East, proven strong performers.
- Review logistics or pricing in the Central region to lift margins.
- Study the South’s efficiency model for potential best practices.

---


## 🏁 Conclusion

This project was all about exploring sales and profit data from the Superstore dataset to understand how performance changes over time, across products, regions, and customer types.

If I were to continue this analysis, I’d look into customer-level trends (like repeat purchases or order frequency) and explore how pricing or discounting impacts profit margins.


## 🧰 Tools Used
- **SQL (Snowflake)**
- **Snowsight** for visualization
- **GitHub** for documentation

---

## 🧾 Author
**Brendan C**  
