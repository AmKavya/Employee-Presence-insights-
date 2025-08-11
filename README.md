# Presence Insights – HR Analytics Dashboard

## 📌 Project Overview
**Presence Insights** is an end-to-end Power BI solution analyzing employee attendance patterns across multiple months, including Presence %, Work-From-Home (WFH) %, and Sick Leave % (SL %).  
The dashboard automates the process of combining multiple Excel sheets/files, transforming raw data into actionable insights for HR teams, and presenting them through a clean, interactive interface.

---

## 📂 Dataset & Structure
- **Source**: Monthly Excel files (April, May, June 2022) containing multiple attendance sheets.
- **Key Fields**:  
  - `Employee Name`  
  - `Date`  
  - `Status` (e.g., P = Present, WFH = Work From Home, SL = Sick Leave, WO = Weekly Off, HO = Holiday)  

---

## 🔄 Data Transformation (Power Query)
**How it’s done**:
- Used **Folder Connector** to load all monthly Excel files dynamically.
- Within each file, extracted **all sheets dynamically** and applied a **common transformation function** to:
  - Promote headers
  - Standardize column names
  - Convert to correct data types
- Appended all sheets into one **combined dataset** (`combined data` table).
- Created calculated flags for P, WFH, SL, WO, HO for later aggregation.

**Why it’s done**:
- Ensures **automation** — new files/sheets are loaded without rewriting queries.
- Maintains **data consistency** and removes manual effort.
- Supports **scalable, refreshable reporting** for future months


---

## 📊 DAX Measures & Calculated Columns

### **Measures**
| Measure | Purpose |
|---------|---------|
| **`presentdays`**<br>`VAR presentdays = CALCULATE(COUNT('combined data'[Value]), 'combined data'[Value] = "P") RETURN presentdays + [WFH count]` | Counts all present days plus WFH days (as they count towards presence). |
| **`totaworkingdays`**<br>`VAR totaldays = COUNT('combined data'[Value]) VAR nonworkdays = CALCULATE(COUNT('combined data'[Value]), 'combined data'[Value] IN {"WO","HO"}) RETURN totaldays - nonworkdays` | Calculates total working days by excluding weekly offs (WO) and holidays (HO). |
| **`Presence %`**<br>`DIVIDE([presentdays], 'measure table'[totaworkingdays], 0)` | Percentage of working days where the employee was present (including WFH). |
| **`WFH count`**<br>`SUM('combined data'[WFHcount])` | Total number of WFH days (including half days). |
| **`WFH %`**<br>`DIVIDE([WFH count], [presentdays], 0)` | Proportion of WFH days relative to all present days. |
| **`SL count`**<br>`SUM('combined data'[SLcount])` | Total number of sick leave days (including half days). |
| **`SL %`**<br>`DIVIDE([SL count], [totaworkingdays], 0)` | Proportion of sick leave days out of total working days. |

---

### **Calculated Columns**
| Column | Purpose |
|--------|---------|
| **`dow`**<br>`FORMAT('combined data'[date], "ddd")` | Extracts day of week abbreviation (e.g., Mon, Tue) for weekday pattern analysis. |
| **`month`**<br>`STARTOFMONTH('combined data'[date])` | Groups data by the start date of each month for monthly analysis. |
| **`SLcount`**<br>`SWITCH(TRUE(), 'combined data'[Value] = "SL", 1, 'combined data'[Value] = "HSL", 0.5, 0)` | Assigns numeric value to sick leave days (full = 1, half = 0.5). |
| **`WFHcount`**<br>`SWITCH(TRUE(), 'combined data'[Value] = "WFH", 1, 'combined data'[Value] = "HWFH", 0.5, 0)` | Assigns numeric value to WFH days (full = 1, half = 0.5). |

---

## 🎨 Dashboard Visuals
- **KPI Cards** – Overall Presence %, WFH %, and SL %.  
- **Employee Table** – Attendance stats by employee, including all calculated measures.  
- **Trend Charts** – Presence %, WFH %, SL % over time.  
- **Weekday Bar Charts** – Comparison of attendance metrics by weekday.  
- **Monthly Slicer** – Quick switch between months.

---

## 📈 Insights & Impact
- ~92% average presence rate across all employees.  
- Fridays had the lowest presence (~90%) and highest WFH (~12.7%).  
- Sick leave spikes in early June suggest a possible seasonal illness trend.  

---




