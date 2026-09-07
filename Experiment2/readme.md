# Netflix System Design Architecture

A high-level system design architecture for a scalable video streaming platform like Netflix, focusing on high availability, low latency video delivery, and decoupled microservices.

---

## 📌 System Overview

This repository contains the architecture design for a distributed video streaming service built to handle millions of concurrent users with minimal latency and high fault tolerance.


## 🎯 Requirements

### 1. Functional Requirements
* **Video Streaming:** Users can seamlessly stream high-quality video content with adaptive bitrate streaming (ABS).
* **User & Auth Management:** Secure authentication, user profile management, and subscription handling.
* **Content Search & Catalog:** Browsing, searching, and filtering movies and TV shows.
* **Recommendations:** Personalized content recommendations based on viewing history.
* **Push Notifications:** Real-time push notifications for new releases, recommendations, and account alerts.

### 2. Non-Functional Requirements
* **High Availability:** 99.99% uptime to ensure uninterrupted streaming across regions.
* **Low Latency:** Minimal buffering time (<200ms initial playback start time).
* **Scalability:** Ability to handle massive traffic spikes during popular movie/show launches.
* **Data Consistency:** Eventual consistency for video view counts/history; strong consistency for billing and user credentials.

---

## 🧮 Back-of-the-Envelope Estimation

* **Active Users:** ~300 Million total subscribers | **50 Million DAU (Daily Active Users)**
* **Concurrent Users (Peak):** ~10 Million active streaming sessions simultaneously.
* **Video Playback Duration:** Average 1 hour of streaming per user per day.
* **Bandwidth & Storage:**
  * Average bitrate: **5 Mbps** (1080p stream)
  * Peak Bandwidth Required: $10 \text{ Million} \times 5 \text{ Mbps} = \mathbf{50 \text{ Terabits per second (Tbps)}}$
  * Daily Storage (Raw + Encoded Variants): ~100+ TB/day for new master uploads across resolutions (4K, 1080p, 720p).

---

## 🧩 Key Architecture Components

* **Clients (Smart TV / Web / Mobile):** Multi-platform frontend interfaces using Adaptive Bitrate Streaming (HLS/DASH).
* **API Gateway (Zuul / Auth / Routing):** Entry point handling authentication, rate limiting, and request routing.
* **Load Balancer:** Distributes incoming traffic across microservices to maintain system stability.
* **Application Microservices:**
  * **User & Auth Service:** User profile data and access management.
  * **Content & Catalog:** Metadata, search indexing, and content listing.
  * **Video Streaming:** Session management and video file delivery orchestration.
  * **Recommendation Engine:** Machine learning pipelines for customized feeds.
* **Messaging & Processing:** **Apache Kafka** for asynchronous processing, event streaming, and analytics.
* **Caching Layer:** **Redis / EVCache** for fast, low-latency access to user sessions and video metadata.
* **Database Cluster:** **Cassandra** for high-write scalability (user history, video metadata) & **MySQL** for structured user/billing data.
* **Storage & CDN:**
  * **AWS S3:** Primary object storage for raw and transcoded video assets.
  * **CDN (Open Connect):** Edge servers caching video segments close to users to reduce latency and origin server load.