# Application Setup Guide

## 1. Installation
```bash
# Clone repository
git clone <repo_url>
cd infloo_django

# Create Virtual Environment
python3 -m venv venv
source venv/bin/activate

# Install Dependencies
pip install -r requirements.txt
```

## 2. Database Setup
```bash
# Run Migrations
python manage.py migrate
```

## 3. Role & Permission Initialization
We use a role-based system (Compliance, Community Manager, etc.).
Run this **once** to set up default Groups and Permissions:
```bash
python manage.py setup_roles
```

## 4. Populating Demo Data (Optional)
To test the Dashboard with fake users, events, and connections:
```bash
# This creates 'test_valid_user_99' with events and followers
python scripts/data/populate_demo.py
```

## 5. Running the Server
```bash
# Needs Redis running for WebSockets
python manage.py runserver
```
