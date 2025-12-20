Here is the complete `README.md` file. You can copy the code block below directly into your GitHub repository.

---

```markdown
# 🎟️ Event Management System (SQL Project)

## 📌 Project Overview
**Goal:** Design and implement a relational database for an **Event Management Platform** that facilitates seamless interaction between users and events. The system tracks the complete lifecycle of event participation—from user registration and ticket pricing to final attendance and feedback collection.

This project demonstrates **SQL proficiency** by covering:
- **Schema Design:** Normalized tables with Primary & Foreign Keys.
- **CRUD Operations:** Handling data insertion, updates, and deletions.
- **Data Analysis:** Complex queries to derive business insights (Revenue, Retention, Churn).

---

## 🧱 Database Schema

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

---

## ⚙️ Data & Operations

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

---

## 📊 Analytical Queries & Business Impact

The following 10 queries were written to extract key performance indicators (KPIs) for stakeholders.

### 1. Event Popularity (Demand vs. Attendance)

*Analyzing the drop-off rate between registration and actual attendance.*

```sql
SELECT 
    e.event_name, e.category, 
    COUNT(r.registration_id) AS total_registrations,
    SUM(CASE WHEN r.attended = 1 THEN 1 ELSE 0 END) AS total_attendees
FROM Events e
LEFT JOIN Registrations r ON e.event_id = r.event_id
GROUP BY e.event_id, e.event_name, e.category
ORDER BY total_registrations DESC;

```

> **Insight:** Reveals which events have good marketing (high registrations) but poor execution/timing (low attendance).

### 2. User Participation Analysis

*Distinguishing between active participants and passive registrants.*

```sql
SELECT 
    u.name, u.city, 
    COUNT(r.event_id) AS events_registered,
    SUM(CASE WHEN r.attended = 1 THEN 1 ELSE 0 END) AS events_attended
FROM Users u
JOIN Registrations r ON u.user_id = r.user_id
GROUP BY u.user_id, u.name, u.city
ORDER BY events_attended DESC, events_registered DESC;

```

> **Insight:** Helps segment users into "Committed" vs "Window Shoppers."

### 3. Overall Event Performance

*Aggregating volume and sentiment.*

```sql
SELECT e.event_name, COUNT(r.registration_id) AS total_regs, AVG(r.feedback_rating) AS avg_rating
FROM Events e LEFT JOIN Registrations r ON e.event_id = r.event_id
GROUP BY e.event_id;

```

> **Insight:** A quick snapshot of success metrics to identify flagship events.

### 4. High-Rated Events (> 8.0)

*Filtering for quality assurance.*

```sql
SELECT event_name FROM Events e JOIN Registrations r ON e.event_id = r.event_id
GROUP BY e.event_id HAVING AVG(r.feedback_rating) > 8;

```

> **Insight:** Identifies "Gold Standard" events to replicate in future planning.

### 5. Revenue Calculation

*Financial impact analysis.*

```sql
SELECT event_name, (COUNT(registration_id) * ticket_price) AS revenue
FROM Events e JOIN Registrations r ON e.event_id = r.event_id
GROUP BY e.event_id;

```

> **Insight:** Pinpoints the most profitable events to guide budget allocation.

### 6. Top Events by Category

*Benchmarking against direct competitors.*

```sql
SELECT category, event_name, MAX(avg_r) FROM 
(SELECT category, event_name, AVG(feedback_rating) as avg_r 
 FROM Events JOIN Registrations USING(event_id) GROUP BY event_id) as t
GROUP BY category, event_name;

```

> **Insight:** Ensures fair comparisons (e.g., comparing Tech Summits only to other Tech events).

### 7. Loyal Users (Power Users)

*Identifying retention success.*

```sql
SELECT name FROM Users u JOIN Registrations r ON u.user_id = r.user_id
WHERE r.attended = 1 GROUP BY u.user_id HAVING COUNT(r.event_id) > 1;

```

> **Insight:** Identifies users suitable for VIP programs or loyalty rewards.

### 8. Zero Attendance Users (Churn Risk)

*Finding users who registered but never showed up.*

```sql
SELECT name FROM Users WHERE user_id NOT IN (SELECT user_id FROM Registrations WHERE attended = 1);

```

> **Insight:** Creates a target list for re-engagement email campaigns.

### 9. Premium Users

*Targeting high-value customers.*

```sql
SELECT name FROM Users JOIN Registrations USING(user_id) 
WHERE event_id = (SELECT event_id FROM Events ORDER BY ticket_price DESC LIMIT 1) AND attended = 1;

```

> **Insight:** Segments users who are willing to pay for premium experiences.

### 10. User Leaderboard

*Gamification of user activity.*

```sql
SELECT u.name, COUNT(r.event_id) AS events_attended, AVG(r.feedback_rating) AS avg_given_rating
FROM Users u JOIN Registrations r ON u.user_id = r.user_id
WHERE r.attended = 1
GROUP BY u.user_id
ORDER BY events_attended DESC, avg_given_rating DESC;

```

> **Insight:** Provides a snapshot of the most active community members.

---

```

```
