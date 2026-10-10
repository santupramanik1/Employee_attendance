# Employee Attendance & Analytics API

## Overview
This repository contains the completed backend API for the HROne Employee Attendance System. Built with FastAPI and backed by MongoDB, this system handles employee management, attendance records (punch-in/out), regularization of anomalies, and complex departmental analytics.

## Prerequisites
- Python 3.11+
- MongoDB 6.0 or newer (Atlas free tier or local Docker)

## Local Setup

1. Create a virtual environment and activate it:
```bash
python -m venv .venv
source .venv/bin/activate  # On Windows use: .venv\Scripts\activate
```

2. Install dependencies:
```bash
pip install -r requirements.txt
```

3. Set up the `.env` file for MongoDB connection details:
```bash
cp .env.example .env
```
Ensure that `MONGO_URI` and `MONGO_DB` are properly configured.

4. (Optional) Load sample seed data into the database:
```bash
python sample_seed.py
```

5. Start the FastAPI application:
```bash
uvicorn app.main:app --port 8000 --reload
```
The server should now be running on `http://127.0.0.1:8000`.

## Highlights
- Safely handles concurrency and race conditions when creating employees or punching in through MongoDB unique indexes.
- Includes complex aggregation pipelines to generate monthly trends, late leaderboards, and department summaries.
- Handles timezones correctly, standardizing all instant interactions through UTC and computing differences correctly across mid-night shift transitions.
