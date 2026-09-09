<!--
  Portfolio vitrine — Pierre Deffrennes, Data Engineer
  Fichier prêt à committer à la racine d'un repo dédié
  (suggestion de nom : pierre-deffrennes-data-engineer).
-->

<h1 align="center">Pierre Deffrennes — Data Engineer</h1>

<p align="center">
  <em>Building reliable, tested and automated data platforms — from ingestion to ML &amp; LLM serving.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Python-3776AB?logo=python&logoColor=white" alt="Python">
  <img src="https://img.shields.io/badge/SQL-4479A1?logo=postgresql&logoColor=white" alt="SQL">
  <img src="https://img.shields.io/badge/dbt-FF694B?logo=dbt&logoColor=white" alt="dbt">
  <img src="https://img.shields.io/badge/Airflow-017CEE?logo=apacheairflow&logoColor=white" alt="Airflow">
  <img src="https://img.shields.io/badge/Spark-E25A1C?logo=apachespark&logoColor=white" alt="Spark">
  <img src="https://img.shields.io/badge/AWS-232F3E?logo=amazonwebservices&logoColor=white" alt="AWS">
  <img src="https://img.shields.io/badge/Docker-2496ED?logo=docker&logoColor=white" alt="Docker">
  <img src="https://img.shields.io/badge/MongoDB-47A248?logo=mongodb&logoColor=white" alt="MongoDB">
  <img src="https://img.shields.io/badge/FAISS%20%2F%20RAG-0467DF?logo=meta&logoColor=white" alt="RAG">
</p>

<p align="center">
  📍 Douai · Lille area, France &nbsp;·&nbsp;
  <a href="https://www.linkedin.com/in/pierre-deffrennes-80b1215b/">LinkedIn</a> &nbsp;·&nbsp;
  <a href="mailto:pierre.deffrennes@gmail.com">Email</a> &nbsp;·&nbsp;
  <a href="https://github.com/PierreDff">GitHub</a>
</p>

---

## 👋 About

Data Engineer trained through **OpenClassrooms'** master's-level program — 13 projects over 9 months, covering the full data lifecycle: ingestion, transformation, orchestration, streaming, machine learning and LLM/RAG systems, deployed on cloud infrastructure.

This portfolio highlights **six projects** that together span the whole data-engineering chain.

---

## 🚀 Featured projects

| Project | What it proves | Core stack | |
|---|---|---|---|
| **[GreenCoop Data Platform](#-greencoop-data-platform--p8)** | Ingestion &amp; ELT | Airbyte · dbt · AWS | [repo →](https://github.com/PierreDff/OC_DE_projet8) |
| **[PulsEvents RAG](#-pulsevents-rag--p11)** | RAG / LLM system | FAISS · Mistral · CI | [repo →](https://github.com/PierreDff/OC_DE_projet11) |
| **[Avantages Sportifs](#-avantages-sportifs--p12)** | Orchestration &amp; Spark | Airflow · Spark · Soda | [repo →](https://github.com/PierreDff/OC_DE_projet12) |
| **[Building Energy — ML + serving](#-building-energy--ml--serving--p6)** | ML + model serving | scikit-learn · BentoML | [repo →](https://github.com/PierreDff/OC_DE_projet6) |
| **[POC Tickets — Streaming](#-poc-tickets--real-time-streaming--p9)** | Real-time streaming | Redpanda · PySpark | [repo →](https://github.com/PierreDff/OC_DE_projet9) |
| **[MongoDB Cluster](#-mongodb-cluster--distributed-nosql--p7)** | Distributed NoSQL | Sharding · Replica sets | [repo →](https://github.com/PierreDff/OC_DE_projet7) |

---

### ⚡ GreenCoop Data Platform — P8
**End-to-end ELT pipeline on AWS** · *"Build and test a data infrastructure"*

`12 dbt models` · `133/133 dbt build PASS` · `×5 query speed-up` · `≤ 1 h data freshness`

**Context.** GreenAndCoop, a cooperative renewable-electricity supplier in the Hauts-de-France region, forecasts electricity demand ("Forecast 2.0"). The Data Science team needed reliable, unified weather data — but the sources were heterogeneous (InfoClimat API + Weather Underground Excel exports) and unvalidated.

**Role.** Designed and built the full ELT platform solo: from raw ingestion to a tested dimensional warehouse, then containerized and deployed to AWS with hourly scheduling and monitoring.

**Architecture.** ELT — 3 sources → **Airbyte** → `raw` → **dbt** across `staging → intermediate → marts` (star schema). In production: Airbyte + RDS PostgreSQL 16 on AWS; dbt containerized (ECR) and run **hourly by an ECS Fargate** scheduled task; logs to CloudWatch.

```
fact_observation_meteo (~4,950 rows)
   ├── station_sk → dim_weather_stations (6 stations)
   └── date_sk    → dim_date (731 days)
```

**Key results.**
- Ingested **~4,950 hourly observations** from 3 sources (InfoClimat — 4 stations, 1,143 obs · Weather Underground — 2 stations, ~3,800 obs)
- **12 dbt models** · **120+ quality tests** incl. 4 custom singular tests — full `dbt build`: **133/133 nodes PASS**
- **8 indexes** on the marts layer — measured query speed-up up to **×5** (`EXPLAIN ANALYZE`, best-of-3)
- **Hourly automated refresh** on ECS Fargate, ≤ 1 h freshness, monitored via CloudWatch

**Skills.** Formalize processing/storage processes · install &amp; configure a DBMS + extraction tool · establish and run a DBMS testing process · model an IS-compatible infrastructure.

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet8)**

---

### 🔎 PulsEvents RAG — P11
**Cultural-event recommendation chatbot (POC)** · *"Design and deploy a RAG system"*

`recall@5 = 1.00` · `faithfulness 0.94 / answer_relevancy 0.93` · `100% abstention on traps` · `~1.2 s / question`

**Context.** Puls-Events wanted to prove the feasibility of a natural-language chatbot recommending cultural events (from public Open Agenda data) before scaling up.

**Role.** Built the full RAG pipeline solo: server-side-filtered collection, HTML cleaning, chunking, embeddings, vector index, retrieval-generation chain, and an annotated evaluation set.

**Architecture.** OpenDataSoft API (server-side ODSQL geo + rolling-year filter) → clean / chunk → `mistral-embed` (1024-dim) → **FAISS** index → **LangChain** retrieval → generation with `mistral-small-2506` (temp 0.2). Streamlit UI.

**Key results.**
- Scoped to **Lille (3,642 events)** from a **~1.18 M-record** national dataset — filtered **server-side** (geo + last-year window)
- **Retrieval — recall@5 = 1.00** on factual questions (every expected event retrieved); lower on thematic/temporal (0.24 / 0.29), an honestly documented semantic-only limit
- **Generation (RAGAS-style LLM-judge)** — faithfulness **0.94**, answer_relevancy **0.93**; **100% abstention** on trap questions (no hallucination)
- **~1.2 s / question** — FAISS search only 2–4 ms; the bottleneck is the Mistral API call
- Chunking tuned (1.58 → 1.24 chunks/event) · **13 pytest tests + GitHub Actions CI** · pinned lockfile, secrets out of git

**Skills.** Collect &amp; clean data · configure the environment · design a model adapted to business needs (RAG).

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet11)**

---

### 🏅 Avantages Sportifs — P12
**Orchestrated data platform with a quality gate** · *"Manage a data-infrastructure project"*

`7-task Airflow DAG` · `26 Soda quality checks` · `Delta bronze/silver/gold` · `CDC → Slack`

**Context.** Sport Data Solution wanted to evaluate two employee benefits (a sport-commute bonus and wellness days), compute their cost, and publish activities to Slack — starting from sensitive HR data.

**Role.** Built the full pipeline and orchestrated it with a project-management approach: versioned business parameters, quality gate, runbook and phased delivery.

**Architecture.** PostgreSQL (source of truth) → **Debezium CDC** → **Redpanda** → { Slack notifier · **Spark** batch } → **Delta Lake** bronze/silver/gold → `gold` schema → Power BI. **Airflow** orchestration, **Soda Core** quality.

**Key results.**
- **Airflow DAG `sds_pipeline`**: 7 tasks, self-bootstrapping from an empty DB, ending in a **Soda quality barrier** (a failed check fails the run)
- **26 Soda checks** historized in `dq.controle_qualite` + monitoring views (`bi.qualite`, `bi.volumetrie`)
- **Spark → Delta lakehouse**: KPIs **identical to the SQL oracle**, Delta time-travel validated
- Real-time **Slack notifier** on new activities via CDC; business params versioned in-DB; sensitive HR files never committed

**Skills.** Strategic &amp; methodological support · plan and track a data project · manage priorities · communicate.

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet12)**

---

### 🏢 Building Energy — ML + serving — P6
**Energy-consumption prediction exposed as an API** · *"Anticipate building energy needs"*

`5 models compared` · `data leakage removed` · `BentoML service` · `AWS ECR`

**Context.** Predict the annual energy consumption of Seattle non-residential buildings to help target efficiency efforts, and expose the model as a usable API.

**Role.** Full ML pipeline solo: EDA, feature engineering, data-leakage identification, model comparison, and deployment as an inference service.

**Architecture.** scikit-learn `Pipeline` + `ColumnTransformer` → model comparison (Linear Regression, Random Forest, GBR, XGBoost, LightGBM) → **BentoML** service with typed inputs (property type, neighborhood…) → containerized and pushed to **AWS ECR** (eu-west-3).

**Key results.**
- **Identified and removed a data-leakage feature** (ENERGY STAR Score): a model using it scored R²≈0.91 but was **correctly discarded** — rigor over a flattering number
- Leakage-free models (Random Forest / Gradient Boosting, tuned) at **test R² ≈ 0.40** (log scale), **RMSE ≈ 49 kBtu**, **MAE ≈ 30 kBtu**; the **Gradient Boosting** model is the one served
- Typed **inference API served via BentoML**, containerized and deployed to AWS ECR

**Skills.** Prepare/transform data · train a model · evaluate a model · expose results via an API.

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet6)**

---

### 🎫 POC Tickets — real-time streaming — P9
**End-to-end streaming pipeline** · *"Model a cloud infrastructure"*

`Redpanda broker` · `PySpark Structured Streaming` · `5 s micro-batch` · `JSON + Parquet`

**Context.** InduTechData needed a proof of concept for processing customer-support tickets in real time.

**Role.** Built the full streaming pipeline, fully containerized.

**Architecture.** Python **producer** → **Redpanda** (Kafka API, topic `client_tickets`) → **PySpark Structured Streaming** (parse → enrich `assign_team()` → aggregate count) → JSON + Parquet with checkpointing. Docker Compose + Redpanda Console for supervision.

**Key results.**
- Continuous flow: ticket generation → Redpanda → Spark **micro-batches every 5 s**, stateful counting with **checkpoint recovery**
- Enrichment routing tickets to support teams by request type; live aggregation by type / team
- Outputs in **JSON** (readable) + **Parquet** (columnar); fully containerized (PySpark + Java 21), zero manual runtime install
- Documented with a **video demo** and a Mermaid architecture diagram

**Skills.** Represent an infrastructure · extract/transform/load from sources · assess IS compatibility.

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet9)** · **[video demo](https://youtu.be/dav-MnLch4M)**

---

### 🍃 MongoDB Cluster — distributed NoSQL — P7
**Sharded cluster with replica sets** · *"Design and analyze a NoSQL database"*

`2 shards` · `Primary–Secondary–Arbiter replica sets` · `config server + mongos` · `shard key: city`

**Context.** Design a NoSQL data store able to scale horizontally and stay available, then analyze real listings data (Paris / Lyon).

**Role.** Set up the full sharded-cluster topology and ran the analysis.

**Architecture.** **2 shards**, each a **replica set** in Primary–Secondary–Arbiter (PSA) configuration, plus a **config server** and a **mongos** query router — the standard production sharding topology. Collection sharded on the `city` key.

```
mongos (router)
  ├── config server
  ├── shard 1 : data1 (P) · data2 (S) · arbiter
  └── shard 2 : data_shard2 (P) · data_shard2_sec (S) · arbiter_shard2
```

**Key results.**
- **2-shard sharded cluster** (shard key `city`), each shard a 3-member replica set → **horizontal scalability + high availability** (automatic failover via arbiters)
- Dedicated **config server + mongos router** (production-grade topology, not a single-node setup)
- Listings data (Paris / Lyon) loaded and queried via **PyMongo / Polars**

**Skills.** Build &amp; manipulate databases · schematize a physical on-prem architecture · design a collection/storage solution.

🔗 **[Repository](https://github.com/PierreDff/OC_DE_projet7)**

---

## 🧰 Transversal skills

Beyond individual projects, these run across the whole portfolio:

- **Containerization** — Docker &amp; docker-compose (P5, P8, P9, P11, P12)
- **CI / CD** — GitHub Actions, tests + build (P11)
- **Data quality &amp; testing** — dbt tests (P8), Soda (P12), pytest (P11)
- **Cloud — AWS** — RDS, ECS/Fargate, ECR, S3, CloudWatch (P6, P8)
- **Security** — authentication &amp; secrets management on sensitive-data handling (P5, P12)

---

## 📚 Other projects

- **P2** — Exploratory analysis of international education data (Python, pandas)
- **P3** — Relational database design &amp; SQL (real-estate data)
- **P4** — Data environment audit &amp; recommendations (SQL, OLAP)
- **P5** — Secure NoSQL migration of medical data ([repo](https://github.com/PierreDff/OC_DE_projet5))
- **P10** — Flow orchestration with Kestra

---

## 📈 Methodology &amp; project management

The final project ("POC → MVP") is documented in a dedicated project-management report — *link to the PDF once published*.

---

## 📫 Contact

[LinkedIn](#) · [pierre.deffrennes@gmail.com](mailto:pierre.deffrennes@gmail.com) · [github.com/PierreDff](https://github.com/PierreDff)
