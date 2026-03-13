```md
# Retirement Dashboard — Product Definition

## 1. Product summary
Retirement Dashboard is a retirement withdrawal decision system.

It does **not** aim to provide a single universal “safe withdrawal rate.” Instead, it helps a user update their retirement spending decision each year based on:
- current investable assets
- fixed retirement income (pension / annuity / monthly retirement pay)
- portfolio structure
- actual withdrawals from the previous year
- market changes
- remaining lifetime horizon

The core output is a set of **three annual spending bands** that users can use as a practical decision interface:
- Conservative
- Normal
- Aspirational

## 2. Core problem
Most retirement frameworks stop at one of two places:
1. they provide a static withdrawal rule (for example, a fixed initial withdrawal rate)
2. they provide investment products or allocation advice

What is often missing is an **ongoing operating system for decumulation**:
- How much can I spend next year?
- If I spend more this year, what does that do to my future safety margin?
- If markets fall, how should next year’s spending change?
- If I underspend, how much flexibility do I gain?

This product is meant to fill that gap.

## 3. Target user (v1)
The first version targets users who:
- are retired or near retirement
- have some investable retirement assets
- have at least one source of fixed retirement cash flow (pension / retirement pay / annuity)
- primarily hold diversified assets such as broad-market ETFs, funds, bonds, or cash
- want a yearly spending decision tool, not a static rule of thumb

## 4. Out of scope (v1)
The first version does **not** aim to solve:
- concentrated single-stock retirement portfolios as the primary case
- tax optimization
- trust / inheritance / family office planning
- real estate valuation systems
- direct buy / sell investment recommendations
- advisor CRM workflow

## 5. Core user value
The dashboard should let a user quickly understand:
- what they can likely spend next year
- how much safety margin they have
- what changes if they choose to spend more or less this year
- how current market conditions affect next year’s sustainable range

The product should feel like a **retirement cockpit**, not a one-time calculator.

## 6. Product philosophy
### 6.1 Spending bands, not one magic number
The product should not output only one withdrawal rate. It should output three practical spending bands:
- Conservative: high safety preference
- Normal: baseline lifestyle
- Aspirational: higher spending with lower safety margin

### 6.2 Amount-first presentation
For most users, actual annual spending amounts are more intuitive than abstract percentages.
Withdrawal rates may be shown, but should be secondary.

### 6.3 Safety is dynamic
The product assumes safety is path-dependent and must be re-evaluated periodically.
A user should be able to update the model each year with:
- actual prior-year spending
- updated asset values
- updated fixed-income streams
- changed life assumptions

### 6.4 Simplicity in default mode
The default interface should be simple and usable by non-technical users.
Deep risk analysis should be available, but hidden behind advanced views.

## 7. Primary workflows
### 7.1 Initial setup
User inputs:
- current age
- planning horizon / expected longevity assumption
- asset allocation
- current investable assets
- fixed retirement income
- baseline spending needs
- flexible lifestyle spending

System outputs:
- Conservative / Normal / Aspirational annual spending bands
- corresponding success ranges

### 7.2 Annual update
Each year, the user updates:
- actual total withdrawal
- special one-off spending
- updated portfolio value
- changes to pension / fixed income if any

System recalculates:
- next year’s spending bands
- changes in safety margin
- whether the user moved toward safer or riskier territory

## 8. Success criteria for v1
The first version is successful if it can:
1. support a clean initial setup
2. generate three usable spending bands
3. accept annual updates
4. show how prior-year decisions affect next-year spending capacity
5. clearly separate default mode and advanced analysis mode

## 9. Design principles
- clear over clever
- actionable over academically complete
- decision support over static explanation
- modular architecture so the engine can improve without breaking the product
```
