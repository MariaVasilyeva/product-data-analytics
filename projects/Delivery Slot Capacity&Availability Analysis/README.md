# Delivery Slot Capacity and Availability Analysis

## Context

This project was completed as an analytics case for the analytics and planning team within the operations function of an e-commerce company.

When placing an order, a customer selects an available courier delivery slot. Each slot is defined by a delivery zone and a time interval and has limited capacity — the maximum number of orders that can be accepted. As customers place orders, the remaining capacity decreases. While the slot is still open for booking, an operations manager may also adjust its capacity manually.

Insufficient capacity reduces customer choice and may lead to lost orders. Excess capacity does not directly harm the customer experience, but it may indicate inefficient planning and resource allocation.

## Objective

1. Analyze the delivery-slot fill history.
2. Define metrics that characterize slot availability for customers.
3. Identify slots at risk of capacity shortage or excess allocation.
4. Prepare detailed reports that operations managers can use to adjust slot settings.

## Data

The analysis covers the period from September 1 to October 31, 2024.

Two datasets were used:

- `intervals.csv` — one row per slot and delivery date, containing the delivery zone, service level, booking cutoff, delivery interval, initial and final recorded capacity, and capacity-change history;
- `orders.csv` — one row per slot and order hour, containing hourly and cumulative capacity consumption.

The relationship between the tables is many-to-one: one slot may have multiple hourly order records.

## Solution

### 1. Data preparation and validation

- Converted date and time fields to `datetime`.
- Validated table granularity and the uniqueness of the composite key `slot_id + interval_start_date`.
- Joined slot-level capacity data with hourly order records.
- Retained slots without orders as zero-consumption slots.
- Identified 38 order rows that did not match a slot by the composite key and exported them separately for data-quality review.

### 2. Analytical data mart

Hourly order records were aggregated to one row per delivery slot. The resulting slot-level dataset included:

- final recorded capacity;
- actual consumed capacity, defined as the maximum cumulative number of orders;
- remaining capacity;
- remaining-capacity rate;
- first and last order timestamps;
- capacity change and manual expansion flag;
- delivery-zone, service-level, and time-interval attributes.

Core metrics:

```text
free_capacity = available_capacity_last - consumed_capacity

availability_rate = free_capacity / available_capacity_last
```

`availability_rate` represents the share of capacity that remained unused by the end of the observed period.

### 3. Availability analysis

Capacity and utilization were analyzed:

- by day and week;
- by delivery zone;
- by service level;
- at the individual-slot level.

Comparing order consumption with declared capacity made it possible to distinguish demand changes from capacity-planning changes.

### 4. Problem-slot identification

Two heuristic thresholds were introduced for operational monitoring. To reduce noise from very small slots, only slots with a final capacity of at least 10 orders were included.

**Capacity-shortage risk:**

```text
availability_rate <= 0.25
```

These slots had no more than 25% of their capacity remaining. Slots with zero or negative free capacity require particular attention because consumption reached or exceeded the declared capacity.

**Excess capacity:**

```text
availability_rate >= 0.80
```

These slots retained at least 80% of their capacity.

### 5. Operations reports

Detailed reporting tables were prepared for:

- capacity-constrained slots, sorted by date, zone, and availability level;
- counts of constrained slots by delivery zone;
- excess-capacity slots;
- slots with allocated capacity but no orders;
- counts of excess-capacity slots by delivery zone.

These reports allow operations managers to identify recurring issues at the zone, service, and delivery-interval levels and support capacity reallocation decisions.

## Key findings

- At the aggregate level, the delivery system did not appear overloaded: a substantial share of declared capacity remained available, suggesting that customers generally retained a choice of delivery slots.
- Availability was unevenly distributed. During most of September, the weekly share of remaining capacity was approximately 69–74%; during October, it declined to roughly 58–67%.
- Hourly order demand was relatively stable, while declared capacity fluctuated considerably more. This suggests that changes in availability were driven primarily by capacity planning rather than sharp demand shifts.
- The number of slots with a low remaining-capacity buffer increased from late September and reached its highest levels in October. These slots were most common in the `plus`, `economy`, and `plus15` services.
- A total of 13,095 slots were classified as having excess capacity. Of these, 11,113 received no orders, indicating potential over-allocation of resources.
- Excess capacity was most common in the `same_day60`, `same_day`, `plus`, and `economy` services.

The main operational recommendation is to monitor recurring shortages and excess allocation by zone, service, and delivery interval. Capacity can be reduced where it remains systematically unused and reallocated to consistently high-demand areas. A forecast-based safety buffer should be retained to avoid reducing customer availability.

## Limitations

- The 25% and 80% thresholds are analytical heuristics and should be calibrated against business requirements and the acceptable operational risk level.
- The analysis uses final recorded slot capacity and maximum cumulative consumption. The data does not directly show which slots were actually displayed to customers in the booking interface.
- Additional data on unavailable-slot selection attempts, checkout abandonment, and cancellations would be required to estimate lost demand.

## Tech stack

`Python`, `pandas`, `NumPy`, `Matplotlib`, `Seaborn`, `Jupyter Notebook`
