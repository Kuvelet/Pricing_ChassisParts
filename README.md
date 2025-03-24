# Smart Pricing in the Chassis Market

## Table of Contents

- [Project Overview](#project-overview)

- [Tools](#tools)

- [Strategy](#strategy)

- [Data Sources](#data-sources)

- Regression(#regression-analysis)

- [Analysis](#analysis)

- [Results & Findings](#results--findings)

- [Next Steps](#next-steps)

## Project Overview

### Why Pricing Strategy is Crucial for Chassis Parts in a Competitive Market?

#### 1. **High Competition and Price Sensitivity**
- Chassis parts are highly commoditized, making price a key differentiator.
- Customers often choose suppliers based primarily on price comparisons.

#### 2. **Profit Margin Protection**
- Intense competition often forces price reductions, squeezing profit margins.
- Strategic pricing balances competitiveness with maintaining healthy margins.

#### 3. **Differentiation Through Value**
- Pricing reflects perceived product value (quality, warranty, durability).
- Strategic pricing allows for differentiation beyond just low prices.

#### 4. **Clear Market Positioning**
- Pricing signals whether your brand is budget, mid-range, or premium.
- Proper positioning helps attract the right customer segments and reinforces brand identity.

#### 5. **Adaptability to Market Changes**
- Raw material costs, regulatory shifts, and market demand frequently fluctuate.
- Dynamic pricing strategies help businesses adapt quickly to these changes.

#### 6. **Customer Loyalty and Retention**
- Pricing incentives (volume discounts, loyalty programs) encourage repeat business.
- Retaining existing customers is critical in a market crowded with alternatives.

#### 7. **Long-Term Competitive Sustainability**
- Effective pricing ensures both short-term sales growth and long-term business sustainability.
- Businesses that proactively manage prices thrive despite ongoing market pressure.

## Tools
- Python
- XL

## Strategy

My approach involves leveraging Python to systematically scrape our own brand's wholesale prices for 20,000 unique SKUs from the largest car parts wholesaler on the web. This will serve as the foundation for our pricing analysis. Once we have collected this data, I will proceed to scrape the wholesale prices of our competitors for each SKU available in our catalog, ensuring we have a comprehensive view of the market landscape.

Since we already know the prices at which we sell our products to wholesalers, I will conduct a regression analysis to establish the relationship between our sales prices and the corresponding wholesale prices. This will allow us to determine the markup that wholesalers apply when selling our parts. To ensure the validity of our findings, I will evaluate the statistical significance of the regression equation and refine our approach accordingly. If the analysis reveals multiple distinct markup patterns based on material type or product category, I will conduct separate regression analyses for each categorical attribute to capture these nuances. If markup variations exist based on material type or product category, I will conduct separate regression analyses for each categorical attribute.

Once the regression models are established, I will use them inversely to estimate the selling prices of each competitors SKU to the wholesaler.

Finally, I will refine our pricing strategy by assessing whether adjustments—either increases or decreases—are warranted. This decision will be guided by key factors such as profit margin protection, market positioning.

The goal is to determine whether it is more advantageous to increase or decrease our prices to optimize profitability while maintaining a strong competitive position in the market.

## Data Sources

- CSV of scraped price data. It includes two columns: 'Cost to Retailer', which represents the actual cost at which we sell each product to the retailer, and 'Own Brand Retail Price', which reflects the publicly listed price obtained through Python-based web scraping. This dataset forms the foundation of our analysis, allowing us to compare our internal pricing with external market data and uncover the markup patterns applied by wholesalers.
- Excel File with Analysis

## Regression

### Objective

This analysis models the relationship between the **Cost to Retailer** and the **Own Brand Retail Price** for 20,000 automotive SKUs. By understanding how much retailers typically mark up our parts, we can optimize our own pricing strategy—balancing profitability with market competitiveness.

### Why I Grouped the Data

We grouped and trained a **separate linear regression model** for each unique combination of **Category** (e.g., Control Arm, Ball Joint, Tie Rod) and **Material** (e.g., Steel, Aluminum). Pricing behaviors differ significantly between product types and materials. For example, Control Arms may have higher retail markups than Ball Joints, and Aluminum parts might follow a different pricing pattern than Steel parts. Grouping allows us to capture these nuances for more accurate and actionable insights.

## Regression Implementation

The data is stored in the `Report` sheet with the following columns:

| Column | Header                 | Description                |
|--------|------------------------|----------------------------|
| B      | Category               | Product category           |
| C      | Material               | Product material           |
| E      | Cost_to_Retailer       | Our wholesale price        |
| G      | Own_Brand_Retail_Price | Marketplace retail price   |

I used  `LET`, `FILTER`, and `INDEX` functions to calculate regression values dynamically without creating separate sheets. For a specific combination like **Control Arm (Steel)**, the formulas are as follows:

### Intercept

```excel
=LET(
  filteredData, FILTER(Report!A14:H20013, (Report!B14:B20013="Control Arm") * (Report!C14:C20013="Steel")),
  INTERCEPT(INDEX(filteredData,,7), INDEX(filteredData,,5))
)
```

This formula filters the data to include only rows where Category is "Control Arm" and Material is "Steel", then calculates the intercept of the linear regression using column 5 (`Cost_to_Retailer`) as X and column 7 (`Own_Brand_Retail_Price`) as Y.

### Slope

```excel
=LET(
  filteredData, FILTER(Report!A14:H20013, (Report!B14:B20013="Control Arm") * (Report!C14:C20013="Steel")),
  SLOPE(INDEX(filteredData,,7), INDEX(filteredData,,5))
)
```

This returns the slope (markup multiplier) of the linear regression line based on the filtered dataset.

### R-squared

```excel
=LET(
  filteredData, FILTER(Report!A14:H20013, (Report!B14:B20013="Control Arm") * (Report!C14:C20013="Steel")),
  RSQ(INDEX(filteredData,,7), INDEX(filteredData,,5))
)
```
This tells : "How much of the variation in retail prices for Control Arm (Steel) can be explained by the variation in your cost to retailers?"

Here are some of the results
![Picture3](https://github.com/user-attachments/assets/32a03206-564b-4eed-9996-3d125c2fc61e)


