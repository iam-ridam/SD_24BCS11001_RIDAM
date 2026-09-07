# URL Shortener — System Design & Capacity Estimation

This repository contains the system design specifications and back-of-the-envelope capacity estimations for a highly scalable **URL Shortener Service** (e.g., TinyURL / Bitly).

---

## 📋 System Requirements

### Functional Requirements
* **URL Shortening:** Convert a long original URL into a unique, shortened URL.
* **Redirection:** Redirect user requests from the short URL to the original destination link.
* **Custom Expiration:** Allow users to set a custom expiration time (TTL) for generated short links.

### Non-Functional Requirements
* **Uniqueness:** Each generated short URL must be unique to prevent collisions.
* **Low Latency:** High-performance read execution for redirections ($50\text{ms}$ to $100\text{ms}$).
* **High Availability:** The redirection service must be highly available with minimal downtime.

---

## 📐 Capacity Planning & Estimations

### 1. Scale & Traffic Assumptions
* **Total Registered Users:** $1\text{ Billion}$
* **Daily Active Users (DAU):** $200\text{ Million}$
* **User Request Profile:** 
  * **Reads:** $40\text{ reads/user/day}$
  * **Writes:** $10\text{ writes/user/day}$
  * **Read/Write Ratio:** $4 : 1$

---

### 2. Throughput & Application Server Sizing
* **Total Daily Requests:**
  $$\text{Total Requests} = 200\text{M DAU} \times (40\text{ Reads} + 10\text{ Writes}) = 10\text{ Billion requests/day}$$

* **Throughput (QPS):**
  $$\text{QPS} = \frac{10\text{ Billion}}{86,400\text{ seconds}} = 125,000\text{ requests/second}$$

* **Application Servers Required:**
  * **Assumptions:** $1\text{ server} = 100\text{ concurrent threads}$, Average Latency = $500\text{ms}$ ($0.5\text{s}$)
  $$\text{Required Servers} = \frac{125,000\text{ QPS} \times 0.5\text{s}}{100\text{ threads/server}} = \mathbf{625\text{ Servers}}$$

---

### 3. Data Storage Estimation (5-Year Horizon)
* **Daily Writes:**
  $$\text{Daily Writes} = 200\text{M DAU} \times 10\text{ writes} = 2\text{ Billion new URLs/day}$$

* **Daily Storage Added:**
  * **Assumption:** $1\text{ URL record} = 1\text{ KB}$ (URL string, hash, expiration metadata, user ID)
  $$\text{Daily Storage} = 2\text{ Billion URLs} \times 1\text{ KB} = \mathbf{2\text{ TB / day}}$$

* **5-Year Persistent Storage:**
  $$\text{Storage (5 Years)} = 2\text{ TB/day} \times 365\text{ days/year} \times 5\text{ years} = \mathbf{3.7\text{ PB}}$$

---

### 4. Memory (RAM) & Caching Estimation
To achieve low latency ($<100\text{ms}$), hot URL data is cached using the **80/20 Pareto Principle** (20% of hot URLs generate 80% of read traffic).

* **Daily Read Operations:**
  $$\text{Reads/day} = 200\text{M DAU} \times 40\text{ reads} = 8\text{ Billion reads/day}$$

* **Daily Read Data Volume:**
  $$\text{Read Volume} = 8\text{ Billion reads} \times 1\text{ KB} = 8\text{ TB/day}$$

* **Base Daily Hot Cache RAM (20% Hot Data):**
  $$\text{Base Daily RAM} = 20\% \times 8\text{ TB} = \mathbf{1.6\text{ TB RAM}}$$

* **Projected Cache RAM Growth (5 Years @ 20% YoY Growth):**
  $$\text{Projected RAM (5 Years)} = \mathbf{3.3\text{ TB RAM}}$$

* **Cache Cluster Node Count:**
  * Using standard memory-optimized instances (**64 GB RAM per node**):
  $$\text{Cache Nodes} = \frac{3.3\text{ TB} \times 1024\text{ GB}}{64\text{ GB per node}} \approx \mathbf{106\text{ Cache Servers}}$$

---

## 📊 Summary Metrics

| Metric | Estimated Value |
| :--- | :--- |
| **Total Daily Requests** | 10 Billion / day |
| **Throughput (QPS)** | 125,000 QPS |
| **Application Server Count** | 625 Servers |
| **Daily Data Ingestion** | 2 TB / day |
| **Data Storage (5-Year)** | 3.7 PB |
| **Cache Memory (5-Year)** | 3.3 TB RAM |
| **Cache Cluster Nodes (64GB RAM)** | 106 Servers |