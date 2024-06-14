### These examples cover various scenarios and use cases for each of the 10 advanced SQL window functions. By leveraging these functions, you can perform complex analytical tasks and gain deeper insights from your data.

### 1. `ROW_NUMBER()`

#### What It Is
`ROW_NUMBER()` assigns a unique sequential integer to each row within a partition of a result set.

#### When to Use
Use `ROW_NUMBER()` when you need to assign unique row identifiers within partitions of data, such as generating unique IDs or identifying row order.

#### Examples

1. **Simple Row Numbering**
   ```sql
   SELECT employee_id,
          ROW_NUMBER() OVER (ORDER BY hire_date) AS row_num
   FROM employees;
   ```
   **Interpretation:** Each employee gets a unique row number based on their hire date.

2. **Partitioned Row Numbering**
   ```sql
   SELECT employee_id,
          department_id,
          ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY hire_date) AS row_num
   FROM employees;
   ```
   **Interpretation:** Each employee within a department is assigned a unique row number based on their hire date.

3. **Top N Rows per Group**
   ```sql
   SELECT *
   FROM (
       SELECT employee_id,
              department_id,
              salary,
              ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num
       FROM employees
   ) AS subquery
   WHERE row_num <= 3;
   ```
   **Interpretation:** Selects the top 3 highest-paid employees within each department.

4. **Pagination**
   ```sql
   SELECT *
   FROM (
       SELECT employee_id,
              ROW_NUMBER() OVER (ORDER BY employee_id) AS row_num
       FROM employees
   ) AS subquery
   WHERE row_num BETWEEN 11 AND 20;
   ```
   **Interpretation:** Retrieves rows 11 to 20 from the employees table for pagination purposes.

5. **Deduplicate Rows**
   ```sql
   SELECT *
   FROM (
       SELECT *,
              ROW_NUMBER() OVER (PARTITION BY employee_id ORDER BY hire_date DESC) AS row_num
       FROM employees
   ) AS subquery
   WHERE row_num = 1;
   ```
   **Interpretation:** Retrieves the latest hire date for each employee, removing duplicates.

6. **Sequential Group Numbering**
   ```sql
   SELECT employee_id,
          department_id,
          ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY department_id) AS group_num
   FROM employees;
   ```
   **Interpretation:** Assigns a sequential number to each row within each department.

7. **Conditional Row Numbering**
   ```sql
   SELECT employee_id,
          department_id,
          ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY CASE WHEN salary > 50000 THEN 1 ELSE 0 END DESC) AS row_num
   FROM employees;
   ```
   **Interpretation:** Assigns row numbers within each department, prioritizing employees with salaries over $50,000.

8. **Rank Rows by Multiple Columns**
   ```sql
   SELECT employee_id,
          department_id,
          ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY hire_date, salary DESC) AS row_num
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department first by hire date, then by salary in descending order.

9. **Window Function with Filter**
   ```sql
   SELECT employee_id,
          ROW_NUMBER() OVER (PARTITION BY department_id ORDER BY salary DESC) AS row_num
   FROM employees
   WHERE department_id = 10;
   ```
   **Interpretation:** Assigns row numbers to employees within department 10, ordered by salary.

10. **Combining with Aggregate Functions**
    ```sql
    SELECT employee_id,
           salary,
           ROW_NUMBER() OVER (ORDER BY salary DESC) AS row_num,
           AVG(salary) OVER () AS avg_salary
    FROM employees;
    ```
    **Interpretation:** Assigns row numbers based on salary and calculates the average salary for all employees.

### 2. `RANK()`

#### What It Is
`RANK()` assigns a rank to each row within a partition of a result set, with gaps in the ranking values if there are ties.

#### When to Use
Use `RANK()` when you need to rank rows within partitions and handle ties by assigning the same rank to tied rows and skipping subsequent ranks.

#### Examples

1. **Simple Ranking**
   ```sql
   SELECT employee_id,
          RANK() OVER (ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees by salary in descending order, with ties receiving the same rank.

2. **Ranking within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department by salary in descending order, with ties.

3. **Handling Ties**
   ```sql
   SELECT employee_id,
          salary,
          RANK() OVER (ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Handles ties by assigning the same rank to tied employees and skipping the next rank(s).

4. **Top N Salaries with Ties**
   ```sql
   SELECT employee_id,
          salary
   FROM (
       SELECT employee_id,
              salary,
              RANK() OVER (ORDER BY salary DESC) AS salary_rank
       FROM employees
   ) AS subquery
   WHERE salary_rank <= 5;
   ```
   **Interpretation:** Retrieves the top 5 highest salaries, including ties.

5. **Multiple Ranking Criteria**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          hire_date,
          RANK() OVER (PARTITION BY department_id ORDER BY salary DESC, hire_date) AS rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department by salary, and by hire date in case of ties.

6. **Cumulative Distribution**
   ```sql
   SELECT employee_id,
          salary,
          RANK() OVER (ORDER BY salary DESC) AS rank,
          PERCENT_RANK() OVER (ORDER BY salary DESC) AS percent_rank
   FROM employees;
   ```
   **Interpretation:** Calculates both rank and percentage rank of employees based on salary.

7. **Conditional Ranking**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          RANK() OVER (PARTITION BY department_id ORDER BY CASE WHEN salary > 50000 THEN 1 ELSE 0 END DESC) AS rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department, prioritizing those with salaries over $50,000.

8. **Ranking with Filter**
   ```sql
   SELECT employee_id,
          salary,
          RANK() OVER (ORDER BY salary DESC) AS rank
   FROM employees
   WHERE department_id = 10;
   ```
   **Interpretation:** Ranks employees within department 10 by salary in descending order.

9. **Ranking Combined with Aggregate Functions**
    ```sql
    SELECT employee_id,
           department_id,
           salary,
           RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank,
           SUM(salary) OVER (PARTITION BY department_id) AS total_salary
    FROM employees;
    ```
    **Interpretation:** Ranks employees within each department by salary and calculates the total salary for each department.

10. **Identifying Rank Changes Over Time**
    ```sql
    SELECT employee_id,
           salary,
           RANK() OVER (ORDER BY salary DESC) AS current_rank,
           LAG(RANK() OVER (ORDER BY salary DESC)) OVER (ORDER BY salary) AS previous_rank
    FROM employees;
    ```
    **Interpretation:** Identifies changes in employee ranks based on salary.

### 3. `DENSE_RANK()`

#### What It Is
`DENSE_RANK()` assigns a rank to each row within a partition of a result set, without gaps in the ranking values.

#### When to Use
Use `DENSE_RANK()` when you need to rank rows within partitions and want to handle ties by assigning the same rank to tied rows without skipping subsequent ranks.

#### Examples

1. **Simple Dense Ranking**
   ```sql
   SELECT employee_id,
          DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees by salary in descending order, without gaps in rank values.

2. **Dense Ranking within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department by salary in descending order, without gaps.

3. **Handling Ties**
   ```sql
   SELECT employee_id,
          salary,
          DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
   FROM employees;
   ```
   **Interpretation:** Handles ties by assigning the same rank to tied employees without skipping ranks.

4. **Top N Salaries with Ties**
   ```sql
   SELECT employee_id,
          salary
   FROM (
       SELECT employee_id,
              salary,
              DENSE_RANK() OVER (ORDER BY salary DESC) AS salary_rank
       FROM employees
   ) AS subquery
   WHERE salary_rank <= 5;
   ```
   **Interpretation:** Retrieves the top 5 highest salaries, including ties, without gaps in rank values.

5. **Multiple Ranking Criteria**
   ```sql
   SELECT employee_id

,
          department_id,
          salary,
          hire_date,
          DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC, hire_date) AS rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department by salary, and by hire date in case of ties, without gaps.

6. **Cumulative Distribution**
   ```sql
   SELECT employee_id,
          salary,
          DENSE_RANK() OVER (ORDER BY salary DESC) AS rank,
          CUME_DIST() OVER (ORDER BY salary DESC) AS cume_dist
   FROM employees;
   ```
   **Interpretation:** Calculates both dense rank and cumulative distribution of employees based on salary.

7. **Conditional Ranking**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          DENSE_RANK() OVER (PARTITION BY department_id ORDER BY CASE WHEN salary > 50000 THEN 1 ELSE 0 END DESC) AS rank
   FROM employees;
   ```
   **Interpretation:** Ranks employees within each department, prioritizing those with salaries over $50,000, without gaps in rank values.

8. **Ranking with Filter**
   ```sql
   SELECT employee_id,
          salary,
          DENSE_RANK() OVER (ORDER BY salary DESC) AS rank
   FROM employees
   WHERE department_id = 10;
   ```
   **Interpretation:** Ranks employees within department 10 by salary in descending order, without gaps.

9. **Dense Ranking Combined with Aggregate Functions**
    ```sql
    SELECT employee_id,
           department_id,
           salary,
           DENSE_RANK() OVER (PARTITION BY department_id ORDER BY salary DESC) AS rank,
           AVG(salary) OVER (PARTITION BY department_id) AS avg_salary
    FROM employees;
    ```
    **Interpretation:** Ranks employees within each department by salary and calculates the average salary for each department, without gaps.

10. **Identifying Rank Changes Over Time**
    ```sql
    SELECT employee_id,
           salary,
           DENSE_RANK() OVER (ORDER BY salary DESC) AS current_rank,
           LAG(DENSE_RANK() OVER (ORDER BY salary DESC)) OVER (ORDER BY salary) AS previous_rank
    FROM employees;
    ```
    **Interpretation:** Identifies changes in employee ranks based on salary, without gaps in rank values.

### 4. `NTILE(n)`

#### What It Is
`NTILE(n)` divides the result set into `n` approximately equal groups and assigns a number to each row indicating its group.

#### When to Use
Use `NTILE(n)` when you need to distribute rows into a specified number of groups, often for comparison or benchmarking purposes.

#### Examples

1. **Simple NTILE**
   ```sql
   SELECT employee_id,
          NTILE(4) OVER (ORDER BY salary DESC) AS quartile
   FROM employees;
   ```
   **Interpretation:** Divides employees into 4 quartiles based on their salary.

2. **NTILE within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          NTILE(4) OVER (PARTITION BY department_id ORDER BY salary DESC) AS quartile
   FROM employees;
   ```
   **Interpretation:** Divides employees within each department into 4 quartiles based on salary.

3. **Deciles Distribution**
   ```sql
   SELECT employee_id,
          salary,
          NTILE(10) OVER (ORDER BY salary DESC) AS decile
   FROM employees;
   ```
   **Interpretation:** Divides employees into 10 deciles based on their salary.

4. **Rank by Hire Date**
   ```sql
   SELECT employee_id,
          hire_date,
          NTILE(5) OVER (ORDER BY hire_date) AS quintile
   FROM employees;
   ```
   **Interpretation:** Divides employees into 5 quintiles based on their hire date.

5. **Performance Benchmarking**
   ```sql
   SELECT employee_id,
          performance_score,
          NTILE(3) OVER (ORDER BY performance_score DESC) AS performance_group
   FROM employees;
   ```
   **Interpretation:** Divides employees into 3 performance groups based on their performance score.

6. **Sales Distribution**
   ```sql
   SELECT employee_id,
          sales_amount,
          NTILE(4) OVER (ORDER BY sales_amount DESC) AS sales_quartile
   FROM sales;
   ```
   **Interpretation:** Divides salespeople into 4 quartiles based on their sales amount.

7. **Profit Margin Analysis**
   ```sql
   SELECT product_id,
          profit_margin,
          NTILE(5) OVER (ORDER BY profit_margin DESC) AS profit_quintile
   FROM products;
   ```
   **Interpretation:** Divides products into 5 quintiles based on their profit margin.

8. **Customer Segmentation**
   ```sql
   SELECT customer_id,
          total_purchases,
          NTILE(4) OVER (ORDER BY total_purchases DESC) AS purchase_quartile
   FROM customers;
   ```
   **Interpretation:** Divides customers into 4 quartiles based on their total purchases.

9. **Stock Performance Grouping**
    ```sql
    SELECT stock_id,
           stock_price,
           NTILE(10) OVER (ORDER BY stock_price DESC) AS price_decile
    FROM stocks;
    ```
    **Interpretation:** Divides stocks into 10 deciles based on their price.

10. **Website Traffic Analysis**
    ```sql
    SELECT page_id,
           page_views,
           NTILE(4) OVER (ORDER BY page_views DESC) AS views_quartile
    FROM website_traffic;
    ```
    **Interpretation:** Divides web pages into 4 quartiles based on their page views.

### 5. `LAG()`

#### What It Is
`LAG()` provides access to a row at a specified physical offset that comes before the current row.

#### When to Use
Use `LAG()` when you need to compare the current row with a previous row, such as for trend analysis or changes over time.

#### Examples

1. **Simple LAG**
   ```sql
   SELECT employee_id,
          salary,
          LAG(salary, 1) OVER (ORDER BY hire_date) AS previous_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the salary of the previous employee based on hire date.

2. **LAG with Default Value**
   ```sql
   SELECT employee_id,
          salary,
          LAG(salary, 1, 0) OVER (ORDER BY hire_date) AS previous_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the salary of the previous employee, defaulting to 0 if there is no previous row.

3. **Comparing Sales**
   ```sql
   SELECT sale_id,
          sale_date,
          amount,
          LAG(amount, 1) OVER (ORDER BY sale_date) AS previous_amount
   FROM sales;
   ```
   **Interpretation:** Compares the sales amount with the previous sale.

4. **Tracking Changes in Stock Prices**
   ```sql
   SELECT stock_id,
          trade_date,
          closing_price,
          LAG(closing_price, 1) OVER (PARTITION BY stock_id ORDER BY trade_date) AS previous_closing_price
   FROM stock_prices;
   ```
   **Interpretation:** Tracks the changes in stock prices by comparing the closing price with the previous day's closing price for each stock.

5. **Identifying Customer Churn**
   ```sql
   SELECT customer_id,
          purchase_date,
          LAG(purchase_date, 1) OVER (PARTITION BY customer_id ORDER BY purchase_date) AS previous_purchase_date
   FROM purchases;
   ```
   **Interpretation:** Identifies gaps between customer purchases to detect potential churn.

6. **Analyzing Employee Promotions**
   ```sql
   SELECT employee_id,
          promotion_date,
          LAG(promotion_date, 1) OVER (PARTITION BY employee_id ORDER BY promotion_date) AS previous_promotion_date
   FROM promotions;
   ```
   **Interpretation:** Analyzes the time between promotions for each employee.

7. **Year-Over-Year Comparison**
   ```sql
   SELECT year,
          revenue,
          LAG(revenue, 1) OVER (ORDER BY year) AS previous_year_revenue
   FROM annual_revenue;
   ```
   **Interpretation:** Compares annual revenue with the previous year's revenue.

8. **Month-Over-Month Growth**
   ```sql
   SELECT month,
          profit,
          LAG(profit, 1) OVER (ORDER BY month) AS previous_month_profit
   FROM monthly_profit;
   ```
   **Interpretation:** Compares monthly profit with the previous month's profit.

9. **Website Traffic Changes**
    ```sql
    SELECT page_id,
           day,
           page_views,
           LAG(page_views, 1) OVER (PARTITION BY page_id ORDER BY day) AS previous_day_views
    FROM website_traffic;
    ```
    **Interpretation:** Analyzes changes in page views from the previous day for each web page.

10. **Sales Trend Analysis**
    ```sql
    SELECT product_id,
           sale_date,
           units_sold,
           LAG(units_sold, 1) OVER (PARTITION BY product_id ORDER BY sale_date) AS previous_units_sold
    FROM sales;
    ```
    **Interpretation:** Analyzes sales trends by comparing the number of units sold with the previous sale date for each product.

### 6. `LEAD()`

#### What It Is
`LEAD()` provides access to a row at a specified physical offset that follows the current row.

#### When to Use
Use `LEAD()` when you

 need to compare the current row with a subsequent row, such as for future planning or forecasting.

#### Examples

1. **Simple LEAD**
   ```sql
   SELECT employee_id,
          salary,
          LEAD(salary, 1) OVER (ORDER BY hire_date) AS next_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the salary of the next employee based on hire date.

2. **LEAD with Default Value**
   ```sql
   SELECT employee_id,
          salary,
          LEAD(salary, 1, 0) OVER (ORDER BY hire_date) AS next_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the salary of the next employee, defaulting to 0 if there is no next row.

3. **Forecasting Sales**
   ```sql
   SELECT sale_id,
          sale_date,
          amount,
          LEAD(amount, 1) OVER (ORDER BY sale_date) AS next_amount
   FROM sales;
   ```
   **Interpretation:** Compares the sales amount with the next sale.

4. **Future Stock Prices**
   ```sql
   SELECT stock_id,
          trade_date,
          closing_price,
          LEAD(closing_price, 1) OVER (PARTITION BY stock_id ORDER BY trade_date) AS next_closing_price
   FROM stock_prices;
   ```
   **Interpretation:** Tracks future changes in stock prices by comparing the closing price with the next day's closing price for each stock.

5. **Customer Purchase Prediction**
   ```sql
   SELECT customer_id,
          purchase_date,
          LEAD(purchase_date, 1) OVER (PARTITION BY customer_id ORDER BY purchase_date) AS next_purchase_date
   FROM purchases;
   ```
   **Interpretation:** Predicts the next purchase date for each customer.

6. **Employee Promotion Forecasting**
   ```sql
   SELECT employee_id,
          promotion_date,
          LEAD(promotion_date, 1) OVER (PARTITION BY employee_id ORDER BY promotion_date) AS next_promotion_date
   FROM promotions;
   ```
   **Interpretation:** Forecasts the time until the next promotion for each employee.

7. **Year-Over-Year Future Comparison**
   ```sql
   SELECT year,
          revenue,
          LEAD(revenue, 1) OVER (ORDER BY year) AS next_year_revenue
   FROM annual_revenue;
   ```
   **Interpretation:** Compares annual revenue with the next year's revenue.

8. **Month-Over-Month Future Growth**
   ```sql
   SELECT month,
          profit,
          LEAD(profit, 1) OVER (ORDER BY month) AS next_month_profit
   FROM monthly_profit;
   ```
   **Interpretation:** Compares monthly profit with the next month's profit.

9. **Website Traffic Forecast**
    ```sql
    SELECT page_id,
           day,
           page_views,
           LEAD(page_views, 1) OVER (PARTITION BY page_id ORDER BY day) AS next_day_views
    FROM website_traffic;
    ```
    **Interpretation:** Analyzes future changes in page views for the next day for each web page.

10. **Sales Trend Forecasting**
    ```sql
    SELECT product_id,
           sale_date,
           units_sold,
           LEAD(units_sold, 1) OVER (PARTITION BY product_id ORDER BY sale_date) AS next_units_sold
    FROM sales;
    ```
    **Interpretation:** Analyzes future sales trends by comparing the number of units sold with the next sale date for each product.

### 7. `FIRST_VALUE()`

#### What It Is
`FIRST_VALUE()` returns the first value in an ordered partition of the result set.

#### When to Use
Use `FIRST_VALUE()` when you need to retrieve the first value in an ordered set of rows, often for comparison or baseline purposes.

#### Examples

1. **Simple FIRST_VALUE**
   ```sql
   SELECT employee_id,
          salary,
          FIRST_VALUE(salary) OVER (ORDER BY hire_date) AS first_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the first salary based on hire date.

2. **FIRST_VALUE within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          FIRST_VALUE(salary) OVER (PARTITION BY department_id ORDER BY hire_date) AS first_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the first salary within each department based on hire date.

3. **First Sale Amount**
   ```sql
   SELECT sale_id,
          sale_date,
          amount,
          FIRST_VALUE(amount) OVER (ORDER BY sale_date) AS first_amount
   FROM sales;
   ```
   **Interpretation:** Retrieves the amount of the first sale.

4. **First Stock Price**
   ```sql
   SELECT stock_id,
          trade_date,
          closing_price,
          FIRST_VALUE(closing_price) OVER (PARTITION BY stock_id ORDER BY trade_date) AS first_closing_price
   FROM stock_prices;
   ```
   **Interpretation:** Retrieves the first closing price for each stock.

5. **First Customer Purchase**
   ```sql
   SELECT customer_id,
          purchase_date,
          FIRST_VALUE(purchase_date) OVER (PARTITION BY customer_id ORDER BY purchase_date) AS first_purchase_date
   FROM purchases;
   ```
   **Interpretation:** Retrieves the first purchase date for each customer.

6. **First Employee Promotion**
   ```sql
   SELECT employee_id,
          promotion_date,
          FIRST_VALUE(promotion_date) OVER (PARTITION BY employee_id ORDER BY promotion_date) AS first_promotion_date
   FROM promotions;
   ```
   **Interpretation:** Retrieves the first promotion date for each employee.

7. **Baseline Year Revenue**
   ```sql
   SELECT year,
          revenue,
          FIRST_VALUE(revenue) OVER (ORDER BY year) AS baseline_revenue
   FROM annual_revenue;
   ```
   **Interpretation:** Retrieves the baseline (first year's) revenue.

8. **Baseline Month Profit**
   ```sql
   SELECT month,
          profit,
          FIRST_VALUE(profit) OVER (ORDER BY month) AS baseline_profit
   FROM monthly_profit;
   ```
   **Interpretation:** Retrieves the baseline (first month's) profit.

9. **First Day Website Traffic**
    ```sql
    SELECT page_id,
           day,
           page_views,
           FIRST_VALUE(page_views) OVER (PARTITION BY page_id ORDER BY day) AS first_day_views
    FROM website_traffic;
    ```
    **Interpretation:** Retrieves the first day's page views for each web page.

10. **First Sale Date**
    ```sql
    SELECT product_id,
           sale_date,
           units_sold,
           FIRST_VALUE(sale_date) OVER (PARTITION BY product_id ORDER BY sale_date) AS first_sale_date
    FROM sales;
    ```
    **Interpretation:** Retrieves the first sale date for each product.

### 8. `LAST_VALUE()`

#### What It Is
`LAST_VALUE()` returns the last value in an ordered partition of the result set.

#### When to Use
Use `LAST_VALUE()` when you need to retrieve the last value in an ordered set of rows, often for final comparisons or latest values.

#### Examples

1. **Simple LAST_VALUE**
   ```sql
   SELECT employee_id,
          salary,
          LAST_VALUE(salary) OVER (ORDER BY hire_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the last salary based on hire date.

2. **LAST_VALUE within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          LAST_VALUE(salary) OVER (PARTITION BY department_id ORDER BY hire_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_salary
   FROM employees;
   ```
   **Interpretation:** Retrieves the last salary within each department based on hire date.

3. **Last Sale Amount**
   ```sql
   SELECT sale_id,
          sale_date,
          amount,
          LAST_VALUE(amount) OVER (ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_amount
   FROM sales;
   ```
   **Interpretation:** Retrieves the amount of the last sale.

4. **Last Stock Price**
   ```sql
   SELECT stock_id,
          trade_date,
          closing_price,
          LAST_VALUE(closing_price) OVER (PARTITION BY stock_id ORDER BY trade_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_closing_price
   FROM stock_prices;
   ```
   **Interpretation:** Retrieves the last closing price for each stock.

5. **Last Customer Purchase**
   ```sql
   SELECT customer_id,
          purchase_date,
          LAST_VALUE(purchase_date) OVER (PARTITION BY customer_id ORDER BY purchase_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_purchase_date
   FROM purchases;
   ```
   **Interpretation:** Retrieves the last purchase date for each customer.

6. **Last Employee Promotion**
   ```sql
   SELECT employee_id,
          promotion_date,
          LAST_VALUE(promotion_date) OVER (PARTITION BY employee_id ORDER BY promotion_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_promotion_date
   FROM promotions;
   ```
   **Interpretation:** Retrieves the last promotion date for each employee.

7. **Latest Year Revenue**
   ```sql
   SELECT year,
          revenue,
          LAST_VALUE(revenue) OVER (ORDER BY year ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS latest_revenue
   FROM

 annual_revenue;
   ```
   **Interpretation:** Retrieves the latest (last year's) revenue.

8. **Latest Month Profit**
   ```sql
   SELECT month,
          profit,
          LAST_VALUE(profit) OVER (ORDER BY month ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS latest_profit
   FROM monthly_profit;
   ```
   **Interpretation:** Retrieves the latest (last month's) profit.

9. **Last Day Website Traffic**
    ```sql
    SELECT page_id,
           day,
           page_views,
           LAST_VALUE(page_views) OVER (PARTITION BY page_id ORDER BY day ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_day_views
    FROM website_traffic;
    ```
    **Interpretation:** Retrieves the last day's page views for each web page.

10. **Last Sale Date**
    ```sql
    SELECT product_id,
           sale_date,
           units_sold,
           LAST_VALUE(sale_date) OVER (PARTITION BY product_id ORDER BY sale_date ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING) AS last_sale_date
    FROM sales;
    ```
    **Interpretation:** Retrieves the last sale date for each product.

### 9. `CUME_DIST()`

#### What It Is
`CUME_DIST()` calculates the cumulative distribution of a value in a set of values.

#### When to Use
Use `CUME_DIST()` when you need to determine the relative position of a value within a set, useful for percentiles or distribution analysis.

#### Examples

1. **Simple CUME_DIST**
   ```sql
   SELECT employee_id,
          salary,
          CUME_DIST() OVER (ORDER BY salary) AS cume_dist
   FROM employees;
   ```
   **Interpretation:** Calculates the cumulative distribution of each employee's salary.

2. **CUME_DIST within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          CUME_DIST() OVER (PARTITION BY department_id ORDER BY salary) AS cume_dist
   FROM employees;
   ```
   **Interpretation:** Calculates the cumulative distribution of each employee's salary within each department.

3. **Sales Distribution**
   ```sql
   SELECT sale_id,
          amount,
          CUME_DIST() OVER (ORDER BY amount) AS cume_dist
   FROM sales;
   ```
   **Interpretation:** Calculates the cumulative distribution of each sale amount.

4. **Stock Price Distribution**
   ```sql
   SELECT stock_id,
          closing_price,
          CUME_DIST() OVER (ORDER BY closing_price) AS cume_dist
   FROM stock_prices;
   ```
   **Interpretation:** Calculates the cumulative distribution of each stock's closing price.

5. **Customer Purchase Distribution**
   ```sql
   SELECT customer_id,
          total_purchases,
          CUME_DIST() OVER (ORDER BY total_purchases) AS cume_dist
   FROM customers;
   ```
   **Interpretation:** Calculates the cumulative distribution of each customer's total purchases.

6. **Promotion Distribution**
   ```sql
   SELECT employee_id,
          promotion_date,
          CUME_DIST() OVER (ORDER BY promotion_date) AS cume_dist
   FROM promotions;
   ```
   **Interpretation:** Calculates the cumulative distribution of each employee's promotion date.

7. **Yearly Revenue Distribution**
   ```sql
   SELECT year,
          revenue,
          CUME_DIST() OVER (ORDER BY revenue) AS cume_dist
   FROM annual_revenue;
   ```
   **Interpretation:** Calculates the cumulative distribution of yearly revenue.

8. **Monthly Profit Distribution**
   ```sql
   SELECT month,
          profit,
          CUME_DIST() OVER (ORDER BY profit) AS cume_dist
   FROM monthly_profit;
   ```
   **Interpretation:** Calculates the cumulative distribution of monthly profit.

9. **Website Traffic Distribution**
    ```sql
    SELECT page_id,
           page_views,
           CUME_DIST() OVER (ORDER BY page_views) AS cume_dist
    FROM website_traffic;
    ```
    **Interpretation:** Calculates the cumulative distribution of page views for each web page.

10. **Sales Performance Distribution**
    ```sql
    SELECT product_id,
           units_sold,
           CUME_DIST() OVER (ORDER BY units_sold) AS cume_dist
    FROM sales;
    ```
    **Interpretation:** Calculates the cumulative distribution of units sold for each product.

### 10. `PERCENT_RANK()`

#### What It Is
`PERCENT_RANK()` calculates the percentage rank of a value in a set of values.

#### When to Use
Use `PERCENT_RANK()` when you need to determine the relative standing of a value as a percentage, useful for percentile ranks or comparative analysis.

#### Examples

1. **Simple PERCENT_RANK**
   ```sql
   SELECT employee_id,
          salary,
          PERCENT_RANK() OVER (ORDER BY salary) AS percent_rank
   FROM employees;
   ```
   **Interpretation:** Calculates the percent rank of each employee's salary.

2. **PERCENT_RANK within Partitions**
   ```sql
   SELECT employee_id,
          department_id,
          salary,
          PERCENT_RANK() OVER (PARTITION BY department_id ORDER BY salary) AS percent_rank
   FROM employees;
   ```
   **Interpretation:** Calculates the percent rank of each employee's salary within each department.

3. **Sales Percent Rank**
   ```sql
   SELECT sale_id,
          amount,
          PERCENT_RANK() OVER (ORDER BY amount) AS percent_rank
   FROM sales;
   ```
   **Interpretation:** Calculates the percent rank of each sale amount.

4. **Stock Price Percent Rank**
   ```sql
   SELECT stock_id,
          closing_price,
          PERCENT_RANK() OVER (ORDER BY closing_price) AS percent_rank
   FROM stock_prices;
   ```
   **Interpretation:** Calculates the percent rank of each stock's closing price.

5. **Customer Purchase Percent Rank**
   ```sql
   SELECT customer_id,
          total_purchases,
          PERCENT_RANK() OVER (ORDER BY total_purchases) AS percent_rank
   FROM customers;
   ```
   **Interpretation:** Calculates the percent rank of each customer's total purchases.

6. **Promotion Percent Rank**
   ```sql
   SELECT employee_id,
          promotion_date,
          PERCENT_RANK() OVER (ORDER BY promotion_date) AS percent_rank
   FROM promotions;
   ```
   **Interpretation:** Calculates the percent rank of each employee's promotion date.

7. **Yearly Revenue Percent Rank**
   ```sql
   SELECT year,
          revenue,
          PERCENT_RANK() OVER (ORDER BY revenue) AS percent_rank
   FROM annual_revenue;
   ```
   **Interpretation:** Calculates the percent rank of yearly revenue.

8. **Monthly Profit Percent Rank**
   ```sql
   SELECT month,
          profit,
          PERCENT_RANK() OVER (ORDER BY profit) AS percent_rank
   FROM monthly_profit;
   ```
   **Interpretation:** Calculates the percent rank of monthly profit.

9. **Website Traffic Percent Rank**
    ```sql
    SELECT page_id,
           page_views,
           PERCENT_RANK() OVER (ORDER BY page_views) AS percent_rank
    FROM website_traffic;
    ```
    **Interpretation:** Calculates the percent rank of page views for each web page.

10. **Sales Performance Percent Rank**
    ```sql
    SELECT product_id,
           units_sold,
           PERCENT_RANK() OVER (ORDER BY units_sold) AS percent_rank
    FROM sales;
    ```
    **Interpretation:** Calculates the percent rank of units sold for each product.

---
