# Smart Pricing in the Chassis Market

## Table of Contents

- [Project Overview](#project-overview)

- [Tools](#tools)

- [Strategy](#strategy)

- [Data Sources](#data-sources)

- [Regression](#regression)

- [Pricing](#pricing)

- [Results & Findings](#results--findings)

- [Next Steps](#next-steps)

## Project Overview

### Why Pricing Strategy is Crucial for Chassis Parts in a Competitive Market?

In the competitive chassis parts market, price is a major factor as products are often commoditized and chosen based on cost. This puts pressure on profit margins, making strategic pricing essential to stay competitive without sacrificing profitability. Beyond low prices, value-based pricing—reflecting quality, warranty, and durability—helps differentiate brands. Pricing also defines market position, signaling whether a brand is budget, mid-range, or premium. With constant shifts in raw material costs and demand, dynamic pricing ensures adaptability. Incentives like volume discounts and loyalty programs boost customer retention. In the long run, smart pricing drives both growth and sustainability.

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

> **Note:** SKU names and pricing data is altered for confidentiality purposes


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

Here are some of the results:
![Picture3](https://github.com/user-attachments/assets/32a03206-564b-4eed-9996-3d125c2fc61e)
![Picture1](https://github.com/user-attachments/assets/8debbfba-7f73-4f0c-af2f-fa7df53cf153)

| Dataset                 | Intercept     | Slope       | R-squared   |
|-------------------------|---------------|-------------|-------------|
| Control Arm & Steel     | 0.21283264    | 1.42157791  | 0.99609570  |
| Ball Joint & Steel      | -0.07709675   | 1.47424611  | 0.99662970  |
| Tie Rod & Steel         | 0.03821035    | 1.37796334  | 0.99625222  |
| Tie Rod End & Steel     | -0.435188526  | 1.523054403 | 0.996383173 |
| Lateral Arm & Steel     | -0.931043285  | 1.45020727  | 0.996640802 |
| Control Arm & Aluminum  | -0.194223819  | 1.57691227  | 0.996511734 |
| Ball Joint & Aluminum   | -0.569224642  | 1.630559283 | 0.996389419 |
| Tie Rod & Aluminum      | 0.200281369   | 1.521492023 | 0.996425862 |
| Tie Rod End & Aluminum  | -0.103081544  | 1.679375693 | 0.996434024 |
| Lateral Arm & Aluminum  | -0.626185764  | 1.602068379 | 0.996676162 |

## Pricing

**1)** The pricing Excel tab contains structured data for each product SKU to support strategic pricing analysis. Each row represents a unique product and includes its **SKU**, which serves as the unique identifier, along with its **Category** (such as Ball Joint or Control Arm) and **Material** (e.g., Steel or Aluminum), which can influence pricing patterns. The **Own_Vendor_LDP** column reflects the Landed Duty Paid cost—our actual cost to bring the product into inventory, including shipping, duties, and associated fees. The **Cost_to_Retailer** column shows the current wholesale price we offer to retailers. Together, these fields form the foundation of our pricing model, allowing us to calculate markup, compare against competitors, and identify opportunities for pricing optimization.

**2)** 
- The **Own_Markup** column represents the markup factor we apply when pricing a product for wholesale. It is calculated by dividing the **Cost_to_Retailer** by the **Own_Vendor_LDP** (our landed cost). In other words, this value shows how much higher our selling price to the retailer is compared to our actual cost of acquiring the product. A markup of 1.5, for example, indicates that the product is sold to the retailer at a price that is 50% higher than our landed cost. This field is crucial for understanding profitability on a per-product basis and plays a key role in determining whether proposed pricing adjustments maintain sufficient margin. It also serves as a constraint in our pricing strategy to ensure we never set prices that result in a markup below a threshold (e.g., 1.4), which could jeopardize financial viability.

- **Own_Brand_Retail_Price**: This column represents the current retail price of our own brand’s product as listed publicly (e.g., on marketplaces or our partner websites). It reflects the final price a consumer would see when purchasing the product.

- **Competitor_Retail_Price**: This column shows the retail price of the equivalent competitor’s product, sourced through web scraping or market intelligence tools. It provides a direct benchmark for evaluating our positioning in the marketplace.

- **Retail Price Diff**: This column calculates the percentage difference between our retail price and the competitor’s retail price. A negative value indicates our price is cheaper than the competitor’s, while a positive value indicates we are more expensive. This metric helps identify opportunities for retail price adjustment to improve competitiveness or profitability.

**3)** **PREDICTION of Competitors Cost to Retailer Using Regression** column contains the estimated wholesale price that competitors are likely offering to retailers, derived through a regression model. Since actual competitor wholesale prices are not publicly available, we developed a regression model using our internal **Price Sold** data and corresponding **Online Retail Prices** to understand the relationship between retail and wholesale pricing.

Once trained and validated, this model is applied inversely to the **Competitor_Retail_Price** values, allowing us to estimate what our competitors might be charging their retailers behind the scenes. The result is a predicted **Cost to Retailer** from the competitor's side.

This field is essential for benchmarking our own **Cost_to_Retailer** values, identifying whether we are competitively priced in the B2B space, and informing our pricing strategy decisions.

To dynamically estimate the **Predicted Competitor Cost to Retailer**, we use an `IFS` formula in Excel that references the correct regression model for each unique combination of **Category** and **Material**.

The formula checks the product's classification (columns `B` and `C`, e.g., `"Control Arm"` and `"Steel"`) and matches it with the corresponding regression parameters (Intercept and Slope) stored in a separate model summary table (e.g., in cells `$B$2:$C$11`). Each row in that table contains the **intercept** and **slope** for a specific Category–Material pair.

The formula works as follows:

```excel
=IFS(AND(B14="Control Arm",C14="Steel"),(H14-$B$2)/$C$2,AND(B14="Ball Joint",C14="Steel"),(H14-$B$3)/$C$3,AND(B14="Tie Rod",C14="Steel"),(H14-$B$4)/$C$4,AND(B14="Tie Rod End",C14="Steel"),(H14-$B$5)/$C$5,AND(B14="Lateral Arm",C14="Steel"),(H14-$B$6)/$C$6,AND(B14="Control Arm",C14="Aluminum"),(H14-$B$7)/$C$7,AND(B14="Ball Joint",C14="Aluminum"),(H14-$B$8)/$C$8,AND(B14="Tie Rod",C14="Aluminum"),(H14-$B$9)/$C$9,AND(B14="Tie Rod End",C14="Aluminum"),(H14-$B$10)/$C$10,AND(B14="Lateral Arm",C14="Aluminum"),(H14-$B$11)/$C$11)
```

1. For each SKU, we identify its **Category–Material pair** (e.g., "Ball Joint–Steel", "Control Arm–Aluminum").
2. We look up the **correct regression model parameters** (Intercept, Slope, and R²) for that specific pair from a model summary table stored in the Excel workbook (range **A1:D11**).
3. We apply the formula above:
   - **Subtract** the Intercept value from the **Competitor Retail Price**
   - **Divide** the result by the Slope
4. The output is the **Predicted Competitor Cost to Retailer** — the estimated B2B price competitors are likely offering to retailers for that SKU.








