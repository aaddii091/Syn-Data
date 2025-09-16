# 🧪 Synthetic Data as a Service (MVP)

**Privacy-safe, diverse, and customizable synthetic datasets for training AI models across industries — now with a Vue frontend.**

[![Build](https://img.shields.io/badge/build-passing-brightgreen)](#)
[![License: MIT](https://img.shields.io/badge/license-MIT-informational)](#license)
[![Python](https://img.shields.io/badge/python-3.10%2B-blue)](#-backend-quickstart)
[![Node](https://img.shields.io/badge/node-18%2B-339933)](#-frontend-quickstart)

---

## ✨ Overview

AI models need **large, representative datasets**—but privacy laws, access restrictions, and imbalance make real data hard to use.  
**SDaaS (Synthetic Data as a Service)** generates **realistic, compliant, and configurable synthetic datasets** that preserve statistical properties while eliminating direct identifiers.

This repo contains:
- **Backend (FastAPI)** for schema/templated generation, evaluation, and downloads.
- **Frontend (Vue 3 + Vite)** for an elegant dashboard to configure and launch jobs, monitor status, preview samples, and download datasets.

---

## 🧭 Table of Contents

- [Why Synthetic Data?](#-why-synthetic-data)
- [Industrial Use Cases](#-industrial-use-cases)
- [How It Works](#-how-it-works)
- [MVP Scope & Features](#-mvp-scope--features)
- [Architecture (MVP)](#-architecture-mvp)
- [Repository Structure](#-repository-structure)
- [Backend Quickstart](#-backend-quickstart)
- [Frontend Quickstart](#-frontend-quickstart)
- [API](#-api)
- [Python SDK (MVP)](#-python-sdk-mvp)
- [Templates](#-templates)
- [Evaluation & Metrics](#-evaluation--metrics)
- [Roadmap](#-roadmap)
- [Contributing](#-contributing)
- [License](#-license)

---

## 🔒 Why Synthetic Data?

- **Privacy-Compliant**: Generate data without exposing real user info (GDPR/HIPAA aligned).
- **Balanced & Diverse**: Fix class imbalance and generate rare edge cases.
- **Rapid Iteration**: Remove procurement/governance bottlenecks.
- **Cost-Effective**: No expensive collection/labeling cycles.
- **Configurable**: Control distributions, correlations, sparsity, drift, and noise.

---

## 🏭 Industrial Use Cases

### 🏥 Healthcare
- Synthetic **EHR** tables (patients, visits, labs, meds).
- Rare disease upsampling for diagnostic models.
- De-identified imaging metadata for CV pipelines.

### 💳 Finance
- **Transactions** with merchant/channel/geo/fraud flags.
- Credit risk profiles & repayment behaviors.
- Stress scenarios for robustness testing.

### 🤖 Robotics & Autonomous Systems
- Time-series **sensor feeds** (IMU/LiDAR/GPS) with controllable noise/failures.
- Simulated environments and edge cases for RL.

### 🛒 Retail & Marketing
- **Customer journeys**, product catalogs, clickstreams.
- Demand forecasting with seasonality & promotions.

---

## ⚙️ How It Works

1. **Schema Input (Optional)**  
   Supply a JSON schema (field types, distributions, constraints) **or** pick a built-in template.

2. **Generation**  
   MVP uses **probabilistic samplers** and **tabular generative models** (CTGAN-like) to produce statistically similar data with no direct identifiers.

3. **Validation**  
   Compute **utility** (distributional similarity), **diversity**, and **privacy-lite** metrics (e.g., uniqueness rate).

4. **Delivery**  
   Export to **CSV/JSON**, fetch via **REST API**, or interact in the **Vue dashboard**.

---

## ✅ MVP Scope & Features

- **Schema-Based Generation**: Define fields, types, distributions, constraints.
- **Pre-Built Templates**: `healthcare_ehr`, `finance_transactions`, `robotics_sensors`.
- **REST API**: `/generate`, `/jobs/{id}`, `/download/{id}`, `/preview/{id}`, `/metrics/{id}`.
- **Basic Metrics**: KS for numerics, frequency distance for categoricals, uniqueness rate.
- **Exports**: CSV / JSON.
- **Reproducibility**: Optional `seed`.
- **Vue Frontend**:
  - Wizard to choose template **or** paste schema.
  - Form controls for rows/format/seed.
  - Job list with status chips.
  - Sample preview (first N rows).
  - One-click download & metrics view.

Out of scope (for now): image/video synthesis, formal differential privacy, full RBAC/orgs, multi-tenant billing.

# Syn-Data
