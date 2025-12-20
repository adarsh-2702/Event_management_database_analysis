# Event Management System (SQL Project)

## Project Overview
Design and implement a relational database for an **Event Management Platform** that facilitates seamless interaction between users and events. The system tracks the complete lifecycle of event participation—from user registration and ticket pricing to final attendance and feedback collection.

This project demonstrates **SQL proficiency** by covering:
- **Schema Design:** Normalized tables with Primary & Foreign Keys.
- **CRUD Operations:** Handling data insertion, updates, and deletions.
- **Data Analysis:** Complex queries to derive business insights (Revenue, Retention, Churn).

---

## Database Schema

The database `event_management` consists of three relational tables:

1. **Users:** Stores demographic details (Name, City, Age).
2. **Events:** Manages event logistics (Category, Price).
3. **Registrations:** A junction table linking Users and Events, tracking attendance and feedback.

### 1. Schema Creation Code
```sql
CREATE DATABASE event_management;
USE event_management;

CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100),
    city VARCHAR(50),
    age INT
);

CREATE TABLE Events (
    event_id INT PRIMARY KEY AUTO_INCREMENT,
    event_name VARCHAR(100),
    category VARCHAR(50),
    ticket_price DECIMAL(10, 2)
);

CREATE TABLE Registrations (
    registration_id INT PRIMARY KEY AUTO_INCREMENT,
    user_id INT,
    event_id INT,
    feedback_rating INT,
    attended BOOLEAN,
    FOREIGN KEY (user_id) REFERENCES Users(user_id),
    FOREIGN KEY (event_id) REFERENCES Events(event_id)
);
```

## Data & Operations

### 2. Data Seeding
Populating the database with realistic sample data (Indian context).

```sql
-- Users
INSERT INTO Users (name, city, age) VALUES 
('Aarav Sharma', 'Delhi', 25),
('Ishita Iyer', 'Chennai', 22),
('Vihaan Gupta', 'Mumbai', 28),
('Ananya Singh', 'Bangalore', 24),
('Kabir Malhotra', 'Pune', 30),
('Diya Verma', 'Hyderabad', 21);

-- Events
INSERT INTO Events (event_name, category, ticket_price) VALUES 
('Tech Summit 2024', 'Tech', 1500.00),
('Sunburn Festival', 'Music', 3500.00),
('IPL Screening', 'Sports', 500.00),
('Startup Meetup', 'Tech', 800.00),
('Classical Night', 'Music', 1200.00);

-- Registrations
INSERT INTO Registrations (user_id, event_id, feedback_rating, attended) VALUES 
(1, 1, 9, 1), (1, 2, 8, 1), (2, 1, 10, 1), (3, 3, 7, 1), (4, 4, 9, 1),
(5, 5, 6, 0), (6, 2, 10, 1), (2, 4, 8, 1), (3, 1, 9, 1), (4, 2, 7, 0);
```

### 3. Basic CRUD Operations
Demonstrating full lifecycle data management.

```sql
-- CREATE: Insert new user
INSERT INTO Users (name, city, age) VALUES ('Rohan Das', 'Kolkata', 27);

-- READ: Filter high-ticket events
SELECT * FROM Events WHERE ticket_price > 1000 ORDER BY ticket_price DESC LIMIT 3;

-- UPDATE: Modify user details
UPDATE Users SET city = 'Jaipur' WHERE user_id = 1;

-- DELETE: Remove a record
DELETE FROM Registrations WHERE registration_id = 10;
```


## Analytical Insights & Business Impact

The following analysis drives decision-making by evaluating event performance, revenue streams, and user retention.

### 1. Event Popularity & Satisfaction

**Problem:** Identify which events drive the most traffic and highest satisfaction.

* **SQL Logic:** Used `LEFT JOIN` to include events with zero registrations and aggregated data with `COUNT` and `AVG`.
* **Business Value:** Provides a high-level performance overview to spot trends in user preference.
* **Result:**
> **Tech Summit 2024** achieved the highest dual-score (Engagement + Rating).
> **Sunburn Festival** matched in attendance but suffered from lower feedback ratings.



### 2. Identifying "Gold Standard" Events

**Problem:** Which events are exceeding customer expectations (Rating > 8.0)?

* **SQL Logic:** Implemented `HAVING` clause to filter aggregated average ratings.
* **Business Value:** Helps the product team understand what drives high satisfaction (venue, content, or price).
* **Result:**
> **Top Performers:** Tech Summit 2024, Sunburn Festival, and Startup Meetup.
> These events are the benchmark for quality assurance.



### 3. Revenue Analysis

**Problem:** Which events are the most profitable?

* **SQL Logic:** Calculated `COUNT(registrations) * Ticket Price` directly within the `SELECT` statement.
* **Business Value:** Crucial for budget allocation and identifying high-margin categories.
* **Result:**
> **Top Earner:** Sunburn Festival (**10,500** Revenue).
> **Lowest Earner:** IPL Screening (500 Revenue).
> *Insight:* Music events generated **2x more revenue** than Tech events despite similar attendance.



### 4. Category Leaders

**Problem:** Who are the market leaders within their specific niches?

* **SQL Logic:** Used a **Derived Table** (Subquery) to benchmark events against their direct category peers.
* **Business Value:** Ensures fair performance comparisons (e.g., comparing Tech vs. Tech).
* **Result:**
> **Tech Leader:** Tech Summit 2024
> **Music Leader:** Sunburn Festival
> **Sports Leader:** IPL Screening



### 5. User Retention (Power Users)

**Problem:** Who are our recurring customers?

* **SQL Logic:** Filtered for `attended = 1` and used `HAVING COUNT > 1`.
* **Business Value:** Identifies candidates for **VIP Loyalty Programs**.
* **Result:**
> **Loyal Users:** Aarav Sharma, Ishita Iyer, and Vihaan Gupta.
> *Action:* These users should be targeted with "Early Bird" offers for future events.



### 6. Churn Risk Analysis

**Problem:** Who registered but never engaged?

* **SQL Logic:** Utilized a `NOT IN` subquery to isolate zero-attendance users.
* **Business Value:** Highlights "Cold Leads" for re-engagement marketing campaigns.
* **Result:**
> **At-Risk User:** Kabir Malhotra (0% Attendance Rate).



### 7. Premium Segment Identification

**Problem:** Who is willing to pay for the most expensive experiences?

* **SQL Logic:** Dynamic subquery (`ORDER BY ticket_price DESC LIMIT 1`) to find the top-tier event automatically.
* **Business Value:** Segments price-insensitive users for premium upsells.
* **Result:**
> **High-Value Customers:** Aarav Sharma & Diya Verma.



#### 8. User Leaderboard

**Problem:** Who are the most active and critical community members?

* **SQL Logic:** Ranked users by combining volume (`COUNT`) and sentiment (`AVG`) metrics.
* **Business Value:** Gamifies the platform and identifies key influencers.
* **Result:**
> **#1 User:** Ishita Iyer (High attendance + consistent feedback).
> **Top Reviewer:** Diya Verma (Awarded perfect 10s).



### 9. Drop-off Analysis (Registrations vs. Attendance)

**Problem:** Where are we losing users between signup and the actual event?

* **SQL Logic:** Used `SUM(CASE WHEN...)` to compare intended vs. actual behavior in one query.
* **Business Value:** Detects operational failures. High registration + Low attendance = **Poor execution**.
* **Result:**
> **Tech Summit:** 100% Conversion rate.
> **Classical Night:** 0% Conversion rate (1 Registration, 0 Attendees).



### 10. User Commitment Profiling

**Problem:** Which users are "Window Shoppers"?

* **SQL Logic:** Grouped attendance ratios by `user_id`.
* **Business Value:** Distinguishes between committed users and those who flake.
* **Result:**
> **Committed:** Aarav, Ishita, Vihaan (100% Attendance).
> **Flaky:** Ananya Singh (50% Drop-off rate).

---

## Conclusion & Project Takeaways

This project successfully simulated the backend logic of an Event Management System, moving beyond simple data storage to actionable business intelligence. By analyzing user behavior and event performance, I demonstrated how raw data can be transformed into strategic decisions: 

**Revenue Drivers** : Sunburn Festival was the highest revenue generator (10,500 INR), proving that while Tech events drive volume, Music events drive higher profit margins.

**Operational Efficiency**: Identified a critical gap in Classical Night, which suffered a 100% drop-off rate (high registration vs. zero attendance), signaling a need for better event scheduling or reminders.

**Customer Segmentation** : Successfully isolated "Power Users" (loyal attendees) from "At-Risk Users" (no-shows), enabling targeted marketing strategies like VIP loyalty programs and re-engagement campaigns.


## SQL Skills Demonstrated

**1. Database Design** : Implemented Normalization and data integrity using `Primary/Foreign Key` constraints.

**2. Advanced Querying** : Mastered Multi-Table Joins (`INNER`, `LEFT`) to synthesize data from disparate sources.

**3. Data Aggregation** : Utilized `GROUP BY`, `HAVING`, and aggregate functions (`AVG`, `COUNT`) for reporting.

**4. Conditional Logic** : Applied `CASE WHEN` statements to calculate custom metrics like "Attendance Rate" dynamically.

**5. Subqueries** : Wrote efficient nested queries and derived tables for complex filtering and benchmarking.

> Open to feedback and collaboration! If you found this analysis interesting, feel free to reach out.








