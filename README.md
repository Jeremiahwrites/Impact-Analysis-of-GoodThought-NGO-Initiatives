# GoodThought NGO SQL Data Analysis Project

## Overview

This project explores the operational and funding data of **GoodThought NGO**, an organization committed to driving positive change through **education, healthcare, and sustainable development** initiatives across communities worldwide.

Using a **PostgreSQL database** containing records from **2010 to 2023**, this analysis applies SQL to uncover insights into:

* High-performing assignments
* Donation trends
* Regional project impact
* Donor contribution patterns

The objective is to demonstrate how data-driven decision-making can improve humanitarian project planning and resource allocation.

---

## Database Structure

The database consists of three core tables:

### `Assignments`

Contains details about NGO projects such as:

* Assignment ID
* Assignment Name
* Region
* Budget
* Start Date
* End Date
* Impact Score

### `Donations`

Stores records of donations made toward assignments:

* Donation ID
* Donor ID
* Assignment ID
* Donation Amount
* Donation Date

### `Donors`

Contains donor information:

* Donor ID
* Donor Name
* Donor Type (`Individual` / `Organization`)

---

## Entity Relationship Diagram (ERD)

Relationships:

* One donor can make many donations
* One assignment can receive many donations
* Donations connect donors to assignments

---

# Project Questions Solved

## 1. Highest Donation Assignments

### Objective

Identify the top 5 assignments that received the highest total donation amounts.

### SQL Query

```sql
SELECT ass.assignment_name, 
       ass.region, 
       ROUND(SUM(don.amount),2) AS rounded_total_donation_amount,
       dn.donor_type
FROM assignments AS ass
JOIN donations AS don
     ON ass.assignment_id = don.assignment_id
JOIN donors AS dn
     ON don.donor_id = dn.donor_id
GROUP BY assignment_name, region, donor_type
ORDER BY rounded_total_donation_amount DESC
LIMIT 5;
```

### Results

| Rank | Assignment Name | Region | Total Donation | Donor Type   |
| ---- | --------------- | ------ | -------------- | ------------ |
| 1    | Assignment_3033 | East   | 3840.66        | Individual   |
| 2    | Assignment_300  | West   | 3133.98        | Organization |
| 3    | Assignment_4114 | North  | 2778.57        | Organization |
| 4    | Assignment_1765 | West   | 2626.98        | Organization |
| 5    | Assignment_268  | East   | 2488.69        | Individual   |

### Insights

* East and West regions dominate top-funded assignments.
* Organizations contributed heavily to several top assignments.
* Individual donors also significantly funded some projects.

---

## 2. Top Regional Impact Assignments

### Objective

Find the highest impact assignment in each region and calculate the total donations received.

### SQL Query

```sql
WITH ranked AS (
    SELECT 
        ass.assignment_name,
        ass.region,
        ass.impact_score,
        SUM(don.amount) AS num_total_donations,
        ROW_NUMBER() OVER (
            PARTITION BY ass.region
            ORDER BY ass.impact_score DESC
        ) AS rn
    FROM assignments AS ass
    JOIN donations AS don 
         ON ass.assignment_id = don.assignment_id
    GROUP BY assignment_name, region, impact_score
)

SELECT 
    assignment_name,
    region,
    impact_score,
    num_total_donations
FROM ranked
WHERE rn = 1
ORDER BY region ASC;
```

### Results

| Region | Assignment Name | Impact Score | Total Donations |
| ------ | --------------- | ------------ | --------------- |
| East   | Assignment_316  | 10.00        | 530.81          |
| North  | Assignment_2253 | 9.99         | 872.03          |
| South  | Assignment_3547 | 10.00        | 880.15          |
| West   | Assignment_2794 | 9.99         | 691.58          |

### Insights

* East and South achieved perfect impact scores of **10**.
* South region’s top-impact project also received the highest donations among regional leaders.
* High donations do not always correspond to highest impact scores.

---

# Key Findings

* Funding concentration exists in East and West assignments.
* Organizational donors are major contributors.
* Some lower-funded assignments generated very high impact scores.
* Donation amount alone may not determine project success.

---

# Tools Used

* PostgreSQL
* SQL Joins
* Aggregate Functions
* Window Functions (`ROW_NUMBER`)
* Common Table Expressions (CTEs)

---

# Conclusion

This project highlights the importance of combining **financial metrics** with **impact measurement** when evaluating NGO performance. GoodThought can use these insights to optimize donor engagement, regional planning, and assignment funding strategies.

---

# Author

**Jeremiah Kehinde**

Data Analyst | SQL Enthusiast | Turning Data into Decisions
