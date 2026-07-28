# Customer Segmentation & Targeted Marketing Strategy
### RFM-Based Clustering Analysis on E-Commerce Transaction Data

---

## Executive Summary

**Business Problem:** The company lacks a systematic way to differentiate customers by value and engagement, resulting in one-size-fits-all marketing that wastes budget on low-value customers while under-investing in high-value customers at risk of churning.

**Solution:** After filtering the dataset to 74,628 delivered transactions, RFM analysis and K-means clustering were applied to 38,772 unique customers with at least one delivered order. The customers were segmented into four actionable groups, each paired with a tailored marketing strategy.

**Key Finding:** 36.6% of customers (15,812 people) fall into a **"High-Value At-Risk"** segment — customers with historical spending and purchase frequency on par with the most active customers, but who have gone quiet recently. This segment alone represents an estimated **$39M+ in historical revenue** and is the single highest-priority group for win-back campaigns.

**Next Steps:** Deploy a tiered win-back campaign targeting the At-Risk segment, A/B test incentive structures, and integrate segment labels into the CRM for ongoing use by the marketing team.

---

## 1. Business Problem

E-commerce companies often treat all customers the same in marketing outreach, despite customers varying enormously in purchase frequency, spending, and recency of engagement. This creates two costly failure modes:

- **Overspending on low-value customers** who are unlikely to generate meaningful return on marketing investment
- **Underinvesting in high-value customers** who have proven spending power but are drifting toward churn — the costliest customers to lose and the most expensive to reacquire

The goal of this project was to answer: **Which customers should marketing prioritize, and with what strategy?**

---

## 2. Methodology

**Data:** 100,000 synthetic Amazon-style e-commerce transactions (20 columns), covering order details, customer information, product data, pricing, and payment details.

**Process:**

1. **Data Cleaning & Validation**
   - Checked for missing values, duplicate orders, and invalid dates
   - Validated pricing logic by reconstructing `TotalAmount` from unit price, quantity, discount, tax, and shipping — identified and corrected a discount-formula assumption error (discount is a percentage, not a flat amount) that initially flagged 59.6% of records as inconsistent; corrected formula reduced this to near-zero
   - Filtered to `Delivered` orders only (74,628 of 100,000, 74.6%) to avoid inflating customer value with cancelled/returned transactions

2. **RFM Feature Engineering**
   - Recency: days since each customer's most recent delivered order
   - Frequency: number of distinct orders per customer
   - Monetary: total amount spent per customer
   - Applied log transformation to correct right-skew, then standardized all three features (mean=0, std=1) to prevent monetary value from dominating the clustering distance metric

3. **Cluster Selection**
   - Evaluated k=2 through k=10 using the Elbow Method and Silhouette Score
   - Silhouette Score favored k=2–3 (0.36–0.37), but inspection of cluster profiles showed k=3 merged two customers groups with a ~4.8x difference in average spend ($1,262 vs. $264) into a single segment
   - Selected **k=4** (Silhouette Score 0.3265) to preserve this business-relevant distinction, accepting a modest reduction in statistical separation in exchange for actionable granularity

4. **Segment Validation Against Geography & Category**
   - Tested whether cluster membership correlated with customer location or preferred product category
   - Chi-square test of independence confirmed a statistically significant association (χ² = 891.88, p < 0.001), but Cramér's V = 0.0876 indicated the effect size was negligible in practice
   - Conclusion: category/geography are not meaningful segmentation levers; purchase behavior (RFM) is the primary driver worth acting on

---

## 3. Skills Demonstrated

| Category | Tools & Techniques |
|---|---|
| **Languages** | Python |
| **Data Manipulation** | pandas, numpy |
| **Machine Learning** | scikit-learn (K-means, StandardScaler, Silhouette Score) |
| **Statistical Testing** | scipy (Chi-square test of independence, Cramér's V effect size) |
| **Visualization** | matplotlib, seaborn (3D scatter, bar charts, elbow/silhouette plots) |
| **Environment** | Jupyter Notebook |
| **Analytical Practices** | Data validation via reverse-calculation, log transformation & feature scaling, statistical vs. practical significance interpretation, business-driven trade-off between model metrics and interpretability |

---

## 4. Results & Business Recommendations

| Segment | Size | Avg. Recency | Avg. Frequency | Avg. Monetary | Recommended Strategy |
|---|---|---|---|---|---|
| **High-Value Active** | 5,889 (15.2%) | 85 days | 2.65 orders | $2,455 | VIP perks, early access to new products, loyalty program enrollment |
| **High-Value At-Risk** | 15,812 (40.8%) | 656 days | 2.59 orders | $2,466 | **Top priority.** Win-back campaign with time-limited incentives; proven spending history makes this the highest-ROI re-engagement target |
| **Dormant Mid-Value** | 10,433 (26.9%) | 995 days | 1.03 orders | $1,262 | Re-purchase incentive to convert one-time buyers into repeat customers |
| **Low-Value Dormant** | 6,638 (17.1%) | 859 days | 1.11 orders | $264 | Low-cost automated email nurture only; not a priority for active investment |

**Key insight:** Category and geographic preference were tested as potential segmentation refinements but showed negligible statistical association with cluster membership (Cramér's V = 0.09) — segment strategy should stay focused on engagement recency and spending behavior rather than product or location targeting.

---

## 5. Next Steps

1. **Launch a pilot win-back campaign** targeting the High-Value At-Risk segment (15,812 customers), A/B testing 2–3 incentive structures (e.g., percentage discount vs. free shipping vs. loyalty points)
2. **Build a churn-prediction model** to flag High-Value Active customers before they slip into At-Risk status, enabling proactive rather than reactive retention
3. **Operationalize the segmentation** by pushing cluster labels into the CRM/marketing automation platform for ongoing campaign targeting
4. **Re-run segmentation quarterly** to track segment migration over time and measure whether win-back campaigns are successfully moving customers from At-Risk back to Active
5. **Extend the analysis** with Customer Lifetime Value (CLV) modeling to further prioritize retention spend by projected future value, not just historical spend
