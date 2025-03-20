# Smart Pricing in the Chassis Market

## Table of Contents

- [Project Overview](#project-overview)

- [Tools](#tools)

- [Strategy](#strategy)

- [Data Sources](#data-sources)

- [Data Cleaning & Preparation](#data-cleaning--preparation)

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

