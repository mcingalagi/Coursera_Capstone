# Coursera_Capstone


WITH ActiveRecords AS (
    SELECT Cust_id,
           Country_type_code,
           COUNT(*) OVER (PARTITION BY Cust_id) AS num_records,
           SUM(CASE WHEN Country_type_code = 123 THEN 1 ELSE 0 END) OVER (PARTITION BY Cust_id) AS num_123
    FROM your_table
    WHERE (end_dt IS NULL OR end_dt > SYSDATE) -- Active records
)
SELECT 
    COUNT(DISTINCT Cust_id) AS total_customers,
    SUM(CASE WHEN num_records > 1 THEN 1 ELSE 0 END) AS customers_with_multiple_active_records,
    SUM(CASE WHEN num_records > 1 AND num_123 > 0 THEN 1 ELSE 0 END) AS customers_with_123_and_multiple_active_records,
    SUM(CASE WHEN num_123 > 0 THEN 1 ELSE 0 END) AS customers_with_123,
    ROUND(SUM(CASE WHEN num_records > 1 THEN 1 ELSE 0 END) / COUNT(DISTINCT Cust_id) * 100, 2) AS percentage_customers_with_multiple_active_records,
    ROUND(SUM(CASE WHEN num_records > 1 AND num_123 > 0 THEN 1 ELSE 0 END) / COUNT(DISTINCT Cust_id) * 100, 2) AS percentage_customers_with_123_and_multiple_active_records,
    ROUND(SUM(CASE WHEN num_123 > 0 THEN 1 ELSE 0 END) / COUNT(DISTINCT Cust_id) * 100, 2) AS percentage_customers_with_123
FROM ActiveRecords;



SELECT Cust_id
FROM (
    SELECT Cust_id, 
           Country_type_code,
           COUNT(*) OVER (PARTITION BY Cust_id) AS num_records,
           SUM(CASE WHEN Country_type_code = 123 THEN 1 ELSE 0 END) OVER (PARTITION BY Cust_id) AS num_123
    FROM your_table
    WHERE end_dt IS NULL OR end_dt > SYSDATE -- Active records
)
WHERE num_records = 2 -- Two records per customer
  AND num_123 = 1; -- One of the records has country code 123


  SELECT Cust_id
FROM your_table
WHERE (end_dt IS NULL OR end_dt > SYSDATE) -- Active records
GROUP BY Cust_id
HAVING COUNT(DISTINCT Country_type_code) = 2 -- Two distinct country codes
   AND SUM(CASE WHEN Country_type_code = 123 THEN 1 ELSE 0 END) = 1; -- One of the country codes is 123

