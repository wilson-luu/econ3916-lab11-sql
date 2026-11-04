# SQL for Economists — Joins, Windows and the Cardinality Trap

**Objective:** This project demonstrates the structural translation from procedural pandas to declarative SQL, highlighting how execution order, three-valued logic, and cardinality traps can silently corrupt analytical queries.

**Methodology:**
* Translated pandas operations (select, filter, sort, groupby, merge) into SQL with DuckDB, and checked that both give the same rows
* Showed the cardinality trap: a one-to-many LEFT JOIN turned 600 customer rows into 3,612 rows. The same Welch t-test on the same revenue figures went from t = 0.07 (p = 0.9413) to t = 0.71 (p = 0.4749), because the repeated rows shrank the standard error and moved the difference
* Used window functions (AVG OVER PARTITION BY, RANK) to compute group averages and ranks without collapsing rows
* Showed SQL's three-valued logic: WHERE income != 50000 kept 3 of 5 survey rows, because NULL evaluations return 'unknown' which WHERE drops, while the same filter in pandas kept 5
* Queried a public Parquet file of about 3 million NYC taxi trips over HTTP without downloading it, reading only the columns the query needed
* Asked an AI to translate three of my pandas steps into SQL, revised my prompt once, and compared the row counts of its queries with my pandas results

**Key Findings:**
Translating between pandas and SQL requires handling structural differences in NULL logic and execution order. Un-aggregated joins silently multiply data, falsely inflating statistical significance (p-value shrank from 0.94 to 0.47) by artificially shrinking the standard error.
