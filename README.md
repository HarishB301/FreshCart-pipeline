# FreshCart Pipelines: From Git to Gold

An end-to-end Data Engineering project demonstrating modern data workflows, including version control and CI/CD automation with GitHub Actions, cloud data warehousing with Snowflake, ML orchestration with Mage, and real-time event streaming with RabbitMQ.

---

## Table of Contents
- [Project Overview](#project-overview)
- [Project Components](#project-components)
- [Tech Stack](#tech-stack)
- [Repository Structure](#repository-structure)
- [Prerequisites](#prerequisites)
- [Setup & Installation](#setup--installation)
- [Running the Pipelines](#running-the-pipelines)
- [Testing & CI/CD](#testing--cicd)

---

## Project Overview
**FreshCart Pipelines** implements core data engineering patterns across four integrated modules:
1. **Part A – Git & CI/CD:** Automated unit testing via GitHub Actions on every push and pull request.
2. **Part B – Cloud ELT (Snowflake):** Transforming raw FreshCart retail data into analytics-ready Gold models using Snowflake SQL and Python notebooks.
3. **Part C – ML Orchestration (Mage):** Building and executing an automated Iris classification pipeline inside Mage AI.
4. **Part D – Real-Time Streaming (RabbitMQ):** Dispatching and consuming live food order events asynchronously using RabbitMQ producer (`dispatcher`) and consumer (`driver`) scripts.


---

## Project Components

### 1. Git & CI/CD Automation (`.github/workflows/ci.yml`)
- Enforces clean repository hygiene using `.gitignore` (excluding `.env`, `*.pkl`, `*.csv`, `mage.log`, and `__pycache__/`).
- Uses **GitHub Actions** to automatically run the `pytest` suite whenever changes are pushed or a pull request is opened.

### 2. FreshCart ELT Pipeline (`elt/`)
- **Extract & Load:** Ingests raw FreshCart datasets into Snowflake staging tables via Jupyter/Python notebooks and Snowflake SQL.
- **Transform (Bronze → Silver → Gold):**
  - **Bronze:** Raw data ingestion preserving source records.
  - **Silver:** Data cleaning, deduplication, type casting, and standardization.
  - **Gold:** Business-level aggregations and analytics-ready tables.

### 3. Iris ML Pipeline (`iris_project/`)
- Orchestrates an end-to-end machine learning workflow in **Mage**:
  - **Data Loader:** Loads and validates the Iris dataset.
  - **Transformer:** Prepares features and splits data into training and testing sets.
  - **Exporter / Trainer:** Trains the classification model, evaluates performance, and saves artifacts locally (ignored by Git via `*.pkl`).

### 4. Food Order Streaming (`stream/`)
- Simulates a live food delivery messaging system powered by **RabbitMQ**:
  - **Dispatcher (`dispatcher.py`):** Publishes simulated customer food order payloads to the RabbitMQ queue.
  - **Driver (`driver.py`):** Subscribes to the queue in real time, receives incoming orders, and processes delivery updates.

---

## Tech Stack
- **Language:** Python 3.9+
- **Cloud Data Warehouse:** Snowflake (Snowflake SQL)
- **Workflow Orchestration:** Mage AI
- **Message Broker:** RabbitMQ (`pika`)
- **Testing & CI/CD:** Pytest, Git, GitHub, GitHub Actions

---

## Repository Structure
```text
freshcart-pipelines/
├── README.md                   # Project documentation
├── .gitignore                  # Ignores .env, *.pkl, *.csv, mage.log, __pycache__/
├── requirements.txt            # Project Python dependencies
├── elt/                        # Part B: FreshCart Snowflake ELT notebook & SQL scripts
├── iris_project/               # Part C: Mage AI project for the Iris ML pipeline
├── stream/                     # Part D: RabbitMQ dispatcher (producer) & driver (consumer) scripts
├── tests/
│   └── test_prepare_data.py    # Unit tests for data preparation logic
└── .github/
    └── workflows/
        └── ci.yml              # GitHub Actions CI workflow running pytest
```

---

## Prerequisites
Before running the pipelines locally, make sure you have:
- **Python 3.9+** and `pip`
- **Git**
- **Snowflake Account** (with active warehouse, database, and credentials)
- **RabbitMQ Server** (running locally via Docker or native service)
- **Jupyter Notebook / VS Code** (to run the `elt/` notebook)

---

## Setup & Installation

### 1. Clone the Repository
```bash
git clone git@github.com:HarishB301/FreshCart-pipeline.git
cd FreshCart-pipeline
```

### 2. Create and Activate a Virtual Environment
```bash
# Create virtual environment
python -m venv venv

# Activate on Windows
venv\Scripts\activate

# Activate on macOS/Linux
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install -r requirements.txt
```

### 4. Configure Environment Variables
Create a `.env` file in the root directory (automatically ignored by `.gitignore`) and add your Snowflake and RabbitMQ settings:
```env
SNOWFLAKE_ACCOUNT=your_account_identifier
SNOWFLAKE_USER=your_username
SNOWFLAKE_PASSWORD=your_password
SNOWFLAKE_WAREHOUSE=COMPUTE_WH
SNOWFLAKE_DATABASE=FRESHCART_DB
SNOWFLAKE_SCHEMA=PUBLIC

RABBITMQ_HOST=localhost
RABBITMQ_QUEUE=food_orders
```

---

## Running the Pipelines

### Part B: FreshCart ELT Pipeline (`elt/`)
1. Launch Jupyter Notebook or open the `elt/` folder in VS Code.
2. Open the FreshCart ELT notebook, connect to your Snowflake environment using your `.env` credentials, and run all cells sequentially from Bronze ingestion to Gold transformations.

### Part C: Iris ML Pipeline (`iris_project/`)
1. Start the Mage AI server from the repository root:
   ```bash
   mage start iris_project
   ```
2. Open `http://localhost:6789` in your browser.
3. Open the Iris ML pipeline and trigger a run (**Run@once**) to execute the loader, transformer, and model training blocks.

### Part D: Food Order Streaming (`stream/`)
1. Ensure your local RabbitMQ broker is running on `localhost:5672`.
2. Open two terminals with your virtual environment activated:
   - **Terminal 1 (Start Driver / Consumer):**
     ```bash
     python stream/driver.py
     ```
   - **Terminal 2 (Start Dispatcher / Producer):**
     ```bash
     python stream/dispatcher.py
     ```

---

## Testing & CI/CD
To run the unit tests locally:
```bash
pytest -v
```
Every push and pull request automatically executes `.github/workflows/ci.yml` on GitHub Actions to run `tests/test_prepare_data.py` and verify code integrity.
