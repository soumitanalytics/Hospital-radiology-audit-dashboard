# 🏥 Hospital Radiology Equipment Audit & Financial Impact Analysis

An end-to-end healthcare data analytics project analyzing **operational downtime**, **Service Level Agreement (SLA) breaches**, and **financial loss** across hospital radiology equipment (MRI, CT, Ultrasound, X-Ray).

---

## 📊 Executive Summary

Unplanned downtime in radiology departments leads to delayed patient diagnoses, operational bottlenecks, and substantial lost revenue. This project transforms raw, messy equipment maintenance logs into an interactive operational dashboard. 

By cleaning time metrics and modeling financial opportunity losses alongside direct repair bills, this analysis highlights critical SLA breach rates and categorizes equipment failure risks into actionable tiers.

---

## 📂 Project Architecture & Files

| File Name | Description |
| :--- | :--- |
| **`dashboard.html`** | Interactive dark-themed web dashboard built with HTML, Tailwind CSS, and Chart.js. |
| **`Radiology_Downtime_Clean.xlsx`** | Full Excel workbook containing the ETL pipeline built in Power Query. |
| **`Radiology_Downtime_Clean.csv`** | Exported clean dataset formatted with engineered features. |
| **`README.md`** | Project documentation, ETL breakdown, and business insight analysis. |

---

## 🛠️ Data Engineering & Business Feature Engineering

Using **Power Query (M Code)**, raw equipment logs were transformed through a structured ETL pipeline to generate key business insights:

### 1. Downtime Hours Standardization (`Downtime_Hours`)
* **Problem:** Raw duration subtractions were stored as day fractions (`0.2159` days), creating type mismatch errors and skewed hour comparisons.
* **Solution:** Standardized duration outputs into decimal hours by multiplying day fractions by 24 ($0.2159 \times 24 = 5.18\text{ hours}$).

### 2. Operational SLA Breach Detection (`Is_SLA_Breach`)
Custom conditional logic was established based on modality target recovery limits:
* **X-Ray:** Target SLA = **30 mins** ($0.5\text{ hrs}$)
* **Ultrasound:** Target SLA = **45 mins** ($0.75\text{ hrs}$)
* **CT Scanner:** Target SLA = **60 mins** ($1.0\text{ hr}$)
* **MRI Scanner:** Target SLA = **120 mins** ($2.0\text{ hrs}$)

```powerquery
if [Modality] = "X-Ray" and [Downtime_Hours] > 0.5 then "Breached"
else if [Modality] = "Ultrasound" and [Downtime_Hours] > 0.75 then "Breached"
else if [Modality] = "CT" and [Downtime_Hours] > 1.0 then "Breached"
else if [Modality] = "MRI" and [Downtime_Hours] > 2.0 then "Breached"
else "Within SLA"
```

### 3. Financial Loss Modeling (`Estimated_Revenue_Loss`)
Downtime causes revenue loss from missed diagnostic scans. Hourly billing rates were assigned per modality and multiplied by total downtime hours:
* **MRI Scanner:** $\$600/\text{hr}$
* **CT Scanner:** $\$500/\text{hr}$
* **Ultrasound:** $\$200/\text{hr}$
* **X-Ray:** $\$150/\text{hr}$

```powerquery
if [Modality] = "MRI" then [Downtime_Hours] * 600
else if [Modality] = "CT" then [Downtime_Hours] * 500
else if [Modality] = "Ultrasound" then [Downtime_Hours] * 200
else if [Modality] = "X-Ray" then [Downtime_Hours] * 150
else 0
```

### 4. Total Financial Impact (`Total_Financial_Report`)
Combines direct vendor maintenance/repair bills (`Cost_USD($)`) with indirect opportunity revenue loss:
$$\text{Total Financial Impact} = \text{Repair Cost (USD)} + \text{Estimated Revenue Loss}$$

### 5. Risk Tier Classification (`Impact_Degree`)
Categorizes total financial exposure into operational risk tiers:
* **Low Impact:** $<\$2,000$
* **Medium Impact:** $\$2,000 - \$8,000$
* **High Impact:** $>\$8,000$

---

## 🔍 Key Business Insights & Analytical Takeaways

1. **High SLA Breach Rates:** Over **80%+** of equipment downtime events exceeded their modality SLA targets, indicating significant bottlenecks in vendor dispatch times or spare part availability (`Awaiting Vendor` / `Pending Parts`).
2. **CT Scanners & MRIs Drive Financial Loss:** Although X-Ray and Ultrasound account for frequent maintenance requests, **MRI and CT Scanners** generate the majority of total financial impact due to high hourly patient revenue rates and high direct repair costs.
3. **Outlier Impact:** Severe downtime incidents (e.g., equipment offline for over 48 hours) heavily skew total financial figures, reinforcing the need for proactive preventative maintenance contracts rather than reactive repair models.

---

## 💻 How to Run the Dashboard

1. Download or clone this repository.
2. Navigate to the project directory.
3. Double-click **`dashboard.html`** to open the interactive dashboard directly in any web browser (Chrome, Edge, Firefox, Safari). No server or database installation required!