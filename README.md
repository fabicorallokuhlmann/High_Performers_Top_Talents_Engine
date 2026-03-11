# High Performers Performance Engine

## Overview

The **High Performers Performance Engine** is a performance evaluation framework designed to identify top performers in commercial teams using a **multi-model scoring system**.

The engine processes operational sales data and calculates several **performance rationales (R1–R5)** using:

- percentile ranking
- statistical normalization
- cluster benchmarking
- weighted performance indicators

This approach ensures a **fair, multi-dimensional evaluation** of employees across different commercial roles.

Supported roles:

- EV — Sales Executives  
- EC — Channel Executives  
- SDR — Sales Development Representatives

---

# Architecture

The engine follows the workflow below:

```
Excel Operational Data
        ↓
Data Filtering
        ↓
Cluster Benchmark Calculation
        ↓
Performance Scoring (R1–R5)
        ↓
Unit Performance Validation
        ↓
Final Scoring Consolidation
        ↓
Excel Analytical Report
```

---

# Data Sources

The system reads operational data from an Excel file containing multiple worksheets.

| Sheet | Description |
|-----|-----|
| EVs | Sales Executive performance metrics |
| ECs | Channel Executive metrics |
| SDRs | Sales Development metrics |
| %Ating_Metas | Unit commercial targets |
| Página10 | Lead generation data |

---

# Global Configuration

## Analysis Period

```
DATA_INICIO
DATA_FIM
```

Defines the evaluation window for the performance analysis.

---

## Standardized Column Names

To maintain consistency across datasets, the script standardizes column references.

| Column | Description |
|------|------|
| Período (ajustado) | Monthly reference |
| Franquia (ajustado) | Business unit |
| Cluster (ajustado) | Market segmentation |
| Indicador (Ajustado) | Performance metric |
| Resultado | Metric value |
| Nome | Employee name |
| STATUS ATUAL | Employment status |

---

# Employee Eligibility Rules

Only employees with valid statuses are evaluated.

```
STATUS_VALIDOS = ["ATIVO", "ATIVO DUPLICADO"]
```

This prevents inactive employees from affecting the scoring system.

---

# Business Rules

## Minimum Score Threshold

```
SCORE_MINIMO = 70
```

Employees must achieve at least **70 points** to qualify for recognition.

---

## Unit Performance Requirement

```
PCT_META_MINIMO = 0.6
```

A business unit must achieve its commercial target in **at least 60% of the months** during the evaluation period.

---

## Minimum Contribution Share

To prevent employees with minimal participation from qualifying, a **minimum contribution share** is enforced.

Standard rule:

```
SHARE_FIXO_PADRAO = 0.25
```

Solo role rule:

```
TETO_SOLO = 0.60
```

Employees who are the **only professional in the role within a unit** must represent at least **60% of the unit’s results**.

---

# Performance Indicators

Each commercial role uses a **different performance model**.

---

# EV (Sales Executive)

| Indicator | Weight |
|------|------|
| NMRR | 40% |
| Demos | 20% |
| Conversion | 20% |
| Early Churn | 20% |

---

# EC (Channel Executive)

| Indicator | Weight |
|------|------|
| NMRR de Contador | 40% |
| Leads de Contador | 20% |
| Contadores indicando | 20% |
| Reuniões | 20% |

---

# SDR (Sales Development)

| Indicator | Weight |
|------|------|
| Leads Agendados | 50% |
| Leads Trabalhados | 25% |
| Taxa de Agendamento | 25% |

---

# Inverse Performance Indicators

Some metrics represent **negative outcomes**, meaning lower values represent better performance.

Example:

```
Early Churn
```

These indicators use **inverse normalization logic**.

---

# Cluster Benchmark Calculation

The function `get_medias_cluster()` calculates the **average performance per indicator within each cluster**.

Clusters represent **groups of comparable business units**, ensuring fair benchmarking.

Example output:

| Cluster | NMRR | Conversion | Demos |
|------|------|------|------|
| Cluster A | 12000 | 18% | 30 |
| Cluster B | 9500 | 15% | 24 |

These averages are later used for **R5 benchmarking**.

---

# Performance Scoring Models

The engine calculates **five different performance rationales**.

Each model evaluates performance from a different statistical perspective.

---

# R1 — Weighted Percentile Ranking

```
score_R1_percentil_ponderado
```

Employees are ranked against peers within the same:

```
Cluster + Month
```

Percentiles range from:

```
0 → lowest performer
100 → top performer
```

Each indicator is multiplied by its weight.

Purpose:

Measure **relative performance within the peer group**.

---

# R2 — Simple Percentile Ranking

```
score_R2_percentil_simples
```

Same logic as R1 but **without indicator weights**.

Purpose:

Provide a **neutral ranking model**.

---

# R3 — Weighted Direct Normalization

```
score_R3_direto_ponderado
```

Each metric is normalized against the **best performer in the cluster**.

Formula:

```
metric_value / max_cluster_value * 100
```

Purpose:

Measure **distance from the best performer**.

---

# R4 — Simple Direct Normalization

```
score_R4_direto_simples
```

Same as R3 but without weights.

Purpose:

Provide an **equal-weight normalization model**.

---

# R5 — Performance vs Cluster Average

```
score_R5_vs_media_cluster
```

This model compares employee performance to the **cluster average**.

Formula:

```
employee_metric / cluster_average * 100
```

Interpretation:

| Score | Meaning |
|------|------|
| 100 | Equal to cluster average |
| >100 | Above cluster average |
| <100 | Below cluster average |

This metric identifies employees who **consistently outperform their market segment**.

---

# Data Filtering

Before scoring, the data is filtered based on:

1. Analysis period
2. Valid employee status

```
df[COL_PERIODO].between(DATA_INICIO, DATA_FIM)
df[COL_STATUS].isin(STATUS_VALIDOS)
```

---

# Unit Performance Evaluation

Unit performance is calculated using the ratio:

```
NMRR / Commercial Target
```

A unit is considered successful if:

```
monthly_target_hit >= 60%
```

This ensures employees are only evaluated positively when operating in **performing business environments**.

---

# Final Score Calculation

The function `calcular_todos_racionais()` calculates all scoring models simultaneously.

Output includes:

| Score | Description |
|------|------|
| score_R1_percentil_ponderado | Weighted percentile ranking |
| score_R2_percentil_simples | Simple percentile ranking |
| score_R3_direto_ponderado | Weighted normalization |
| score_R4_direto_simples | Simple normalization |
| score_R5_vs_media_cluster_ponderado | Weighted cluster comparison |
| score_R5_vs_media_cluster_simples | Simple cluster comparison |

---

# Benchmark Storage

Cluster benchmark data is stored in:

```
Historico_Medias_Cluster.csv
```

This allows:

- longitudinal performance tracking
- benchmark evolution analysis
- reproducibility of calculations

---

# Output

The system generates a final Excel report containing:

| Sheet | Description |
|------|------|
| Scores_EV | EV performance results |
| Scores_EC | EC performance results |
| Scores_SDR | SDR performance results |
| Cluster_Averages | Cluster benchmark metrics |

---

# Final Report

Output file:

```
Relatorio_Performance_Multirracial_Final_Organizado2.xlsx
```

This report can be used for:

- executive performance reviews
- incentive programs
- performance benchmarking
- compensation models

---

# Key Characteristics

✔ Multi-model scoring framework  
✔ Role-specific performance indicators  
✔ Cluster benchmarking  
✔ Weighted statistical scoring  
✔ Automated reporting  
✔ Scalable evaluation architecture

---

---

# Mock Example

Below is a simplified example illustrating how the **High Performers Performance Engine** evaluates an employee.

## Input Data Example

| Período | Franquia | Cluster | Nome | Indicador | Resultado |
|------|------|------|------|------|------|
| 2024-01 | Alpha Team | Cluster A | João Silva | NMRR | 15000 |
| 2024-01 | Alpha Team | Cluster A | João Silva | Demos | 32 |
| 2024-01 | Alpha Team | Cluster A | João Silva | Conversion | 21% |
| 2024-01 | Alpha Team | Cluster A | João Silva | Early Churn | 3% |

Cluster benchmark:

| Cluster | NMRR Avg | Conversion Avg | Demos Avg |
|------|------|------|------|
| Cluster A | 12000 | 18% | 30 |

---

## Scoring Output

After running the engine, the system generates a consolidated scoring table.

| Employee | Role | Unit | Score R1 | Score R2 | Score R3 | Score R4 | Score R5 | Final Status |
|------|------|------|------|------|------|------|------|------|
| João Silva | EV | Alpha Team | 92 | 88 | 95 | 90 | 104 | High Performer |

Interpretation:

- **R1 (92):** João ranks in the top percentile of his cluster.
- **R3 (95):** His performance is close to the top performer benchmark.
- **R5 (104):** His results are **4% above the cluster average**.

Since the final score is above the minimum threshold:

```
SCORE_MINIMO = 70
```

João Silva is classified as a **High Performer**.

---

This mock example demonstrates how the engine transforms **raw operational sales data into structured performance intelligence** and can optionally trigger **automated communication workflows**.
