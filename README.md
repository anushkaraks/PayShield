# PayShield – AI-Powered Parametric Income Protection for Delivery Partners

## 1. Problem Statement

Delivery partners working with platforms such as Swiggy and Zomato depend on daily earnings for their livelihood. Their income is highly vulnerable to external disruptions beyond their control, including:

- Adverse weather conditions (e.g., heavy rainfall, extreme heat, severe pollution)
- Platform outages or server downtime
- Government-imposed restrictions or curfews
- Local strikes and sudden zone closures

These disruptions can lead to immediate and significant income loss, with no reliable system for timely compensation.

---

## 2. Proposed Solution

PayShield is an AI-powered parametric insurance platform designed to protect delivery partners from **loss of income**.

Unlike traditional insurance systems that rely on manual claims, PayShield uses predefined **parametric triggers** to automatically detect disruptions and provide instant compensation based on real-time data.

> **Coverage Scope:** Loss of income only. PayShield strictly excludes health, life, accident, and vehicle repair coverage.

---

## 3. Target Persona

**Category:** Food Delivery Partners (Swiggy / Zomato)

**Example Persona:**  
A delivery partner in Bangalore working approximately 8 hours per day with an average earning of ₹120 per hour. Any disruption in working hours directly impacts daily income.

---

## 4. Application Workflow

1. User registers on the web application (mobile-responsive PWA)
2. Selects delivery platform and inputs average working hours
3. Subscribes to a weekly insurance plan
4. System continuously monitors external data sources via background tasks:
   - Weather conditions (OpenWeatherMap API)
   - Platform uptime (mock API endpoint)
   - Government restrictions (mock/public data feed)
5. When a trigger condition is met:
   - Income loss is calculated automatically
   - Claim is generated and queued for AI-based verification
6. Upon successful verification:
   - Compensation is approved
   - Payment is credited to the user via mock payment gateway (Razorpay test mode)

---

## 5. Weekly Premium Model

PayShield follows a **weekly subscription model**, aligned with the earning cycle of gig workers.

### Available Plans

| Plan     | Weekly Coverage | Weekly Premium |
|----------|----------------|----------------|
| Basic    | ₹1,500         | ₹50            |
| Standard | ₹3,000         | ₹100           |
| Pro      | ₹5,000         | ₹180           |

### Pricing Factors

Premiums are dynamically calculated based on:
- Geographic risk profile (weather patterns, historical disruption frequency)
- Average working hours declared during onboarding
- Historical disruption and claim data for the worker's zone
- Hyper-local risk factors (e.g., waterlogging history, AQI trends)

---

## 6. Parametric Triggers

PayShield eliminates manual claims through predefined, automated trigger conditions evaluated every 15 minutes via a Celery Beat scheduler.

### Weather Trigger
Activated when rainfall exceeds a defined threshold (e.g., above X mm/hour) or AQI crosses a hazardous level, indicating reduced earning potential.

### Platform Downtime Trigger
Activated when delivery platform services are unavailable for more than a defined duration (e.g., 30 minutes), detected via mock uptime API.

### Restriction Trigger
Activated when delivery operations are restricted in a specific region due to government regulations, curfews, or local strikes.

### Extreme Heat Trigger
Activated when temperature exceeds a defined threshold in the worker's operating zone.

---

## 7. AI/ML Integration

PayShield integrates AI/ML components to improve accuracy and efficiency across the platform.

### Premium Calculation
A Random Forest / XGBoost model (scikit-learn) estimates a dynamic risk score using historical weather data, zone disruption frequency, and platform activity trends to adjust weekly pricing.

### Income Prediction
A Linear Regression baseline model estimates expected hourly earnings for the worker's zone and time window, used to accurately quantify income loss during a disruption event.

### Fraud Detection
An Isolation Forest model (scikit-learn) identifies anomalies such as:
- GPS spoofing or inconsistent location data
- Unusual inactivity patterns during claimed disruption windows
- Repeated suspicious claims inconsistent with local weather records
- Duplicate claim attempts within the same trigger window

All models are serialized via pickle and loaded at application startup for low-latency inference.

---

## 8. Platform Choice

**Web Application (Mobile-Responsive PWA)**

Rationale:
- Delivery partners primarily operate via smartphones; a PWA delivers a near-native mobile experience without app store friction
- Enables real-time monitoring, push notifications, and payouts
- Supports seamless integration with GPS and external APIs
- Simplifies deployment and iteration during a 6-week build cycle

---

## 9. Tech Stack

### Backend
- **Python 3.11+**
- **FastAPI** – REST API and webhook endpoints (async, auto-generates Swagger docs at `/docs`)
- **Celery + Redis** – Async task queue for trigger monitoring and claim processing
- **APScheduler / Celery Beat** – Periodic jobs for polling weather and uptime APIs every 15 minutes
- **Pydantic** – Data validation and schema enforcement

### Database
- **PostgreSQL** – Primary relational store (users, policies, claims, payouts) via **SQLAlchemy ORM** + **Alembic** for migrations
- **Redis** – Caching layer for weather/disruption API responses and Celery broker

### AI/ML
- **Scikit-learn** – Premium calculation (Random Forest/XGBoost), income prediction (Linear Regression), fraud detection (Isolation Forest)
- **Pandas + NumPy** – Feature engineering pipeline
- **Pickle** – Model serialization for runtime serving

### Frontend
- **React** (Vite) + **Tailwind CSS** – Worker dashboard and admin panel
- Served as a mobile-responsive PWA

### APIs & Integrations
- **OpenWeatherMap API** (free tier) – Rainfall, temperature, AQI data
- **Google Maps API** – Location services and zone validation
- **Mock Platform Uptime API** – Self-hosted JSON endpoint simulating Swiggy/Zomato status
- **Razorpay Test Mode** – Simulated UPI/payment gateway for instant payout demo

### DevOps
- **Docker + Docker Compose** – Containerized local dev and submission environment
- **GitHub Actions** – CI pipeline (lint + test on push)
- **Render / Railway** – Free-tier deployment for live demo

---

## 10. Development Plan

### Phase 1 – Weeks 1–2 (Seed): Ideation & Foundation
- Research, ideation, and persona validation
- UI/UX design and initial prototype (Figma wireframes)
- FastAPI project scaffold: folder structure, Pydantic schemas, DB models
- PostgreSQL schema and Alembic migrations
- GitHub repository setup with this README

### Phase 2 – Weeks 3–4 (Scale): Automation & Protection
- Backend development: registration, policy management, claims flow
- OpenWeatherMap trigger integration via Celery Beat
- Mock platform downtime and restriction APIs
- Scikit-learn premium calculation model (training + serving)
- Dynamic premium endpoint wired to onboarding flow
- Zero-touch claim process: trigger → auto-claim → AI verification → queue payout

### Phase 3 – Weeks 5–6 (Soar): Scale & Optimise
- Isolation Forest fraud detection layer
- Razorpay test mode integration for simulated instant payouts
- Intelligent dual dashboard:
  - **Worker view:** Earnings protected, active weekly coverage, claim history
  - **Admin/Insurer view:** Loss ratios, disruption heatmaps, predictive claim analytics
- Final submission package: 5-minute demo video + pitch deck (PDF)
- Testing, optimization, and Docker deployment

---

## 11. Adversarial Defense & Anti-Spoofing Strategy

To counter advanced GPS spoofing and coordinated fraud attempts, PayShield implements a **multi-layered defense architecture**.

---

### 11.1 Differentiation Strategy

PayShield uses **multi-signal validation** instead of relying solely on GPS.

A claim is validated only when multiple signals align:

- Location consistency (GPS + IP + network signals)  
- Realistic movement patterns (trajectory, speed, continuity)  
- Behavioral history (working patterns, past activity)  
- Environmental matching (weather and disruption consistency)  

Spoofing attempts are identified through mismatches and unrealistic patterns.

---

### 11.2 Data Signals Used

#### Device & Network Signals

- IP and ISP consistency  
- Cell tower triangulation  
- Device fingerprinting  
- Detection of spoofing/emulator tools  

#### Mobility Data

- Continuous movement tracking  
- Speed and acceleration analysis  
- Route validation using maps  

#### Platform Signals

- Activity vs inactivity patterns  
- Order-level engagement (mock integration)  

#### Environmental Signals

- Weather vs claim density  
- Clustered claims from identical locations  

---

### 11.3 AI/ML Enhancements

Fraud detection is extended using:

- Isolation Forest (baseline anomaly detection)  
- Sequence-based trajectory analysis  
- Cluster anomaly detection for group fraud  

**Composite risk scoring includes:**

- Device trust score  
- Location authenticity score  
- Behavioral consistency score  

---

### 11.4 UX Balance

To ensure fairness:

- **Auto-approved claims:** Low risk  
- **Soft-flagged claims:** Require minimal verification  
- **Hard-flagged claims:** Temporarily held for review  

**Additional safeguards:**

- Grace handling for poor network conditions  
- Historical trust scoring for repeat users  
- Transparent user notifications  

---

### 11.5 Coordinated Fraud Mitigation

- Detection of synchronized claim clusters  
- Rate limiting during mass claims  
- Dynamic trigger validation thresholds  
- Temporary payout throttling in high-risk zones  

---

### 11.6 Outcome

This system ensures:

- Strong resistance to GPS spoofing  
- Accurate claim validation  
- Fair treatment of genuine users  
- Financial sustainability of the platform  
