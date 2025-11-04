# Sanford-Export-Analysis-FY-2023-FY-2025-_-by-Eshwar-Reddy-cholleti
Sanford Export Analysis (FY 2023 – FY 2025)_by Eshwar Reddy cholleti
## **Overview**  
This project was completed as part of the *Business Intelligence Analyst Technical Assessment* for **Sanford New Zealand**.  
The goal was to analyse three years of aquaculture export data and develop an interactive **Power BI dashboard** to support strategic business decisions around:  

- **Right Market:** Which countries offer the best growth and pricing opportunities?  
- **Right Product:** Which species and products are most profitable?  
- **Right Price:** What is the optimal pricing strategy by market?  

Deliverables include a Power BI report (`Sanford_Export_Analysis.pbix`), an SQL ETL script, and this README summarising methodology, visual design, and key findings.

---

## **Data Sources**
Three CSV files were provided in the assessment brief:  

| File | Description | Year |
|------|--------------|------|
| `exports-by-product-jul-23.csv` | NZ aquaculture export data | FY 2023 |
| `exports-by-product-jul-24.csv` | NZ aquaculture export data | FY 2024 |
| `exports-by-product-jul-25.csv` | NZ aquaculture export data | FY 2025 |

**Columns:** `Species`, `Product`, `Country`, `Volume (kg)`, `Value (NZD)`

---

## **ETL / Data Preparation**
A simple SQL ETL script (`combine_exports.sql`) was used to merge the three yearly CSV datasets into one master table **Exports_AllYears**, adding a `Year` column for each file.

```sql
-- Combine FY23–FY25 data into one table
CREATE TABLE dbo.Exports_AllYears (
    Year INT,
    Species NVARCHAR(100),
    Product NVARCHAR(150),
    Country NVARCHAR(150),
    [Volume (kg)] DECIMAL(18,2),
    [Value (NZD)] DECIMAL(18,2)
);

INSERT INTO dbo.Exports_AllYears
SELECT 2023, Species, Product, Country, [Volume (kg)], [Value (NZD)] FROM dbo.Exports_FY23
UNION ALL
SELECT 2024, Species, Product, Country, [Volume (kg)], [Value (NZD)] FROM dbo.Exports_FY24
UNION ALL
SELECT 2025, Species, Product, Country, [Volume (kg)], [Value (NZD)] FROM dbo.Exports_FY25;
GO


-----

## Power BI Model & Measures

Main Table: Exports_AllYears
Key Columns: Species, Product, Country, Year, Volume (kg), Value (NZD)

## **DAX Measures** :

-Total Value = SUM('Exports_AllYears'[Value])
-Total Volume = SUM('Exports_AllYears'[Volume])
-Avg Price per Kg = DIVIDE([Total Value], [Total Volume])

Growth % FY25 vs FY23 =
VAR V23 = CALCULATE([Total Value], 'Exports_AllYears'[Year] = 2023)
VAR V25 = CALCULATE([Total Value], 'Exports_AllYears'[Year] = 2025)
RETURN DIVIDE(V25 - V23, V23)

## **Formatting & Metadata**:

Country → Data Category = Country/Region (for maps).

Value, Volume → Numeric (Decimal)

Year → Whole Number

Measures formatted as NZD or Percentage

-----
## **Dashboard Structure**

--Right Market – Top Growth & Pricing Markets

Visuals:

Bubble Map → Location: Country, Bubble Size: Total Value, Color: Avg Price per Kg

-Bar Chart 1: Top Markets by Total Export Value (FY25)

-Bar Chart 2: Growth % FY25 vs FY23 by Country

Slicers: Year, Species

--Key Insights:

-The United States, Australia, and China are the top export markets by value.

-Rapid growth observed in Solomon Islands, Malaysia, and New Caledonia.

-Premium pricing achieved in Japan and European markets.

----
Right Product – Most Profitable Species & Products

Visuals:

Column Chart: Total Value by Species

Bar Chart: Average Price per Kg by Species

Treemap: Total Value by Product (e.g., “Frozen Half Shell Mussels”)

Year Slicer for interactivity

Key Insights:

Mussels contribute ~70% of total revenue through high volume.

Salmon commands the highest price per kg (~NZD 30/kg).

Mussel Oil has low volume but exceptional unit value → high-margin niche product.

Right Price – Optimal Pricing Strategy by Market

Visuals:

Scatter Chart: X = Total Volume | Y = Avg Price per Kg | Size = Total Value | Detail = Country

Line Chart: Avg Price per Kg by Year & Species

Matrix/Table: Country, Total Value, Volume, Avg Price per Kg, Growth %

## **Key Insights**:

Inverse relationship between price and volume: high-volume markets (US, China) are price-sensitive.

Premium markets (Japan, EU) sustain higher prices year-on-year.

Recommended dual pricing strategy:

Maintain competitive pricing in bulk markets.

Focus on margin growth in premium markets.

## **Key Findings Summary**

Focus	Findings
Right Market	Growth concentrated in Asia-Pacific; premium pricing in Japan & EU.
Right Product	Mussels dominate revenue; Salmon drives premium pricing.
Right Price	Price-volume trade-off indicates need for market segmentation strategy.
Overall Strategy	Combine volume defense with premium market expansion.


## **Tools Used **
Tool	Purpose
Power BI Desktop	Data modeling, measures, and dashboard creation
SQL Server (T-SQL)	ETL script for merging yearly datasets
Excel / CSV	Raw data inspection and validation
GitHub	Version control and submission platform
