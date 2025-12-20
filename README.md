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
