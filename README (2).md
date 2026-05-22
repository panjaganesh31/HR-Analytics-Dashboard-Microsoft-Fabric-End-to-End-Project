# 📊 HR Analytics Dashboard — Microsoft Fabric End-to-End Project

> 🌐 **Live Portfolio:** [ganeshpanja18.github.io](https://ganeshpanja18.github.io)

> A production-grade HR Analytics solution built entirely on **Microsoft Fabric** — covering data ingestion, transformation, lakehouse storage, semantic modeling, and interactive Power BI reporting. Designed to surface actionable workforce insights for HR teams and business leaders.

<img width="959" height="476" alt="Dashboard Image" src="https://github.com/user-attachments/assets/33b72829-23e9-49fe-8ee3-f49e451ad71d" />

---

## 🚀 Project Highlights

- **End-to-end pipeline** on a single unified platform (Microsoft Fabric) — no external tools required
- **Automated refresh orchestration** using a Data Pipeline that chains Dataflow → Semantic Model in sequence
- **Delta Lake storage** via Fabric Lakehouse, enabling scalable, versioned, ACID-compliant data
- **Business-ready KPIs** with DAX measures for HeadCount, Attrition %, Average Salary, and Average Age
- **Interactive filtering** across Department, Position, Gender, Employment Status, and State

---

## 🏗️ Architecture Overview

```
Excel / CSV Source
        │
        ▼
┌─────────────────────┐
│  HR_Dashboard_      │  ← Dataflow Gen2 (Power Query / M Language)
│  Dataflow           │     • Data cleaning & transformation
│                     │     • Age Bracket custom column
│                     │     • 39 columns, 311 rows
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  HR_Dashboard_      │  ← Microsoft Fabric Lakehouse (Delta Tables)
│  Lakehouse          │     • HRDataset table (311 rows × 39 columns)
│                     │     • SQL Analytics Endpoint enabled
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  HR_Dashboard_      │  ← Semantic Model (DAX Measures & Relationships)
│  Semantic_Model     │     • KPIs: HeadCount, Attrition, Avg Salary
│                     │     • Upstream: Lakehouse + Dataflow
└────────┬────────────┘
         │
         ▼
┌─────────────────────┐
│  HR Dashboard       │  ← Power BI Report (Interactive Visuals)
│  Report             │     • Filters: Dept, Position, Gender, State
│                     │     • 6 chart types + 5 KPI cards
└─────────────────────┘
         ▲
         │
┌─────────────────────┐
│  HR_Dashboard_      │  ← Data Pipeline (Orchestration)
│  Refresh_Pipeline   │     • Hr_Dataflow → HR_Semantic_Model_Refresh
└─────────────────────┘
```

**Design rationale:** The layered architecture separates concerns cleanly — raw ingestion, transformation, storage, and presentation each live in dedicated Fabric components. This mirrors real-world Medallion architecture patterns and makes the solution easy to maintain and extend.

---

## 📁 Project Components

| # | Component | Fabric Type | Purpose |
|---|-----------|-------------|---------|
| 1 | **HR Dashboard** | Power BI Report | Interactive analytics — 6 visuals, 5 KPI cards, multi-field slicers |
| 2 | **HR_Dashboard_Dataflow** | Dataflow Gen2 | ETL layer — loads, cleans, and transforms raw HR data using Power Query / M |
| 3 | **HR_Dashboard_Lakehouse** | Lakehouse (Delta Tables) | Central data store — persists `HRDataset` as a versioned Delta table |
| 4 | **HR_Dashboard_Lakehouse** | SQL Analytics Endpoint | Enables direct T-SQL queries on Lakehouse tables without data movement |
| 5 | **HR_Dashboard_Refresh_Pipeline** | Data Pipeline | Orchestrates sequential refresh: Dataflow runs first, then Semantic Model |
| 6 | **HR_Dashboard_Semantic_Model** | Semantic Model | Business logic layer — DAX measures, KPI definitions, relationships |

---

## 📊 Dashboard KPIs & Visuals

### 🔢 KPI Cards

| Metric | Value | Description |
|--------|-------|-------------|
| **HeadCount** | 311 | Total active employee records |
| **Attrition** | 104 | Employees who have left |
| **Attrition %** | 33% | Proportion of workforce lost |
| **Average Salary** | $69.02K | Mean annual salary across all employees |
| **Average Age** | 47.41 | Mean employee age |

### 📈 Charts & Visuals

| Visual | Type | Key Insight |
|--------|------|-------------|
| **HeadCount by Department** | Bar chart | Production is the largest dept at 209 employees |
| **HeadCount by Age Bracket** | Donut chart | The 36–45 bracket is the largest segment (47.91%) |
| **HeadCount by Marital Status & Gender** | Grouped bar | Side-by-side Male vs Female breakdown by status |
| **HeadCount by Recruitment Source** | Bar chart | Indeed (100 hires) is the #1 sourcing channel |
| **Attrition by Year** | Line chart | Historical attrition trend from 2008–2014 |
| **Cumulative HeadCount by Year** | Dual-line chart | Workforce growth trajectory to 310 employees |

### 🎛️ Filters / Slicers

`Department` · `Position` · `Employment Status` · `Gender` · `State`

---

## 🔄 Pipeline & Refresh Logic

```
HR_Dashboard_Refresh_Pipeline
        │
        ├──► [Step 1] Hr_Dataflow              (Dataflow Gen2 refresh)
        │
        └──► [Step 2] HR_Semantic_Model_Refresh (triggered only after Step 1 succeeds ✅)
```

The pipeline uses **sequential dependency** — the Semantic Model refresh only triggers after the Dataflow completes successfully. This prevents stale data from reaching the report layer and ensures the entire stack stays in sync with a single pipeline run.

---

## 🗄️ Dataset Details

**Table:** `HRDataset` · Stored in: `HR_Dashboard_Lakehouse`

| Property | Value |
|----------|-------|
| Rows | 311 |
| Columns | 39 |
| Storage format | Delta (Parquet + transaction log) |
| Key columns | `Employee_Name`, `EmpID`, `GenderID`, `DeptID`, `MaritalStatusID`, `EmpStatusID`, `PerfScoreID` |
| Custom columns added | `Age Bracket` (Power Query M, Dataflow layer) |
| SQL access | Available via SQL Analytics Endpoint |

---

## 🔗 Semantic Model Lineage

| Item | Type | Direction |
|------|------|-----------|
| HR Dashboard (Report) | Power BI Report | ⬇ Downstream consumer |
| HR_Dashboard_Dataflow | Dataflow Gen2 | ⬆ Upstream source |
| HR_Dashboard_Lakehouse | Lakehouse | ⬆ Upstream source |

The Semantic Model sits at the center of the lineage graph, decoupling the storage layer from the presentation layer. Any upstream change (data or schema) flows through without requiring report-level edits.

---

## 🛠️ Tech Stack

| Technology | Role in Project |
|------------|-----------------|
| **Microsoft Fabric** | Unified platform — Workspace, Lakehouse, Dataflow Gen2, Data Pipeline |
| **Power BI** | Report creation and Semantic Model hosting |
| **Delta Lake** | Open-format columnar storage with ACID transactions in Lakehouse |
| **DAX** | Calculated measures — HeadCount, Attrition %, Avg Salary, Avg Age |
| **Power Query / M** | Dataflow transformations — null handling, type casting, Age Bracket column |
| **SQL Analytics Endpoint** | T-SQL interface directly on Lakehouse Delta tables (no copy needed) |

---

## 📸 Screenshots

### HR Dashboard Report
<img width="959" height="476" alt="HR Dashboard Report" src="https://github.com/user-attachments/assets/3fb774df-ca50-45fc-83e4-0f187fbd0141" />

### Lakehouse — HRDataset Table
<img width="959" height="472" alt="Lakehouse HRDataset Table" src="https://github.com/user-attachments/assets/5fb4714a-a338-4f62-a754-57291e454d0d" />

### Dataflow — Power Query Transformations
<img width="959" height="472" alt="Dataflow Power Query" src="https://github.com/user-attachments/assets/d10e1f71-a6f9-4d4b-979d-5acd0d8168ce" />

### Refresh Pipeline
<img width="959" height="476" alt="HR Dashboard Pipeline" src="https://github.com/user-attachments/assets/fbbc8f01-956a-4fdb-b658-c9206da1cc77" />

### Semantic Model — Lineage View
<img width="953" height="473" alt="Semantic Model Lineage" src="https://github.com/user-attachments/assets/5821035a-4a53-4533-9037-e6c6d5915b7b" />

---

## 💡 What I Learned / Skills Demonstrated

- Designing a **multi-layer Fabric architecture** (Dataflow → Lakehouse → Semantic Model → Report)
- Writing **Power Query / M** transformations for data cleansing and custom column creation
- Building a **Semantic Model** with DAX measures for business KPIs
- Configuring **Dataflow Gen2** as a scalable ETL layer within Fabric
- Using **Delta Lake** as a governed, queryable data store with SQL access
- Orchestrating end-to-end refresh with a **Data Pipeline** and sequential activity dependencies
- Designing a **Power BI report** with multiple chart types and cross-filtering slicers

---

## 🙏 Acknowledgements

- Built on **Microsoft Fabric** (Preview / Trial workspace)
- HR Dataset — sample employee data used for analytics demonstration purposes
- Inspired by real-world HR reporting requirements: workforce planning, attrition analysis, and recruitment effectiveness
