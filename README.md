# 🚀 Airflow ETL Pipeline: NASA APOD Data Integration

A complete **Extract-Transform-Load (ETL)** pipeline that fetches NASA's Astronomy Picture of the Day data, processes it, and stores it in PostgreSQL using Apache Airflow orchestration.

---

## 🎯 Overview

This project demonstrates a production-ready ETL workflow using:
- **Apache Airflow** - Orchestration & scheduling
- **PostgresSQL** - Data storage
- **NASA APOD API** - Data source (Get the api here https://api.nasa.gov/#browseAPI)
- **Docker** - Containerized environment
- **DBeaver** - Database visualization

---

## 📦 Tech Stack

| Component | Purpose |
|-----------|---------|
| **Airflow** | Workflow orchestration & task scheduling |
| **PostgreSQL** | Data warehouse |
| **Docker** | Runtime environment isolation |
| **HttpOperator** | API data extraction |
| **PostgresHook** | Database connectivity |

---

## 🔄 ETL Pipeline Flow

### 1️⃣ **Extract**
- Calls NASA APOD API via `HttpOperator`
- Fetches daily astronomy data in JSON format
- Captures: title, explanation, URL, date, media type

### 2️⃣ **Transform**
- Processes JSON response using TaskFlow API (`@task` decorator)
- Extracts relevant fields
- Ensures data format compatibility with database schema

### 3️⃣ **Load**
- Inserts transformed data into PostgreSQL
- Auto-creates `apod_data` table if it doesn't exist
- Uses `PostgresHook` for secure database operations

---

## 📋 Database Schema

```sql
CREATE TABLE apod_data (
    id SERIAL PRIMARY KEY,
    title VARCHAR(255),
    explanation TEXT,
    url TEXT,
    date DATE,
    media_type VARCHAR(50)
);
```

---

## 🏗️ Architecture

```mermaid
graph TD
    Start([Daily Trigger]) --> CreateTable["Create Table<br/>(if not exists)"]
    CreateTable -->|Success| Extract["Extract<br/>Fetch NASA APOD API<br/>via HttpOperator"]
    CreateTable -->|Error| Error["❌ Error<br/>Log & Alert"]
    
    Extract -->|JSON Response| Transform["Transform<br/>Parse JSON<br/>Extract Relevant Fields<br/>via @task decorator"]
    Extract -->|Error| Error
    
    Transform -->|Validated Data| Load["Load<br/>Insert into PostgreSQL<br/>via PostgresHook"]
    Transform -->|Error| Error
    
    Load -->|Success| Complete["✅ Complete<br/>Data stored in DB"]
    Load -->|Error| Error
    
    Error --> End([Pipeline End])
    Complete --> End
    
    style Start fill:#90EE90
    style Complete fill:#90EE90
    style End fill:#FFE4B5
    style Error fill:#FFB6C6
    style Extract fill:#87CEEB
    style Transform fill:#87CEEB
    style Load fill:#87CEEB
    style CreateTable fill:#87CEEB
```

---

## 🚦 Getting Started

### 1. Start Airflow
```bash
astro dev start
```

### 2. Create Connections in Airflow UI
- Navigate to **Admin > Connections**
- Add `nasa_api` (HTTP connection)
- Add `my_postgres_connection` (PostgreSQL connection)

### 3. Monitor & Trigger DAG
- Access Airflow UI at `http://localhost:8080`
- Find `nasa_apod_postgres` DAG
- Trigger and monitor execution

### 4. View Results in DBEaver
- Connect to PostgreSQL database
- Query the `apod_data` table
- Analyze extracted astronomy data

---

## ✨ Key Features

✅ **Automated daily data extraction**  
✅ **Error handling & retry logic**  
✅ **Task dependency management**  
✅ **Data validation & transformation**  
✅ **Scalable & containerized architecture**  

---

## 📁 Project Structure

```
nasa_api_project/
├── dags/
│   └── etl.py              # Main DAG definition
├── Dockerfile              # Container configuration
├── requirements.txt        # Python dependencies
├── docker-compose.yml      # Service orchestration
└── airflow_settings.yaml   # Airflow configuration
```

---

## 🔧 Dependencies

- `apache-airflow`
- `apache-airflow-providers-http`
- `apache-airflow-providers-postgres`
- `psycopg2-binary`

---

## 📊 Component Interaction

```mermaid
graph LR
    API["🌌 NASA APOD API"] -->|HTTP Request| Airflow["Apache Airflow<br/>Orchestrator"]
    Airflow -->|HttpOperator| Extract["Extract Stage"]
    Extract -->|TaskFlow API| Transform["Transform Stage"]
    Transform -->|PostgresHook| DB[("PostgreSQL<br/>Database")]
    DB -->|Query| DBeaver["🔍 DBeaver<br/>Visualization"]
    
    style API fill:#FFD700
    style Airflow fill:#5B9BD5
    style Extract fill:#87CEEB
    style Transform fill:#87CEEB
    style DB fill:#90EE90
    style DBeaver fill:#FFB6C6
```

---

## 🛠️ Troubleshooting

**Connection types missing?**
- Ensure provider packages are in `requirements.txt`
- Run `astro dev stop && astro dev start`

**Database connection errors?**
- Verify PostgreSQL connection credentials in Airflow UI
- Check Docker container status with `docker ps`

---

## 📚 Resources

- [Apache Airflow Documentation](https://airflow.apache.org/docs/)
- [Airflow HTTP Provider](https://airflow.apache.org/docs/apache-airflow-providers-http/stable/)
- [Airflow PostgreSQL Provider](https://airflow.apache.org/docs/apache-airflow-providers-postgres/stable/)
- [NASA APOD API](https://api.nasa.gov/)
- [Astronomer CLI Docs](https://docs.astronomer.io/)

---

**Happy Data Pipelining! 🎉**
