

Group 1 Dataset: `Retail_Sales_Data.csv`

 Dataset Description

The dataset represents **retail sales transactions** collected from multiple store branches. It includes details such as sales amount, product category, payment method, and customer satisfaction ratings.

**Key Information:**
- **Total Records:** 1,199 transactions
- **Columns:**
  - Transaction_ID
  - Date
  - Branch
  - Product_Category
  - Product_Name
  - Quantity
  - Unit_Price
  - Total_Sales (Cedis)
  - Payment_Method
  - Customer_Satisfaction

Sample Rows:
| Transaction_ID | Date       | Branch   | Product_Category | Product_Name | Quantity | Unit_Price | Total_Sales | Payment_Method | Customer_Satisfaction |
|-----------------|------------|----------|------------------|--------------|-----------|-------------|--------------|----------------|------------------------|
| T1000 | 07/01/2024 | Kumasi | Electronics | Smartwatch | 7 | null | null | Mobile Money | 4 |
| T1001 | 28/06/2024 | Kumasi | Groceries | Rice | 3 | 173.42 | 520.26 | Cash | 6 |
| T1002 | 04-07-24 | Takoradi | Electronics | Charger | 7 | null | null | Mobile Money | 6 |


Data Cleaning Steps (Power Query Operations)

Below are the data preparation and cleaning operations performed in Microsoft Power BI’s Power Query Editor:

| Step | Column(s) Affected | Issue Identified | Cleaning Action | Justification |
|------|--------------------|------------------|------------------|----------------|
| 1 | `Date` | Inconsistent date formats (`07/01/2024`, `04-07-24`) | Converted all to **Date** type using conditional logic and helper columns | Ensures consistent and accurate date analysis |
| 2 | `Transaction_ID` | Missing or inconsistent ID patterns | Split by non-digit/digit, replaced blanks with “T”, filled down, and merged columns | Maintained unique identifiers without disrupting data order |
| 3 | `Quantity` | 9 missing values | Replaced nulls with **average value (6)** | Average used since range (1–10) had no outliers |
| 4 | `Unit_Price` | 12 N/A and 22 missing values | Changed data type to decimal; imputed **median (₵307.30)** | Median chosen due to right-skew and presence of outliers |
| 5 | `Total_Sales` | Missing and inconsistent types | Converted to decimal; imputed **median (₵1,446.15)** | Median provides robustness against extreme values |
| 6 | `Payment_Method` | Inconsistent text casing | Transformed to **“Capitalize Each Word”** | Ensures uniform category labels (e.g., “Mobile Money”) |
| 7 | `Customer_Satisfaction` | Mixed text/number formats; missing entries | Mapped text to numerical scale (1–6); replaced blanks with average rating per product using **Group By** and **Merge** | Standardized satisfaction ratings and retained data meaning |
| 8 | All Text Columns | Extra spaces and inconsistent casing | Applied **Trim** and **Format as Proper Case** | Improved readability and prevented duplicates |
| 9 | General | Checked for unique categories and duplicates | Verified uniqueness and removed redundant rows | Ensured data integrity |

---

## Power Query Transformations

Date Correction Formula:
```powerquery
= if [#"Date - Copy"] = #date(1000, 1, 1) 
then Date.FromText(Text.Middle([Date], 3, 2) & "/" & Text.Start([Date], 2) & "/" & Text.End([Date], 4)) 
else [#" Date - Copy"]
```

**Customer Satisfaction Imputation (Conceptual Steps):**
1. Group by `Product_Name` and compute **average rating**.
2. Merge grouped table with main table (Left Outer Join).
3. Add a custom column:
   ```powerquery
   if [Customer_Satisfaction] = null 
   then [AverageRating] 
   else [Customer_Satisfaction]
   ```
4. Rename new column → `Customer_Satisfaction_Filled`.


Challenges Faced

1. Inconsistent Transaction IDs:
   - Some IDs were missing or malformed.
   - Creating new index-based IDs risked disrupting the dataset’s original order, so a reconstruction method using split and merge logic was adopted.

2. Mixed Date Formats:
   - Various date structures (`07/01/2024`, `04-07-24`) caused errors during conversion.
   - Required building a helper column and conditional transformation logic.

3. Handling Missing Numerical Data:
   - Several numeric columns had missing or skewed distributions.
   - Median imputation was preferred to prevent distortion from outliers.

4. Text Inconsistencies:
   - Variations in casing and spacing (e.g., “mobile money” vs. “Mobile Money”) affected grouping and visualization accuracy.

5. Customer Satisfaction Mapping:
   - Values existed both as text and numbers (e.g., “good” and “4”).
   - Required developing a scale (1–6) and imputing missing values using average per product.

