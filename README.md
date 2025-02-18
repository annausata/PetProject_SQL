# PetProject_SQL
## 🔍 Dataset Overview
The dataset, **"E-commerce Customer Behavior and Purchase Dataset"**, was sourced from Kaggle and is a synthetic dataset generated using the Faker Python library. It simulates an e-commerce environment, capturing various aspects of customer behavior and purchase history. This dataset is designed for data analysis and predictive modeling in e-commerce, making it suitable for tasks like customer churn prediction, market basket analysis, recommendation systems, and trend analysis.  

**Dataset Attributes**  
The dataset includes the following key attributes:  
🔶 Customer Information:  
- *customer_id* – Unique identifier for each customer.  
- *customer_name* – Name of the customer.
- *customer_age* – Age of the customer.
- *age* – Duplicate age column (removed during data cleaning).
- *gender* – Gender of the customer.
  
🔶 Purchase Details:
  - *purchase_date* – Date of each purchase.
  - *product_category* – Type of the purchased product.
  - *product_price* – Price of the purchased product.
  - *quantity* – Number of units purchased.
  - *total_purchase_amount* – Total cost of the purchase.
  - *payment_method* – Payment method used (e.g., credit card, PayPal).

🔶 Behavioral Indicators:
  - *returns* – Boolean indicator showing if a product was returned (previously returns).
  - *churn* – Boolean column indicating if the customer has churned (1 = churned, 0 = retained).

This dataset provides valuable insights into **customer purchasing behavior, payment preferences, and retention trends**, making it a useful resource for various data-driven applications in e-commerce analytics.

## 🧹 Data Cleaning Process

🔶 Checking and Handling Duplicates    
   - Performed a  check by analyzing repeated entries based on *customer_id*, *purchase_date*, *product_category*, and *total_purchase_amount*.No duplicate records were found in the dataset.

🔶 Removing Redundant Columns  
   - Dropped the unnecessary *age* column, as it was a duplicate of *customer_age*.

🔶 Converting Data Types for Boolean Fields
   - Transformed the returns column from NUMERIC(10,1) to a BOOLEAN field (*is_returns*).
   ```
       ALTER TABLE ecommerce_data
       ADD COLUMN is_returns BOOLEAN;

       UPDATE ecommerce_data
       SET is_returns = CASE 
                    WHEN returns = 1 THEN TRUE 
                    ELSE FALSE 
                END;

       ALTER TABLE ecommerce_data
       DROP COLUMN returns;
   ```
   - Similarly, converted the *churn* column from INT to BOOLEAN.

🔶 Optimizing Text Columns  
   - Changed *customer_name*, *payment_method*, and *product_categor*y from TEXT to VARCHAR with appropriate length constraints based on their maximum observed lengths.

🔶 Converting customer_id from VARCHAR to INT  
   - Added a new column, converted existing values, validated the conversion, and replaced the original column with the new integer-based *customer_id*.

## ⁉️ Key Analytical Questions
#### 1. Identifying the Most Profitable Customer Segments
#### 2. Purchase Trends Over Time and Seasonality  

## 📊 Data Analysis and Insights
### 1️⃣ *Identifying the Most Profitable Customer Segments*
**Question**: How does the average purchase amount vary by customer age? Is there a specific age group that generates the highest revenue?  

*1. Group customers by age (e.g., 18-25, 26-35, etc.).  
2. Calculate the average purchase amount for each group.  
3. Examine whether the payment method influences spending behavior (e.g., PayPal vs. credit card).*
#### 🔶 Checking the Minimum and Maximum Ages
```
SELECT MIN(customer_age) as min_age,
       MAX(customer_age) as max_age
FROM ecommerce_data;
```
🔹 Before creating age groups, I first check the range of ages in the dataset.  
🔹 This will return the youngest and oldest customer ages, helping ensure that my grouping logic covers all customers.  

#### 🔶 First Test: Grouping Ages & Calculating Purchase Behavior
```
SELECT
    CASE
        WHEN customer_age <= 25 THEN '18-25'
        WHEN customer_age <= 35 THEN '26-35'
        WHEN customer_age <= 45 THEN '36-45'
        WHEN customer_age <= 55 THEN '46-55'
        ELSE '55+'
    END as age_groups,
    ROUND(AVG(total_purchase_amount)) as avg_purchase_amount,
    COUNT(*) as count_number_purchases
FROM ecommerce_data
GROUP BY age_groups
ORDER BY avg_purchase_amount;
```
🔹 **Why?**  
- Created age categories dynamically using CASE WHEN.  
- Calculated the average purchase amount (AVG(total_purchase_amount)).
- Counedt the number of purchases per age group (COUNT(*)).
- Ordered by spending behavior to see which age group spends the most.  
  
#### 🔶 Adding an "Age Group" Column for Easier Queries. Updating the Table with Age Groups
```
ALTER TABLE ecommerce_data
ADD COLUMN age_group VARCHAR(6);
```
🔹Instead of computing the age group every time, I add a column to store it permanently. This optimizes queries later.
```
UPDATE ecommerce_data
SET age_group = CASE 
                   WHEN customer_age <= 25 THEN '18-25'
                   WHEN customer_age <= 35 THEN '26-35'
                   WHEN customer_age <= 45 THEN '36-45'
                   WHEN customer_age <= 55 THEN '46-55'
                   ELSE '55+'
               END;
```
🔹 I assign each customer a predefined age group and store it in the new column.  
🔹 **Benefit:** Now, I don’t need to calculate age groups every time I query the dataset.  

#### 🔶 Final Query: Purchase Behavior by Age Group & Payment Method
```
SELECT
    payment_method,
    age_group,
    ROUND(AVG(total_purchase_amount)) as avg_purchase_amount,
    COUNT(customer_id) as count_customer
FROM ecommerce_data 
GROUP BY payment_method, age_group
ORDER BY payment_method, age_group DESC;
```
🔹 **Why?**
- Analyzed purchase behavior across payment methods.
- Grouped by both age group & payment method to see spending trends.
- Ordered results by payment_method first and age_group descending to structure insights clearly.


### 📒 Analysis of Age Groups and Payment Methods in E-commerce Purchases

![Identifying the Most Profitable Customer Segments](https://github.com/user-attachments/assets/cefa1a96-c507-41b7-8e41-d7c3255d1685)

**Spending Patterns Across Age Groups:**  
1. The average purchase amount is relatively stable across all age groups, with minor variations.
2. Older customers (55+) tend to spend slightly more on average compared to younger ones (18-25).
3. The youngest group (18-25) consistently has the lowest average purchase amount, which might indicate budget-conscious spending behavior.  

**Popularity of Payment Methods by Age:**
1. Credit Cards and PayPal are the most used payment methods across all age groups, with the highest number of transactions.
2. Cash payments are more common among older demographics, possibly due to traditional purchasing habits.
3. Cryptocurrency usage is the lowest, especially among younger customers, which is somewhat unexpected given the tech-savvy nature of younger generations.

**Key Insights:**
1. Older customers (55+) spend slightly more per purchase, regardless of payment method.
2. Younger users (18-25) have the lowest transaction amounts, which could be due to financial limitations or different shopping habits.
3. The preference for Credit Cards and PayPal suggests a trust in digital payment methods, while Crypto remains a niche option with lower adoption rates.

**Potential Business Actions:**
1. Target younger customers with promotions or discounts to encourage higher spending.
2. Expand crypto adoption by offering incentives for using it, particularly for younger demographics.
3. Improve digital payment experiences for older customers, as they show high engagement with credit cards and PayPal.

### 2️⃣ *Purchase Trends Over Time and Seasonality* 
**Question**: How do purchase volume and total spending fluctuate throughout the year, and what seasonal trends can be observed?

*1. Analyze purchase behavior across different months to identify peak sales periods.  
2. Determine the most profitable and most customer-active months for each product category.  
3. Identify patterns in customer demand and revenue generation, linking them to possible seasonal trends.*

#### 🔶 Extracting Month and Grouping Data
First, I extract the month from the purchase date and group the data by product_category and month. I calculate two key metrics:
1. Total revenue (sum of purchase amounts).
2. Number of unique customers who made purchases.
```
SELECT 
    product_category,
    date_part('month', purchase_date) AS month,
    COUNT(customer_id) AS total_customers,
    ROUND(SUM(total_purchase_amount)) AS total_revenue
FROM ecommerce_data
GROUP BY product_category, month
ORDER BY product_category, month ;
```
🔹 This query gives me the monthly revenue and customer count for each product category. However, I want to highlight only the most significant months.

#### 🔶 Identifying Top Revenue Months
To find the three months with the highest revenue for each product category, I use the RANK() function:
```
WITH revenue_ranked AS (
    SELECT 
        product_category,
        date_part('month', purchase_date) AS month,
        ROUND(SUM(total_purchase_amount)) AS total_revenue,
        COUNT(customer_id) AS total_customers,
        RANK() OVER (PARTITION BY product_category ORDER BY SUM(total_purchase_amount) DESC) AS revenue_rank
    FROM ecommerce_data
    GROUP BY product_category, month
)
SELECT product_category, month, total_revenue, total_customers
FROM revenue_ranked
WHERE revenue_rank <= 3
ORDER BY product_category, total_revenue DESC;
```
🔹 Now I have only the top 3 months for revenue per product category. However, I also need to identify months with the highest number of customers.

#### 🔶 Identifying Top Customer Months
Similarly, I rank months based on customer count:
```

WITH customer_ranked AS (
    SELECT 
        product_category,
        date_part('month', purchase_date) AS month,
        ROUND(SUM(total_purchase_amount)) AS total_revenue,
        COUNT(customer_id) AS total_customers,
        RANK() OVER (PARTITION BY product_category ORDER BY COUNT(customer_id) DESC) AS customer_rank
    FROM ecommerce_data
    GROUP BY product_category, month
)
SELECT product_category, month, total_revenue, total_customers
FROM customer_ranked
WHERE customer_rank <= 3
ORDER BY product_category, total_customers DESC;
```
🔹 This query gives me the three months with the highest customer activity per product category.

#### 🔶 Combining Both Metrics (Final Query)
To get a complete picture, I merge the top revenue and top customer months into a single table using UNION ALL:
```
WITH revenue_ranked AS (
    SELECT 
        product_category,
        date_part('month', purchase_date) AS month,
        ROUND(SUM(total_purchase_amount)) AS total_revenue,
        COUNT(customer_id) AS total_customers,
        RANK() OVER (PARTITION BY product_category ORDER BY SUM(total_purchase_amount) DESC) AS revenue_rank
    FROM ecommerce_data
    GROUP BY product_category, month
),
customer_ranked AS (
    SELECT 
        product_category,
        date_part('month', purchase_date) AS month,
        ROUND(SUM(total_purchase_amount)) AS total_revenue,
        COUNT(customer_id) AS total_customers,
        RANK() OVER (PARTITION BY product_category ORDER BY COUNT(customer_id) DESC) AS customer_rank
    FROM ecommerce_data
    GROUP BY product_category, month
)
SELECT product_category, month, total_revenue, total_customers, 'Top Revenue' AS ranking_type
FROM revenue_ranked
WHERE revenue_rank <= 3

UNION ALL

SELECT product_category, month, total_revenue, total_customers, 'Top Customers' AS ranking_type
FROM customer_ranked
WHERE customer_rank <= 3

ORDER BY product_category, ranking_type, total_revenue DESC, total_customers DESC;
```
🔹 This final query provides both:
- The top 3 months by revenue for each product category.
- The top 3 months by customer count for each product category.
- A column (ranking_type) to distinguish whether the ranking is based on revenue or customer activity.

### 📒 Analysis of Purchase Trends Over Time and Seasonality
Based on the results, I can identify key trends regarding seasonality and purchase behavior across different product categories.

![1 Analysis of Purchase Trends Over Time and Seasonality](https://github.com/user-attachments/assets/657bd22c-86a1-4fb8-97cd-cfd8ee3af0d0)
![2 Analysis of Purchase Trends Over Time and Seasonality](https://github.com/user-attachments/assets/de46d4ad-e276-49e0-9635-47c422bb2a67)


**Key Takeaways on Seasonality**
1. *January is a strong sales month for Books, Clothing, and Electronics*, likely due to New Year sales.
2. *August is a peak month across multiple categories*, possibly influenced by back-to-school shopping.
3. *March consistently appears in the top months*, suggesting seasonal promotions or consumer habits in early spring.
4. *Electronics have different purchase behavior*, with revenue peaking in different months compared to customer volume.
 
## 💡 Key Learnings and Skills Gained
Throughout this project, I significantly improved my SQL skills by writing complex queries and utilizing various functions. I worked extensively with **GROUP BY, window functions, UNION, and aggregation functions** to analyze customer behavior and purchase trends. Additionally, I gained hands-on experience in **data cleaning and transformation**, including handling duplicates, optimizing table structures, and modifying data types for efficiency. This project also deepened my understanding of **seasonality and customer segmentation**, allowing me to derive meaningful business insights that support data-driven decision-making.
