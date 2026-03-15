# Day 15 · PM Session — Database Theory & Relational Algebra
## Artifact 7 · Take-Home Assignment
**PG Diploma · AI-ML & Agentic AI Engineering · IIT Gandhinagar**
**Week 03 · Day 15 of 120**

---

## 📋 Assignment Overview

| Field | Details |
|-------|---------|
| **Session** | Day 15 · PM (IIT-GN Faculty) |
| **Topics** | ER modelling, Relational Algebra (σ π ⋈ ∪ −), Normalisation (1NF→2NF→3NF→BCNF), ACID Properties, Pandas-to-Algebra mapping |
| **Estimated Time** | 60 – 75 min |
| **Due** | Day 17 · 09:15 AM |

---

## 📁 Repository Structure

```
W3_D15_Art7_PM_TakeHome/
├── README.md                          ← this file
├── W3_D15_Art7_PM_TakeHome.ipynb      ← Jupyter notebook (all code + SQL)
└── W3_D15_Art7_PM_TakeHome.docx       ← Full written submission
```

---

## 🗂️ Assignment Breakdown

### Part A — IIT-GN Template (40%)

**A1 · ER Diagram — Online Food Delivery App**

6 entities with full cardinality:

| Relationship | Cardinality |
|---|---|
| Customer → Order | 1 : N |
| Order ↔ MenuItem (via OrderItem) | M : N |
| Restaurant → MenuItem | 1 : N |
| Order → Restaurant | N : 1 |
| Order → DeliveryAgent | N : 0..1 |
| Order → Payment | 1 : 1 |

**A2 · Normalisation of OrderFacts**

Step-by-step decomposition of a flat 22-column table to 3NF:
- **1NF** → Set composite PK (order_id, item_id); ensure atomic values
- **2NF** → Remove partial dependencies (customer info, item info, agent info)
- **3NF** → Remove transitive dependencies (customer_id → city, restaurant_id → rating)

Final 3NF tables: `Customer`, `Restaurant`, `DeliveryAgent`, `MenuItem`, `Order`, `OrderItem`

**A3 · Relational Algebra Expressions**

| # | Query | Operators Used |
|---|-------|----------------|
| Q1 | Customers from Mumbai | σ, π |
| Q2 | Delivered order IDs | σ, π |
| Q3 | Customer names + order IDs | ⋈, π |
| Q4 | Items in order 1001 | σ, ⋈, π |
| Q5 | High-rated Bangalore restaurants | σ, ⋈, π |

**A4 · Pandas-to-Algebra Mapping**

| Pandas | Relational Algebra |
|--------|--------------------|
| `df[condition]` | σ Selection |
| `df[['col1','col2']]` | π Projection |
| `df.merge(df2, on=key)` | ⋈ Natural Join |
| `pd.concat([df1, df2])` | ∪ Union |
| `df.groupby().agg()` | γ Aggregation (extended RA) |
| `df1[~df1.id.isin(df2)]` | − Set Difference |

---

### Part B — Stretch Problem: BCNF (30%)

**Schema that is in 3NF but violates BCNF:**

```
AgentCoverage(agent_id, restaurant_id, zone)

FDs:
  (agent_id, restaurant_id) → zone      ← CK1
  (agent_id, zone) → restaurant_id      ← CK2
  restaurant_id → zone                   ← VIOLATES BCNF (not a superkey!)

Why 3NF passes: zone is a prime attribute (in CK2), so transitive dep is allowed.
Why BCNF fails: restaurant_id is not a superkey.
```

**BCNF Decomposition:**
```
RestaurantZone(restaurant_id PK, zone)
AgentAssignment(agent_id, restaurant_id PK)
```

**When to prefer 3NF over BCNF:**
- Dependency preservation required (FDs lost after BCNF decomposition)
- Read-heavy workloads where extra joins hurt performance
- Rarely-updated data where update anomaly risk is low
- Legacy schemas where migration cost exceeds benefit

---

### Part C — Interview Ready (20%)

**C1 · Update Anomalies — Concrete Example**

In a flat `OrderHistory` table, changing Wireless Mouse price from ₹799 → ₹849 requires updating 3 separate rows. If rows 5001 and 5003 are updated but 5004 is missed, the database contains two prices for the same product — a direct data inconsistency caused by a single column update across multiple rows.

**C2 · Price History Schema (3NF)**

```sql
Product(product_id PK, product_name, category, current_price)
PriceHistory(price_history_id PK, product_id FK, old_price, new_price, changed_at, changed_by)
```

Price update = one INSERT + one UPDATE inside a single transaction (ACID atomicity).

**C3 · ACID & Double-Booking**

Primary property at risk: **ISOLATION**

Prevention options:
1. `SELECT ... FOR UPDATE` (pessimistic locking)
2. Optimistic locking with version column
3. `UNIQUE(room_id, check_in_date)` constraint

---

### Part D — AI-Augmented Task (10%)

**Prompt used:**
> Design the database schema for a ride-sharing app (like Ola/Uber). Include ER diagram description, normalised tables in 3NF, and 5 SQL queries with window functions.

**AI Output — Ride-sharing schema:**
```
Driver · Rider · Vehicle · Trip · Payment · Rating
```

**Window Function Queries (all verified in notebook):**

| # | Function | Purpose |
|---|----------|---------|
| Q1 | `RANK()` | Rank drivers by total earnings |
| Q2 | `ROW_NUMBER()` | Trip sequence per rider |
| Q3 | `LAG()` | Fare change vs previous trip |
| Q4 | `SUM() OVER` | Running total fare per driver |
| Q5 | `NTILE(3)` | Segment trips by fare tier |

**Evaluation of AI output:**
- ✅ Schema passes 3NF check (no partial or transitive dependencies)
- ⚠️ Surge pricing not modelled — `fare` stored flat in `Trip` (gap identified by human review)
- ✅ All 5 SQL queries run with verified output in SQLite

---

## 🚀 Running the Notebook

```bash
# Install dependencies
pip install pandas numpy jupyter

# Launch notebook
jupyter notebook W3_D15_Art7_PM_TakeHome.ipynb
```

> All SQL queries use **SQLite in-memory** (via Python's `sqlite3` module) — no external database setup required.

---

## 📊 Rubric Summary

| Criteria | Weight | Coverage |
|----------|--------|----------|
| Correctness | 40% | All 5 RA expressions with correct operators; full 3-step normalisation with FD analysis; ER cardinalities for all 6 relationships |
| Code Quality | 25% | 3 Pandas-to-algebra mappings with live output; 5 window function SQL queries verified with printed results |
| Understanding | 20% | Concrete row-level update anomaly with specific values; BCNF violation proven with specific FDs; ACID isolation explanation with timeline |
| AI Usage | 15% | Schema evaluated for 3NF; missing surge pricing gap identified; all 5 SQL queries verified |

---

*IIT Gandhinagar · PG Diploma in AI-ML & Agentic AI Engineering · Day 15 of 120*
