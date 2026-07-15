# Marketing Channel Performance & Attribution

## Context

An online store acquires users through paid search, paid social, email, referrals, and organic traffic.

Marketing planning requires more than measuring traffic volume. Audience reach and paid orders need to be compared with marketing spend and attributed revenue for each acquisition channel.

## Objective

1. Evaluate the reach and performance of marketing channels.
2. Calculate paid orders, paying users, revenue, and average order value.
3. Combine channel results with marketing spend.
4. Calculate CAC and ROMI.
5. Identify the strongest and weakest channels.
6. Provide recommendations for the marketing team.
7. Assess the limitations of the selected attribution approach.

## Data

Three datasets were used:

- `sessions.csv` — user sessions, including user, session date, marketing channel, and device;
- `orders.csv` — orders, including user, order date, status, and revenue;
- `marketing_spend.csv` — marketing spend by date, channel, campaign, and ad group.

Source-table size:

- 160,000 sessions;
- 85,000 unique users;
- 120,000 orders;
- 100,000 marketing-spend records.

After filtering, the analysis included 56,345 paid orders from 40,659 paying users.

## Solution

### 1. Data exploration and preparation

- Inspected table structure, data types, and missing values.
- Converted session, order, and spend dates to `datetime`.
- Retained only orders with `status = paid` for order and revenue analysis.
- Examined the number of orders per user and the number of channels each user interacted with.
- Confirmed that some users placed repeat orders and interacted with multiple channels.

### 2. Attribution rule

First-touch attribution was used consistently for all user-level metrics: each user was assigned to the channel of the earliest recorded session. User counts, paying users, orders, and revenue were calculated within aligned first-touch cohorts.

This rule was selected by the team for the project. I apply it to meet the stated requirement, but I do not consider it sufficiently reliable as the sole basis for budget decisions because it ignores the contribution of all subsequent touchpoints.

### 3. Channel metrics

The following metrics were calculated for each channel:

- **First-touch users** — users whose earliest recorded session belongs to the channel;
- **Paid users** — paying users attributed to their first-touch channel;
- **Paid orders** — number of successfully paid orders;
- **Conversion to paying user** — paying users as a share of the first-touch cohort;
- **Revenue** — total attributed revenue from paid orders;
- **AOV** — average order value;
- **CAC** — customer acquisition cost per paying user;
- **ROMI** — return on marketing investment.

Formulas:

```text
Conversion = paying users / first-touch users

AOV = revenue / paid orders

CAC = marketing spend / paying users

ROMI = (revenue − marketing spend) / marketing spend
```

CAC and ROMI were not calculated for organic traffic because direct marketing spend was zero.

### 4. Combining channel results with spend

Marketing spend was aggregated to the channel level and joined with audience, order, and revenue metrics.

The results were presented in comparative tables and charts covering:

- reach;
- paid orders;
- paying-user rate;
- revenue;
- average order value;
- CAC;
- ROMI.

## Key findings

### Referral

Referral was the strongest paid channel:

- ROMI — **1.85**;
- CAC — **1,347.42**;
- average order value — **2,600.85**, the highest across all channels;
- revenue — **20,260,630**.

The channel is a candidate for scaling, provided that efficiency remains stable as acquisition volume increases.

### Paid social

Paid social delivered the weakest result:

- the highest CAC — **2,337.12**;
- negative ROMI — **−0.42**;
- spend of **13,842,758.62**, exceeding attributed revenue.

The recommended next step is a campaign- and ad-group-level review, followed by removal or redesign of inefficient audience and creative segments.

### Paid search

Paid search delivered the largest scale:

- 15,288 paying users;
- 21,463 paid orders;
- the highest revenue — **35,121,030**;
- ROMI — **0.22**.

The channel was profitable, but its return was limited relative to spend. Campaign and keyword optimization should focus on improving ROMI without materially reducing acquisition volume.

### Email

Email was a comparatively low-cost and profitable channel:

- the lowest CAC among paid channels — **836.85**;
- ROMI — **0.65**;
- revenue — **6,306,120**.

The channel may benefit from broader reach, improved segmentation, and additional communication experiments.

### Organic

Organic traffic generated 9,605 paying users and **18,053,060** in attributed revenue with no direct marketing spend recorded.

Zero spend does not necessarily mean the channel is cost-free, because the dataset does not include SEO, content, brand, or product-support costs.

## Recommendations

1. Test controlled scaling of the referral program.
2. Decompose paid-social performance to the campaign and `ad_group` levels.
3. Optimize paid search with a focus on improving ROMI while protecting volume.
4. Develop email as a low-cost retention and repeat-communication channel.
5. Compare first-touch results with last-touch and multi-touch attribution.
6. Extend future analysis to contribution margin, repeat purchases, and channel-level LTV.

## Limitations

- First-touch attribution was selected by the team. In my assessment, it should not be used as the sole basis for budget allocation because it may over-credit discovery channels and under-credit touchpoints closer to purchase.
- Order attribution uses the earliest recorded session without an explicit check that the session occurred before the specific order date.
- Channel-level CAC and ROMI may hide substantial variation across campaigns and ad groups.
- ROMI is based on revenue rather than profit or contribution margin.
- Direct spend is unavailable for organic traffic, limiting comparison with paid channels.

## Tech stack

`Python`, `pandas`, `NumPy`, `Matplotlib`, `Seaborn`, `Jupyter Notebook`
