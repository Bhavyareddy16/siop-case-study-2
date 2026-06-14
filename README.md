# SIOP Case Study 2: Forecast Accuracy & Bias Reporting

This repository contains the end-to-end data transformation pipeline, analytical reports, and Power BI modeling guidelines for **SIOP Case Study 2**. 

🖥️ **[Live Interactive Dashboard Link](https://bhavyareddy16.github.io/siop-case-study-2/)**

The goal of this project is to aggregate monthly SIOP forecast snapshots and compare them side-by-side with actual sales figures by `Item`, `Country`, and `Period` to report on forecast accuracy and bias.

---

## 📂 Project Structure

* **`build_siop_assignment.py`**: Python ETL script that parses the raw forecast/actual sheets from an `.xlsb` file, cleans keys, aggregates values, maps lag labels, and outputs the deliverables.
* **`outputs/siop_case_study_2/`**:
  * 🖥️ **`siop_dashboard.html` [NEW]**: Interactive, premium HTML dashboard containing responsive KPIs, country filters, lag toggles, and charts. Open directly on your Mac.
  * 📊 **`SIOP_Case_Study_2_Assignment_Pack.xlsx`**: Excel pack with automated summary dashboards, country-period breakdowns, charts, conditional formatting, and a data dictionary.
  * 🗄️ **`SIOP_Case_Study_2_Transformed_Data.csv`**: Transformed and aggregated fact table (112,282 rows) ready for BI modeling.
  * 📄 **`SIOP_Case_Study_2_Validation_Report.pdf`**: Premium, publication-ready PDF report presenting validation statistics, country KPIs, and a comparison visualization.
  * 📝 **`SIOP_Case_Study_2_Validation_Report.Rmd`**: R Markdown analytical code for reproducing the summary statistics and ggplot visualizations.
  * 📄 **`SIOP_Case_Study_2_Report.md`**: Executive markdown summary of the study.

---

## 📈 Metric & KPI Definitions

* **Resultant Snapshot**: Latest forecast snapshot (`2023 P12`)
* **Lag 0 Snapshot**: Previous forecast snapshot (`2023 P11`)
* **Lag 1 Snapshot**: Two-month prior forecast snapshot (`2023 P10`)
* **Forecast Accuracy %**: $1 - \frac{|\text{Forecast} - \text{Actual}|}{\text{Actual}}$ *(uses `DIVIDE` to prevent division-by-zero errors)*
* **Forecast Bias %**: $\frac{\text{Forecast} - \text{Actual}}{\text{Actual}}$

---

## 🖥️ Power BI Implementation

To set up the dashboard in Power BI:
1. **Load Fact Table**: Import `SIOP_Case_Study_2_Transformed_Data.csv`.
2. **Lag Selection**: Create a disconnected table named `Lag Selection` containing a single column `Lag` with three rows: `"Resultant"`, `"Lag 0"`, and `"Lag 1"`.
3. **Add DAX Measures**:

### Dynamic Lag Revenue Forecast
```dax
Lag Revenue Forecast = 
SWITCH(
    SELECTEDVALUE('Lag Selection'[Lag]), 
    "Resultant", SUM(SIOP[Resultant_Revenue_Forecast]), 
    "Lag 0", SUM(SIOP[Lag0_Revenue_Forecast]), 
    "Lag 1", SUM(SIOP[Lag1_Revenue_Forecast])
)
```

### Revenue Forecast Accuracy %
```dax
Revenue Forecast Accuracy % = 
1 - DIVIDE(
    ABS([Lag Revenue Forecast] - SUM(SIOP[Actual_Revenue])), 
    SUM(SIOP[Actual_Revenue])
)
```

### Revenue Forecast Bias %
```dax
Revenue Forecast Bias % = 
DIVIDE(
    [Lag Revenue Forecast] - SUM(SIOP[Actual_Revenue]), 
    SUM(SIOP[Actual_Revenue])
)
```

---

## 📊 Summary Performance (Overall Country Revenue)

| Country | Actual Revenue | Resultant Accuracy | Resultant Bias | Lag 0 Accuracy | Lag 0 Bias | Lag 1 Accuracy | Lag 1 Bias |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **A** | $38,088,468 | 5.09% | -94.91% | 6.10% | -93.90% | 5.31% | -94.69% |
| **B** | $4,690,326 | 14.24% | -85.76% | 19.73% | -80.27% | 14.88% | -85.12% |
| **C** | $2,851,024 | 10.30% | -89.70% | 27.10% | -72.90% | 14.15% | -85.85% |
| **D** | $409,216 | -170.44% | 270.44% | -253.10% | 353.10% | -221.92% | 321.92% |

---

## 🚀 Getting Started

### Python ETL
To run the preprocessing script, install the dependencies and execute the script:
```bash
pip install pandas openpyxl pyxlsb
python build_siop_assignment.py
```

### R Markdown Report
Open `outputs/siop_case_study_2/SIOP_Case_Study_2_Validation_Report.Rmd` in RStudio or run the following R script to compile an HTML document:
```R
rmarkdown::render("outputs/siop_case_study_2/SIOP_Case_Study_2_Validation_Report.Rmd")
```
