\# High Performers Performance Engine



\## Overview



The \*\*High Performers Performance Engine\*\* is a performance evaluation framework designed to identify top performers in commercial teams using a \*\*multi-model scoring system\*\*.



The engine processes operational sales data and calculates several \*\*performance rationales (R1–R5)\*\* using:



\- percentile ranking

\- statistical normalization

\- cluster benchmarking

\- weighted performance indicators



This approach ensures a \*\*fair, multi-dimensional evaluation\*\* of employees across different commercial roles.



Supported roles:



\- EV — Sales Executives  

\- EC — Channel Executives  

\- SDR — Sales Development Representatives



---



\# Architecture



The engine follows the workflow below:



```

Excel Operational Data

&nbsp;       ↓

Data Filtering

&nbsp;       ↓

Cluster Benchmark Calculation

&nbsp;       ↓

Performance Scoring (R1–R5)

&nbsp;       ↓

Unit Performance Validation

&nbsp;       ↓

Final Scoring Consolidation

&nbsp;       ↓

Excel Analytical Report

```



---



\# Data Sources



The system reads operational data from an Excel file containing multiple worksheets.



| Sheet | Description |

|-----|-----|

| EVs | Sales Executive performance metrics |

| ECs | Channel Executive metrics |

| SDRs | Sales Development metrics |

| %Ating\_Metas | Unit commercial targets |

| Página10 | Lead generation data |



---



\# Global Configuration



\## Analysis Period



```

DATA\_INICIO

DATA\_FIM

```



Defines the evaluation window for the performance analysis.



---



\## Standardized Column Names



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



\# Employee Eligibility Rules



Only employees with valid statuses are evaluated.



```

STATUS\_VALIDOS = \["ATIVO", "ATIVO DUPLICADO"]

```



This prevents inactive employees from affecting the scoring system.



---



\# Business Rules



\## Minimum Score Threshold



```

SCORE\_MINIMO = 70

```



Employees must achieve at least \*\*70 points\*\* to qualify for recognition.



---



\## Unit Performance Requirement



```

PCT\_META\_MINIMO = 0.6

```



A business unit must achieve its commercial target in \*\*at least 60% of the months\*\* during the evaluation period.



---



\## Minimum Contribution Share



To prevent employees with minimal participation from qualifying, a \*\*minimum contribution share\*\* is enforced.



Standard rule:



```

SHARE\_FIXO\_PADRAO = 0.25

```



Solo role rule:



```

TETO\_SOLO = 0.60

```



Employees who are the \*\*only professional in the role within a unit\*\* must represent at least \*\*60% of the unit’s results\*\*.



---



\# Performance Indicators



Each commercial role uses a \*\*different performance model\*\*.



---



\# EV (Sales Executive)



| Indicator | Weight |

|------|------|

| NMRR | 40% |

| Demos | 20% |

| Conversion | 20% |

| Early Churn | 20% |



---



\# EC (Channel Executive)



| Indicator | Weight |

|------|------|

| NMRR de Contador | 40% |

| Leads de Contador | 20% |

| Contadores indicando | 20% |

| Reuniões | 20% |



---



\# SDR (Sales Development)



| Indicator | Weight |

|------|------|

| Leads Agendados | 50% |

| Leads Trabalhados | 25% |

| Taxa de Agendamento | 25% |



---



\# Inverse Performance Indicators



Some metrics represent \*\*negative outcomes\*\*, meaning lower values represent better performance.



Example:



```

Early Churn

```



These indicators use \*\*inverse normalization logic\*\*.



---



\# Cluster Benchmark Calculation



The function `get\_medias\_cluster()` calculates the \*\*average performance per indicator within each cluster\*\*.



Clusters represent \*\*groups of comparable business units\*\*, ensuring fair benchmarking.



Example output:



| Cluster | NMRR | Conversion | Demos |

|------|------|------|------|

| Cluster A | 12000 | 18% | 30 |

| Cluster B | 9500 | 15% | 24 |



These averages are later used for \*\*R5 benchmarking\*\*.



---



\# Performance Scoring Models



The engine calculates \*\*five different performance rationales\*\*.



Each model evaluates performance from a different statistical perspective.



---



\# R1 — Weighted Percentile Ranking



```

score\_R1\_percentil\_ponderado

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



Measure \*\*relative performance within the peer group\*\*.



---



\# R2 — Simple Percentile Ranking



```

score\_R2\_percentil\_simples

```



Same logic as R1 but \*\*without indicator weights\*\*.



Purpose:



Provide a \*\*neutral ranking model\*\*.



---



\# R3 — Weighted Direct Normalization



```

score\_R3\_direto\_ponderado

```



Each metric is normalized against the \*\*best performer in the cluster\*\*.



Formula:



```

metric\_value / max\_cluster\_value \* 100

```



Purpose:



Measure \*\*distance from the best performer\*\*.



---



\# R4 — Simple Direct Normalization



```

score\_R4\_direto\_simples

```



Same as R3 but without weights.



Purpose:



Provide an \*\*equal-weight normalization model\*\*.



---



\# R5 — Performance vs Cluster Average



```

score\_R5\_vs\_media\_cluster

```



This model compares employee performance to the \*\*cluster average\*\*.



Formula:



```

employee\_metric / cluster\_average \* 100

```



Interpretation:



| Score | Meaning |

|------|------|

| 100 | Equal to cluster average |

| >100 | Above cluster average |

| <100 | Below cluster average |



This metric identifies employees who \*\*consistently outperform their market segment\*\*.



---



\# Data Filtering



Before scoring, the data is filtered based on:



1\. Analysis period

2\. Valid employee status



```

df\[COL\_PERIODO].between(DATA\_INICIO, DATA\_FIM)

df\[COL\_STATUS].isin(STATUS\_VALIDOS)

```



---



\# Unit Performance Evaluation



Unit performance is calculated using the ratio:



```

NMRR / Commercial Target

```



A unit is considered successful if:



```

monthly\_target\_hit >= 60%

```



This ensures employees are only evaluated positively when operating in \*\*performing business environments\*\*.



---



\# Final Score Calculation



The function `calcular\_todos\_racionais()` calculates all scoring models simultaneously.



Output includes:



| Score | Description |

|------|------|

| score\_R1\_percentil\_ponderado | Weighted percentile ranking |

| score\_R2\_percentil\_simples | Simple percentile ranking |

| score\_R3\_direto\_ponderado | Weighted normalization |

| score\_R4\_direto\_simples | Simple normalization |

| score\_R5\_vs\_media\_cluster\_ponderado | Weighted cluster comparison |

| score\_R5\_vs\_media\_cluster\_simples | Simple cluster comparison |



---



\# Benchmark Storage



Cluster benchmark data is stored in:



```

Historico\_Medias\_Cluster.csv

```



This allows:



\- longitudinal performance tracking

\- benchmark evolution analysis

\- reproducibility of calculations



---



\# Output



The system generates a final Excel report containing:



| Sheet | Description |

|------|------|

| Scores\_EV | EV performance results |

| Scores\_EC | EC performance results |

| Scores\_SDR | SDR performance results |

| Cluster\_Averages | Cluster benchmark metrics |



---



\# Final Report



Output file:



```

Relatorio\_Performance\_Multirracial\_Final\_Organizado2.xlsx

```



This report can be used for:



\- executive performance reviews

\- incentive programs

\- performance benchmarking

\- compensation models



---



\# Key Characteristics



✔ Multi-model scoring framework  

✔ Role-specific performance indicators  

✔ Cluster benchmarking  

✔ Weighted statistical scoring  

✔ Automated reporting  

✔ Scalable evaluation architecture



---



---



\# Mock Example



Below is a simplified example illustrating how the \*\*High Performers Performance Engine\*\* evaluates an employee.



\## Input Data Example



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



\## Scoring Output



After running the engine, the system generates a consolidated scoring table.



| Employee | Role | Unit | Score R1 | Score R2 | Score R3 | Score R4 | Score R5 | Final Status |

|------|------|------|------|------|------|------|------|------|

| João Silva | EV | Alpha Team | 92 | 88 | 95 | 90 | 104 | High Performer |



Interpretation:



\- \*\*R1 (92):\*\* João ranks in the top percentile of his cluster.

\- \*\*R3 (95):\*\* His performance is close to the top performer benchmark.

\- \*\*R5 (104):\*\* His results are \*\*4% above the cluster average\*\*.



Since the final score is above the minimum threshold:



```

SCORE\_MINIMO = 70

```



João Silva is classified as a \*\*High Performer\*\*.



---



This mock example demonstrates how the engine transforms \*\*raw operational sales data into structured performance intelligence\*\* and can optionally trigger \*\*automated communication workflows\*\*.

