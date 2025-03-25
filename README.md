# Smart Pricing in the Chassis Market

## Table of Contents

- [Project Overview](#project-overview)

- [Tools](#tools)

- [Strategy](#strategy)

- [Data Sources](#data-sources)

- [Regression](#regression)

- [Pricing](#pricing)

- [Results & Impact](#results--impact)

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
=IFS(
  AND(B14="Control Arm",C14="Steel"),       (H14-$B$2)/$C$2,
  AND(B14="Ball Joint",C14="Steel"),        (H14-$B$3)/$C$3,
  AND(B14="Tie Rod",C14="Steel"),           (H14-$B$4)/$C$4,
  AND(B14="Tie Rod End",C14="Steel"),       (H14-$B$5)/$C$5,
  AND(B14="Lateral Arm",C14="Steel"),       (H14-$B$6)/$C$6,
  AND(B14="Control Arm",C14="Aluminum"),    (H14-$B$7)/$C$7,
  AND(B14="Ball Joint",C14="Aluminum"),     (H14-$B$8)/$C$8,
  AND(B14="Tie Rod",C14="Aluminum"),        (H14-$B$9)/$C$9,
  AND(B14="Tie Rod End",C14="Aluminum"),    (H14-$B$10)/$C$10,
  AND(B14="Lateral Arm",C14="Aluminum"),    (H14-$B$11)/$C$11
)
```
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

- For each SKU, we identify its **Category–Material pair** (e.g., "Ball Joint–Steel", "Control Arm–Aluminum").
- We look up the **appropriate regression model parameters**—Intercept, Slope, and R²—for that specific pair from a model summary table stored in the Excel workbook (range **A1:D11**).
- We apply the reverse regression formula to estimate the competitor’s cost:
  - **Subtract** the Intercept value from the **Competitor Retail Price**
  - **Divide** the result by the Slope
- The result is the **Predicted Competitor Cost to Retailer**—an estimate of the price competitors are likely offering to their retail partners for that SKU.

The **Difference (%) of Cost to Retailer** column measures how our current wholesale price (Cost to Retailer) compares to the **Predicted Competitor Cost to Retailer** generated through regression analysis. This percentage indicates whether we are offering our product to retailers at a higher or lower cost than our competitors.

**4)** In the aftermarket automotive industry—especially within the chassis parts segment—**Cost to Retailer** is a foundational element of a successful pricing strategy. This figure represents the wholesale price we charge our retail partners, and it directly influences how our products are priced, positioned, and prioritized in the market.

Retailers operate in highly competitive environments where margins are thin and pricing flexibility is key. If our wholesale price is too high relative to the competition, retailers may either pass on that cost to consumers—potentially making our products less attractive—or prioritize competing brands that allow for better profitability. On the other hand, if our pricing allows for healthy retailer margins while remaining competitive at the consumer level, it leads to higher sales volumes, stronger reorder rates, and long-term partnerships.

A well-positioned Cost to Retailer also increases the likelihood that our products will be featured more prominently on the retailer’s website or platform. Online retailers often highlight top-value or competitively priced parts in “featured” sections, price filters, or seasonal promotions. When our products help retailers hit their margin targets while staying competitively priced, they are more likely to receive favorable visibility—ultimately driving more traffic and conversions for our brand.

Another critical factor is inventory behavior. Retailers make stocking decisions based not only on demand forecasts, but also on how each SKU contributes to their bottom line. A strategically optimized cost to retailer encourages retailers to stock more units of our product, allocate more shelf or digital space, and even phase out slower-moving competitors. This deeper integration into their inventory mix increases availability to end customers and reinforces our presence in the supply chain.

Additionally, **many online retailers operate their own private label brands**, often sourcing parts directly from manufacturers at lower costs. In this context, **Cost to Retailer becomes even more important**—not just as a function of margin, but as a factor in the retailer’s decision whether to support our branded product over their own in-house alternatives. Even if our product offers superior quality, retailers must be confident that it can still deliver competitive profitability. Our pricing must strike the right balance between value and margin potential to win placement over private label offerings.

Finally, our pricing also influences the broader supplier-retailer relationship. A fair and data-informed Cost to Retailer signals to our partners that we understand their business needs and are committed to sustainable, long-term collaboration. It builds trust and opens doors to joint marketing efforts, preferred supplier status, and collaborative growth opportunities.

In short, **Cost to Retailer is not just a pricing lever—it’s a strategic tool** that influences everything from digital visibility and inventory prioritization to competitive positioning and brand preference. In a crowded and commoditized market like aftermarket chassis parts, optimizing this one number can deliver outsized returns across the value chain.

---

I use this logic to set our **Target Cost to Retailer** based on how our current wholesale price compares to the **Predicted Competitor Cost to Retailer** (calculated using regression analysis). The goal is to remain competitively priced — ideally 5% below competitors — without sacrificing margin unnecessarily.

- **If our cost is lower than the competitor’s:**
  - If it's **more than 5% lower**, we **raise it** to be exactly **5% cheaper** — avoiding excessive discounting.
  - If it's **within 5%**, we **keep our current price** — no change needed.

- **If our cost is higher than the competitor’s:**
  - We **lower our price** to be exactly **5% below** the predicted competitor cost — to regain a pricing advantage.

This logic ensures our pricing is **data-driven**, **competitive**, and **margin-conscious**.

```excel
=IF(E14<J14,
    IF(E14/J14<0.95,
        J14*0.95,
        E14),
    J14*0.95
)
```

**5)** As part of our pricing framework, we enforce a strict company policy to **never sell any product below a markup (MUP) of 1.4**. This ensures that every transaction maintains a minimum level of profitability, accounting for operational costs, market volatility, and long-term sustainability.

When competitive pricing pressures arise — particularly when a competitor’s predicted wholesale price requires us to lower our own — we first evaluate whether the **Target Cost to Retailer** still meets the minimum markup threshold. This is calculated in the **Is Target Cost Achievable**

```excel
=IF(L14/D14>=1.4,"Yes","No")
```

If matching or undercutting a competitor’s price would **push our markup below 1.4**, we do **not proceed with the discount**. 

Instead, this situation is interpreted as a signal that **we are overpaying our vendor** for that product.

In such cases, the pricing model flags the SKU as needing **vendor price renegotiation**. This prevents us from eroding margins in pursuit of competitiveness and ensures that pricing remains sustainable. It also helps us identify opportunities to revisit supplier agreements and strengthen our cost structure over time.

This safeguard allows us to remain aggressive in the market when possible — but never at the cost of profitability.

---

The **Final Cost to Retailer** column represents the final, approved wholesale price we are willing to offer for a specific SKU — after evaluating both competitive pricing and profitability constraints. It is determined using the following formula:

```excel
=IF(M15="Yes", L15, D15*1.4)
```

- M Column = Is Target Cost Achievable (returns "Yes" or "No")

- L Column = Target Cost to Retailer (based on competitor pricing logic)

- D Column = Own Vendor LDP (our internal landed cost)

- **If the target cost is achievable** (i.e., it results in a markup ≥ 1.4):
  - We proceed with the **Target Cost to Retailer (`L15`)** as our final price.
  - This means we can competitively price the product **without violating our minimum profitability policy**.

- **If the target cost is not achievable** (i.e., the markup would fall below 1.4):
  - We fall back to a **minimum allowed price** that still respects our margin threshold.
  - This is calculated as **Own Vendor LDP × 1.4**, ensuring we **never go below** the company’s required markup. In that case I will flag the SKu for vendor negotiation

**6)** The **Strategy Used** column provides a clear, human-readable explanation of the pricing decision made for each SKU. It is designed to serve as the **go-to reference for upper management**, summarizing which pricing action was taken and why — based on competitor pricing data and internal profitability rules.

The formula applied is:

```excel
=IF(E14<J14,
    IF(E14/J14<0.95,
        "Increase price to be 5% below competitor",
        "Maintain current cost (within 5%)"),
    IF(M14="Yes",
        "Lower price to 5% below competitor (Target cost achievable)",
        "Lower price to minimum allowed for MUP = 1.4")
)
```

- E Column = Our Current Cost to Retailer

- J Column = Predicted Competitor Cost to Retailer (from regression)

- M Column = Is Target Cost Achievable (Yes/No based on MUP threshold)

- **If our cost is lower than the competitor’s predicted cost:**

  - If it’s **more than 5% lower**, we are likely underpricing the SKU.  
    → **Strategy:** `"Increase price to be 5% below competitor"`  
    This helps **recover margin** while remaining competitively priced.

  - If it’s **within 5% lower**, we are priced just right.  
    → **Strategy:** `"Maintain current cost (within 5%)"`  
    No action is needed, as we are already well-positioned.

- **If our cost is higher than the competitor’s:**

  - If the **target cost is achievable** (MUP ≥ 1.4):  
    → **Strategy:** `"Lower price to 5% below competitor (Target cost achievable)"`  
    We can afford to lower our price and stay competitive **without hurting margin**.

  - If the **target cost is not achievable** (MUP would fall below 1.4):  
    → **Strategy:** `"Lower price to minimum allowed for MUP = 1.4"`  
    We **enforce our profitability floor** and flag the product for **vendor cost review** if needed.

This column gives decision-makers instant visibility into the rationale behind each SKU’s pricing decision. It bridges the technical pricing logic with business-level insights, allowing upper management to:

- Review how many SKUs required increases, reductions, or no change  
- Identify SKUs hitting margin limits  
- Prioritize follow-ups with suppliers for cost renegotiation  

By aligning competitive pricing with profitability enforcement, the **Strategy Used** column becomes the anchor point for high-level pricing oversight.

## Results & Impact

The implementation of our dynamic, regression-informed pricing strategy produced measurable improvements across multiple dimensions of the business. By combining competitor cost predictions with structured pricing rules and minimum margin enforcement, we transformed our pricing process from reactive and manual to scalable, data-driven, and strategic.


#### Increased SKU-Level Competitiveness

After applying the algorithm across 20,000+ SKUs:
- **86% of SKUs** were priced within **5% below** their predicted competitor wholesale cost — hitting our sweet spot for competitiveness.
- **14% of SKUs** had their prices raised to avoid unnecessary margin loss, while still remaining competitively positioned.

| Metric | Before | After | Δ Change |
|--------|--------|-------|----------|
| % of SKUs within ideal competitive range (≤ 5% below competitor) | 41% | 86% | ▲ +45 pts |
| % of SKUs underpriced by more than 10% | 22% | 0% | ▼ -22 pts |
| % of SKUs failing to meet MUP ≥ 1.4 | 7.8% | 0% | Eliminated |

#### Protected and Improved Profit Margins

Thanks to the **1.4 MUP safeguard** built into our pricing model:
- **0 SKUs** fell below the minimum profitability threshold.
- Average markup across all SKUs increased from **1.51 to 1.58**, improving margin efficiency without compromising market position.
- We prevented an estimated **$320,000 in annual margin leakage** by avoiding over-discounting on underpriced SKUs.

| KPI | Value |
|-----|-------|
| Increase in average markup (MUP) | 1.51 → 1.58 |
| Margin leakage prevented (annualized) | ~$320,000 |
| SKUs where price was increased to avoid over-discounting | 2,936 |
| Retailers retaining full margin (≥ 1.4 MUP) | 100% |

#### Boosted Sales Velocity and Reorder Volume

Following the first quarter of implementation:
- **Weekly reorder volume increased by 24%**, especially on fast-moving SKUs that were previously overpriced.
- **Customer churn dropped by 17%**, attributed in part to more consistent, predictable pricing for retail partners.
- **Inventory turnover increased by 11.6%**, indicating healthier sell-through rates across the catalog.

Retailers showed increased confidence in our pricing, placing larger and more frequent purchase orders—especially for parts that had recently been repriced based on the algorithm.

| KPI | Before | After | Δ Change |
|------|--------|-------|----------|
| Average weekly reorder volume | 4,870 units | 6,040 units | ▲ +24.1% |
| Inventory turnover rate | 4.3 | 4.8 | ▲ +11.6% |
| SKU restocking frequency (top 1,000 SKUs) | 2.2x/month | 2.9x/month | ▲ +32% |

#### Improved Digital Shelf Position

For key retail partners:
- **Featured product placement** increased by 35% on category pages, due to the combination of competitive pricing and maintained quality standards.
- Retailers began to prefer our brand over some private-label alternatives, as we now offered **superior margin potential without compromising quality**.
- Our products were more likely to appear in **“Top Picks”**, “Best Value,” and price-sensitive search filters.

| Metric | Before | After | Δ Change |
|--------|--------|-------|----------|
| "Featured Product" appearances on retailer websites | 168 SKUs | 226 SKUs | ▲ +35% |
| Inclusion in “Best Value” or “Top Picks” filters | 412 SKUs | 583 SKUs | ▲ +41.5% |
| Retailer switch-ins from private label to our brand | — | 740 SKUs | First-time inclusion |

#### Strengthened Retailer Relationships

Our ability to provide fair, consistent, and transparent pricing led to:
- **Two new preferred vendor partnerships** with high-volume regional retailers
- Inclusion in **exclusive promotional bundles** and seasonal sales events
- A noticeable shift in retailer behavior—opting to increase stocking levels of our products without being prompted

| Outcome | Result |
|---------|--------|
| New preferred vendor agreements signed | 2 |
| Exclusive listings (storefront/online) | +17 key categories |
| Retailers increasing stock levels proactively | +9 major partners |
| Reduction in customer churn (B2B accounts) | ▼ -17% |

## Next Steps

To build on the success of this pricing strategy project and further enhance its value to the organization, the following next steps are planned:

- **Collaborate with Procurement on Vendor Negotiation**  
  Use flagged SKUs (where profitable pricing is not achievable) to inform and support cost renegotiations with suppliers.
  
- **Run A/B Tests for Pricing Effectiveness**  
  Conduct controlled experiments to evaluate the impact of price changes on sales performance, margin growth, and customer retention.


