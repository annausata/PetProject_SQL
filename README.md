# PetProject_SQL

### 1️⃣ Identifying the Most Profitable Customer Segments
**Question**: How does the average purchase amount vary by customer age? Is there a specific age group that generates the highest revenue?  

*1. Group customers by age (e.g., 18-25, 26-35, etc.).  
2. Calculate the average purchase amount for each group.  
3. Examine whether the payment method influences spending behavior (e.g., PayPal vs. credit card).*

### 2️⃣ Do Returns Influence Customer Churn?
**Question**: Is there a correlation between frequent product returns and customer churn (churn = True)?  
  
*1. Group customers based on the number of returns.  
2. Calculate the churn rate among customers who have returned products.  
3. Compare it to those who have never returned an item.*

### 3️⃣ Purchase Trends Over Time and Seasonality
**Question**: How do purchase volume and total spending change across different seasons?  

*1. Categorize purchase dates into seasons (winter, spring, summer, fall).  
2. Analyze the total number of purchases and revenue in each season.  
3. Identify which product categories are most popular during different seasons.*

### 4️⃣ Relationship Between Product Category and Return Probability
**Question**: Which product categories have the highest return rates, and why?  

*1. Calculate the percentage of returns for each product category.  
2. Investigate whether higher-priced products are returned more often.  
3. Analyze whether the payment method affects return rates (e.g., are PayPal transactions more likely to result in returns?).*

### 5️⃣ Is There an "Ideal" Customer Who Spends the Most?
**Question**: Is there a specific customer profile that tends to make the highest-value purchases? (e.g., based on age, payment method, purchase frequency, etc.)  

*1. Identify the age and gender of customers with the highest total spending.
2. Examine whether spending correlates with purchase frequency (i.e., do frequent buyers spend more overall?).
3. Determine if the payment method influences the average purchase amount.*

# ANALYTICS

### 1️⃣ Checking the Minimum and Maximum Ages
```
SELECT MIN(customer_age) as min_age,
       MAX(customer_age) as max_age
FROM ecommerce_data;
```
🔹 **Why?** Before creating age groups, you first check the range of ages in your dataset.
🔹 **Expected Output:** This will return the youngest and oldest customer ages, helping ensure that your grouping logic covers all customers.

### 2️⃣ First Test: Grouping Ages & Calculating Purchase Behavior
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
- You create age categories dynamically using CASE WHEN.
- You calculate the average purchase amount (AVG(total_purchase_amount)).
- You count the number of purchases per age group (COUNT(*)).
- You order by spending behavior to see which age group spends the most.  

🔹 **Improvements:**  
- If there are missing age values (NULL), consider adding COALESCE(customer_age, 0) to handle them.  

### 3️⃣ Adding an "Age Group" Column for Easier Queries. Updating the Table with Age Groups
```
ALTER TABLE ecommerce_data
ADD COLUMN age_group VARCHAR(6);
```
🔹**Why?** Instead of computing the age group every time, you add a column to store it permanently. This optimizes queries later.
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
🔹 **Why?** You assign each customer a predefined age group and store it in the new column.  
🔹 **Benefit:** Now, you don’t need to calculate age groups every time you query the dataset.

5️⃣ Final Query: Purchase Behavior by Age Group & Payment Method
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
- You analyze purchase behavior across payment methods.
- You group by both age group & payment method to see spending trends.
- You order results by payment_method first and age_group descending to structure insights clearly.


### Analysis of Age Groups and Payment Methods in E-commerce Purchases

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
