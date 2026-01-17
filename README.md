
# Contact Centre KPI Analysis (Genesys export + SQL + Power BI)


## Overview
Power BI dashboard built from a Genesys Cloud call-level export (one row per inbound call) to report operational KPIs for a single queue / line of business. The repo includes an anonymised November sample (26k calls) but the approach supports multi-month reporting.



## Problem
- Stakeholders needed a clear, repeatable view of queue performance (volume, abandon rate, queue time, AHT) for one LOB.
- Raw Genesys exports required cleaning and shaping before reliable KPI reporting was possible.
- Needed both headline KPIs and distributions (queue wait/abandon behaviour, handle-time buckets, disconnect types).


## Approach
- **Data prep (Power Query):** cleaned and typed call-level fields and prepared a reporting-ready fact table (`fact_calls.csv`).
- **SQL analysis:** produced daily KPIs, monthly summary, queue-time/abandon distributions, handle-time bucketing, and disconnect breakdowns (see `sql_code.md`).
- **Power BI (model + DAX):** recreated KPI logic as measures and built a single-page dashboard for trend + distribution views.


## Tools
- **Genesys Cloud export**
- **SQL**
- **Power BI**
- **Power Query**
- **DAX**


## Results (November sample)
- Abandon rate remained stable around 9-10% with no major spikes.
- Abandoned calls typically dropped quickly (answered queue time 12s vs abandon time 6s).
- Most common handle-time bucket was 15-30 minutes (25% of calls); 60+ minutes were a small minority.
- Majority of calls ended via customer disconnect; agent disconnects next; system disconnects were a small share.



## Data model
This report uses a single call-level fact table (one row per call from the Genesys export).  
A KPI dictionary is in `metrics.md`.


## Data quality checks
Prep and validation checks are listed in `data_quality.md`.


## Screenshots
![Dashboard](ContactCentreDashboard.png)



## Files
- `ContactCentreDashboard.png` – dashboard screenshot
- `fact_calls.csv` – cleaned call-level dataset (Nov sample)
- `sql_code.md` – SQL used for KPI calculations and distributions
- `metrics.md` – KPI definitions (dictionary)
- `data_quality.md` – cleaning/validation notes





This project was created in response to a request to produce a dashboard showing performance KPIs for a single contact-centre queue / line of business (LOB).  
The goal was to analyse call behaviour, understand key operational metrics, and present the findings clearly for stakeholders.

The dataset used here includes November only for file-size reasons, but the structure supports multi-month reporting.

---

# Project Structure

This repository contains the following files:

- **ContactCentreDashboard.png**  
  Screenshot of the final Power BI dashboard.

- **fact_calls.csv**  
  Cleaned call-level dataset used for SQL analysis and Power BI modelling.

- **sql_code.md**  
  SQL scripts used to calculate KPIs, perform analysis, and create distributions for reporting.

- **README.md**  
  Project overview, explanations, and methodology.

---

# Background

I was asked to provide KPI reporting and insight for a specific inbound queue in the contact centre.  
The underlying call data originated from a Genesys Cloud telephony export, with one row per call containing queue times, handle times, abandon behaviour, and disconnect details.

The requirement was to surface the performance metrics that matter for this LOB, including:

- call volumes  
- abandonment behaviour  
- queue wait patterns  
- handle time patterns  
- how calls were disconnected  

This project covers the full workflow: analysing the exported call-level data in SQL, preparing it for reporting, and building a Power BI dashboard presenting the required KPIs.

---

# Dataset Overview

Each row represents one inbound call. Key fields include:

- **call_date** – Date of the call  
- **handle_seconds** – Total handling duration (talk + hold + ACW)  
- **queue_seconds** – Time spent waiting in the queue  
- **abandoned_flag** – 1 = abandoned in queue, 0 = answered  
- **time_to_abandon_seconds** – Queue time before the caller abandoned  
- **disconnect_type** – How the call ended (customer, agent, system)

Dataset size: more than 26,000 calls for November.

---

# SQL Work

All SQL used in this project is included in:  
`sql_code.md`

The SQL tasks completed were:

### 1. Daily KPI Calculations
- Total calls  
- Abandoned calls  
- Abandon rate  
- Average handle time  
- Queue, talk, hold, ACW times  

### 2. Monthly Summary KPIs
A single aggregated view for the full month.

### 3. Queue Time Analysis
- Average queue time for answered and abandoned calls  
- Distribution of abandon times  
- Percentage of abandons occurring in the first few seconds  

### 4. Handle Time Bucketing
Grouping calls into duration ranges:
- 0–60 seconds  
- 1–5 minutes  
- 5–15 minutes  
- 15–30 minutes  
- 30–60 minutes  
- 60+ minutes  

### 5. Disconnect Type Breakdown
Counts and percentages of how calls ended.

These outputs were then recreated through DAX and visualised in Power BI.

---

# Power BI Dashboard

A screenshot of the final dashboard is included in:  
`ContactCentreDashboard.png`

The dashboard contains the following sections:

### 1. KPI Cards
- Total Calls  
- Abandoned Calls  
- Abandon Rate (%)  

### 2. Daily Trend
Shows how call volume and abandon rate changed across November.

### 3. Average Queue Times
Compares queue time for answered versus abandoned calls.

### 4. Handle Time Distribution
Percentage of calls in each duration bucket.

### 5. Disconnect Type Breakdown
Shows how calls ended: customer disconnect, agent disconnect, or system disconnect.

---

# Key Findings

### 1. Abandon Rate Stability
Abandon rate stays around 9–10% with no major spikes.

### 2. Early Queue Abandonment
- Answered calls queued for roughly 12 seconds  
- Abandoned calls queued for roughly 6 seconds  
Most abandoned calls leave the queue quickly.

### 3. Call Duration Patterns
The most common duration bucket is 15–30 minutes, representing around one quarter of all calls.  
Only a small minority exceed 60 minutes.

### 4. How Calls Ended
Most calls ended with the customer disconnecting, followed by agent disconnects, with system disconnects making up a small portion.

---

# Tools Used

- Genesys Cloud (source of telephony/call-level data)
- SQL
- Power BI (data modelling, DAX, visual design)  
- Power Query  

---

# What This Project Shows

This project covers the full process of working with contact-centre data:

- using SQL to calculate KPIs and explore call patterns  
- preparing data for reporting  
- building calculated columns and measures in Power BI  
- creating a dashboard that summarises performance for one queue/LOB  
- interpreting the results to highlight operational behaviour

---

