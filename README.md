### Commercial Real Estate (CRE) Redevelopment Tender Optimization Dashboard

### 📌 Executive Project Overview
This repository contains an end-to-end data analytics and procurement optimization project evaluating competing multi-tier redevelopment tenders spanning **2025 to 2026**. The interactive Tableau  dashboard aligns complex commercial rental timelines against qualitative builder operational risks to facilitate executive vendor selection and data-driven shortlisting. 

### 🚀 Interactive Dashboard Preview

[Dashboard](Dashboard.png)

*👉 [https://public.tableau.com/app/profile/mohammad.nadim.khan/viz/RedevelopmentKACHS/TenderSummary]* 

### 🛠️ Data Pipeline & Architecture
The data engineering and business intelligence workflow consisted of four key phases: 
1. **Data Ingestion:** Extracted unstructured pricing timelines from 6 distinct tender PDF documents using ChatGPT to parse layout strings into structured CSV rows.
2. **Data Transformation (Excel):** Standardized local currency symbols (₹), parsed mixed string markers, and type-cast records to clear numerical data types.
3. **Data Modeling (Tableau):** Engineered a cross-sheet relational data model pairing financial terms against developer history metrics using custom string relationship calculations (SPLIT and REGEXP).
4. **Interactive BI Layer:** Deployed cross-sheet action filtering across a 5-chart matrix to allow stakeholders to isolate individual builders dynamically.

### 📊 Business Insights & Visual Matrix
* **Chart 1: Commercial Rent Escalation:** Tracks the 35-month multi-tier step-up contract outlays to compare financial introductory rates against real long-term costs.
* **Chart 2: Additional Carpet Area:** Carpet Area Comparison of all the bidders.
* **Chart 3: Capability Scorecards:** Cross-references qualitative timelines (approval speeds, legal track records) into actionable executive risk matrices.
* **Chart 4: Execution Scale:** Uses a Dual-Axis line/bar configuration to contrast years in business against completed projects to assess vendor experience.
* **Chart 5: Bank Guarantees:** Measures capital backing across negotiating rounds to protect against structural project abandonment risks.


### 🎯 Strategic Negotiation Outcomes
* Successfully modeled pricing trends across initial and revised tender cycles.
* Streamlined stakeholders' evaluation process from 6 initial applicants down to the 3 highest-viability final contenders based on structural data metrics.

---

### 📖 Data Dictionary & Model Specification
This section details the schema architecture, column definitions, data types, and transformation rules applied during the ETL pipeline to ensure strict data governance. 

### 🏗️ Relational Schema Design
The data model uses a **Star-like Relational Schema** inside Tableau. Rather than blending or flattening the data in Excel (which creates redundancy), the tables are joined dynamically via logical relationships using a standardized text key. 

```text
                      [ Table: Top3_Builder_History ] (Dimension/Master)
                                     |
               +---------------------+---------------------+

               |                                           |
               | (Link: Criteria = Split_Name)             | (Link: Criteria = Builder_Name)
               v                                           v
[ Table: Original_Builder_Offers ] (Fact)    [ Table: Revised_Builder_Offers ] (Fact)
```

#### Key Relationship Logic:
* **Primary Key Field:** Criteria in the Top3_Builder_History table acts as the standardized master identifier (Short Name: e.g., *Sanghvi*, *Khoker*, *Rupa*).
* **Custom Text Split (Original Offers Join):** The original offers table used non-standardized full corporate entities (e.g., *Sanghvi Lifespace Private Limited*). A custom Tableau relationship calculation was engineered to isolate the first string component for a clean join: 
```tableau
SPLIT([Builder Name], " ", 1)
```

---

### 📊 Field Metadata Reference

#### Table A: Original_Builder_Offers (Fact Table)
* **Source:** Extracted from initial procurement tender PDFs (6 Rows).
* **Granularity:** One row per participating developer bid.

| Field Name | Data Type | Tableau Role | Cleaned From | Description / Business Rule |
| :--- | :--- | :--- | :--- | :--- |
| **Builder Name** | String | Dimension | Text (Raw) | Full legal corporate entity name of the developer. |
| **Carpet Area** | Decimal (#.#) | Measure | Text ("35%") | Percentage of extra carpet area offered to existing tenants. Converted from percentage string to decimal. |
| **Corpus Fund** | Whole (#) | Measure | Text ("₹1,300 &...") | Fixed financial corpus payout offered to society. Extracted text up to space boundaries using TEXTBEFORE logic. |
| **Rent 1 to 11 Months** | Whole (#) | Measure | Text ("₹80/sq.ft...") | Monthly rent rate per sq. ft. for the first phase. Extracted numeric substring right before the first slash (/) delimiter. |
| **Rent 12 to 23 Months** | Whole (#) | Measure | Text ("₹100/sq.ft...") | Monthly rent rate per sq. ft. for the second phase. Standardized to numeric values. |
| **Rent 24 to 35 Months** | Whole (#) | Measure | Text ("₹120/sq.ft...") | Monthly rent rate per sq. ft. for the final phase. Standardized to numeric values. |
| **Bank Guarantee (Cr)** | Decimal (#.#) | Measure | Text ("₹1.25 Crores") | Financial performance guarantee in Indian Crores (₹10M). Unmatched non-numeric strings cast to NULL. |

#### Table B: Revised_Builder_Offers (Fact Table)
* **Source:** Extracted from round-two commercial negotiation documents (3 Rows).
* **Granularity:** One row per shortlisted final contender.

| Field Name | Data Type | Tableau Role | Cleaned From | Description / Business Rule |
| :--- | :--- | :--- | :--- | :--- |
| **Builder Name** | String | Dimension | Raw Entry | Standardized short name of the shortlisted developer used to drive master filtering. |
| **Revised Carpet Area** | Decimal (#.#) | Measure | Numeric | Renegotiated carpet area percentage adjustments. |
| **Revised Corpus Fund** | Whole (#) | Measure | Numeric | Escalated or adjusted corpus payouts post-negotiation rounds. |
| **Revised Rent 1-11** | Whole (#) | Measure | Numeric | Optimized monthly rental tier after commercial squeezing. |
| **Revised Rent 12-23** | Whole (#) | Measure | Numeric | Optimized rental tier for months 12 to 23. |
| **Revised Rent 24-35** | Whole (#) | Measure | Numeric | Optimized rental tier for months 24 to 35. |
| **Revised Bank Guar** | Decimal (#.#) | Measure | Numeric | Updated performance security deposit minimum requirements. |

#### Table C: Top3_Builder_History (Dimension / Reference Table)
* **Source:** Compiled from historical municipal development logs and tracking records.
* **Granularity:** Operational and qualitative profile metrics per developer.

| Field Name | Data Type | Tableau Role | Description / Business Rule |
| :--- | :--- | :--- | :--- |
| **Criteria** | String | **Primary Key** | Core relational identifier matching short-form builder names. |
| **Years in Business** | Whole (#) | Measure | Continuous market experience tracking (Total operational years). |
| **Total Projects Comp** | Whole (#) | Measure | Cumulative volume metrics for completed real estate assignments. |
| **Timely Delivery Track** | String | Dimension | Qualitative speed category evaluating developer deadline precision. |
| **Paperwork Speed** | String | Dimension | Operational bureaucracy rating detailing approval velocity with local government bodies. |
| **Construction Quality** | String | Dimension | Evaluation rating of building structural integrity based on engineer audits. |
| **Financial Stability** | String | Dimension | Creditworthiness and corporate liquid backing rating. |
| **Government Awards** | Whole (#) | Measure | Documented count of formal municipal and industry recognitions. |

---

### ⚙️ Advanced Transformation & Regex Enforcements
During pipeline ingestion, hidden formatting spaces from the PDF conversion phase caused values to throw parsing errors in Tableau. To resolve this without manual data entry corrections, data types were structurally enforced inside Tableau using the following calculation fields: 

### Robust Substring Numeric Extraction
To completely isolate true integer sequences from trailing string characters or leftover currency marks, a targeted Regular Expression extraction was deployed: 
```tableau
// Field: Clean Rent 1 to 11 Months
ZN(INT(REGEXP_EXTRACT(STR([Rent for first 11 months]), '(\d+)')))
```
