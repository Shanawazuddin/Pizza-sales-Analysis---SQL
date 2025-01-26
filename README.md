# 🍕 Pizza Sales Analysis - SQL Project

## 📖 Overview
This project dives into the **Pizza Sales Dataset** to uncover valuable insights about pizza orders, customer preferences, revenue trends, and more using **SQL**. By addressing key business questions, this analysis provides actionable insights that can help a pizza business optimize its operations, enhance customer satisfaction, and drive revenue.

---

## 🔍 Key Questions Answered
### **Basic Analysis**
1. **How many orders were placed?**
2. **What is the total revenue generated from pizza sales?**
3. **Which is the highest-priced pizza?**
4. **What is the most common pizza size ordered?**
5. **Which are the top 5 most ordered pizza types?**

### **Intermediate Analysis**
6. **What is the total quantity of each pizza category ordered?**
7. **How are orders distributed by the hour of the day?**
8. **What is the category-wise distribution of pizzas?**
9. **What is the average number of pizzas ordered per day?**
10. **Which are the top 3 pizza types based on revenue?**

### **Advanced Analysis**
11. **What percentage does each pizza type contribute to total revenue?**
12. **What is the cumulative revenue generated over time?**
13. **What are the top 3 pizza types by revenue for each category?**

---

## 🛠 Technologies Used
- **SQL**: For querying and analyzing data.
- **Database**: MySQL.
- **Tools**: SQL Workbench for query execution.

---

## ⚡️ Insights Uncovered
- **Total Orders**: The business processed **21,350 orders**.
- **Total Revenue**: The total revenue generated is **$291,191**.
- **Most Popular Pizza**: The **Barbecue Chicken Pizza** is a customer favorite with 903 orders.
- **Top Revenue Generators**:
- **Barbecue Chicken Pizza**: $15,949.25
- **Thai Chicken Pizza**: $15,184.75
- **California Chicken Pizza**: $14,527.25
- **Peak Ordering Hour**: Most orders are placed at **12 PM**.
- **Top Pizza Category**: **Classic pizzas** are the most ordered, with **5,232 pizzas sold**.

---

## 🎯 Key SQL Queries

Below is the SQL code used to answer the key business questions. Feel free to copy and run these queries in your own environment for practice or further analysis. 

### **Database Setup**
```sql
CREATE DATABASE PIZZAHUT;
USE PIZZAHUT;

CREATE TABLE orders (
    order_id INT NOT NULL PRIMARY KEY,
    date DATE NOT NULL,
    time TIME NOT NULL
);

CREATE TABLE order_details (
    order_details_id INT NOT NULL PRIMARY KEY,
    order_id INT NOT NULL,
    pizza_id VARCHAR(50) NOT NULL,
    quantity INT NOT NULL
);

CREATE TABLE pizzas (
    pizza_id VARCHAR(50) NOT NULL PRIMARY KEY,
    pizza_type_id VARCHAR(50) NOT NULL,
    size VARCHAR(2) NOT NULL,
    price DECIMAL(6, 2) NOT NULL
);

CREATE TABLE pizza_types (
    pizza_type_id VARCHAR(50) NOT NULL PRIMARY KEY,
    name VARCHAR(100),
    category VARCHAR(50)
);
```

### **Key Queries**
#### 1️⃣ Total Number of Orders
```sql
SELECT 
    COUNT(order_id) AS total_orders
FROM
    orders;
-- Result: 21,350
```

#### 2️⃣ Total Revenue
```sql
SELECT 
    ROUND(SUM(price * quantity), 2) AS total_revenue
FROM
    pizzas
JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id;
-- Result: $291,191
```

#### 3️⃣ Highest Priced Pizza
```sql
SELECT 
    name, price
FROM
    pizzas
JOIN
    pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
ORDER BY price DESC
LIMIT 1;
-- Result: The Greek Pizza - $35.95
```

#### 4️⃣ Most Common Pizza Size Ordered
```sql
SELECT 
    size AS common_size,
    COUNT(order_details_id) AS quantity_ordered
FROM
    pizzas
JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY size
ORDER BY quantity_ordered DESC
LIMIT 1;
-- Result: Size L - 6,592 orders
```

#### 5️⃣ Top 5 Most Ordered Pizza Types
```sql
SELECT 
    pizza_types.pizza_type_id,
    name,
    SUM(order_details.quantity) AS quantity
FROM
    pizza_types
JOIN
    pizzas ON pizzas.pizza_type_id = pizza_types.pizza_type_id
JOIN
    order_details ON order_details.pizza_id = pizzas.pizza_id
GROUP BY pizza_types.pizza_type_id, name
ORDER BY quantity DESC
LIMIT 5;
-- Results: Barbecue Chicken (903), Hawaiian (873), Pepperoni (858), Classic Deluxe (835), Thai Chicken (833)
```

#### 6️⃣ Total Quantity of Each Pizza Category Ordered
```sql
SELECT 
    category AS pizza_category, 
    SUM(quantity) AS quantity
FROM
    pizza_types
JOIN
    pizzas ON pizza_types.pizza_type_id = pizzas.pizza_type_id
JOIN
    order_details ON pizzas.pizza_id = order_details.pizza_id
GROUP BY category;
-- Results: Classic (5,232), Veggie (4,219), Supreme (4,241), Chicken (3,957)
```

#### 7️⃣ Orders Distribution by Hour
```sql
SELECT 
    HOUR(time) AS order_hour, 
    COUNT(order_id) AS order_count
FROM
    orders
GROUP BY order_hour
ORDER BY order_hour;
-- Peak hour: 12 PM (2,520 orders)
```

#### 8️⃣ Cumulative Revenue Over Time
```sql
SELECT 
    DATE(date) AS order_date, 
    ROUND(SUM(revenue) OVER (ORDER BY date), 2) AS cumulative_revenue
FROM
    (SELECT 
        date, 
        SUM(quantity * price) AS revenue
     FROM
        orders
     JOIN order_details ON orders.order_id = order_details.order_id
     JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
     GROUP BY date) AS daily_revenue;
```

#### 9️⃣ Top 3 Pizzas by Revenue in Each Category
```sql
SELECT 
    category, 
    name, 
    revenue, 
    rn 
FROM 
    (SELECT 
        category, 
        name, 
        revenue, 
        RANK() OVER (PARTITION BY category ORDER BY revenue DESC) AS rn
     FROM
        (SELECT 
            pizza_types.pizza_type_id, 
            pizza_types.category, 
            pizza_types.name, 
            SUM(quantity * price) AS revenue
         FROM
            orders
         JOIN order_details ON orders.order_id = order_details.order_id
         JOIN pizzas ON order_details.pizza_id = pizzas.pizza_id
         JOIN pizza_types ON pizzas.pizza_type_id = pizza_types.pizza_type_id
         GROUP BY pizza_types.pizza_type_id, pizza_types.category, pizza_types.name) AS revenue_data) AS ranked_data
WHERE rn <= 3;
```

---

## 🚀 Future Enhancements
- Integrate customer demographics to offer personalized pizza recommendations.
- Analyze customer feedback to enhance pizza quality and service.
- Predict sales trends using machine learning models.

---

### 💻 How to Use This Project
1. Clone the repository and set up the database using the schema provided.
2. Import the **Pizza Sales Dataset** into your SQL environment.
3. Run the queries in `queries.sql` to explore the insights.
4. Extend the analysis to add more business questions or integrate new data.

