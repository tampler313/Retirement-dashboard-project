```md
# Retirement Dashboard — MVP Scope

## 1. Goal of MVP
Build a first usable version of the retirement withdrawal dashboard that can:
- take in a user’s retirement setup
- simulate spending ranges
- output three annual spending bands
- update those bands based on yearly realized withdrawals and portfolio changes

The MVP is a **decision-support tool**, not a full advisor platform.

## 2. Supported asset types (v1)
### Included
- Broad-market equity ETFs / equity allocation bucket
- Bond / short-duration bond allocation bucket
- Cash bucket
- Fixed retirement income streams (pension / annuity / monthly retirement pay)

### Excluded or heavily simplified
- concentrated single-stock portfolios
- real estate valuation as a core return engine
- private business valuation
- options / derivatives
- commodities as custom user-managed sleeves

## 3. Supported user inputs
### Initial inputs
- current age
- planning horizon / ending age assumption
- total investable retirement assets
- asset allocation (equity / bond / cash)
- annual fixed retirement income
- baseline necessary annual spending
- baseline flexible annual spending

### Annual update inputs
- actual withdrawal last year
- major one-off spending (optional)
- updated total investable assets
- updated fixed retirement income (optional)
- revised spending assumptions (optional)

## 4. MVP modules
### Module 1 — User setup
Purpose:
- create a retirement case profile

Outputs:
- validated retirement profile data

### Module 2 — Market / assumption data layer
Purpose:
- load return assumptions and/or historical data inputs for equity, bonds, cash, inflation

Outputs:
- standardized simulation inputs

### Module 3 — Core withdrawal engine
Purpose:
- simulate future paths and derive three annual spending bands

Outputs:
- Conservative / Normal / Aspirational spending amounts
- corresponding success-rate labels
- implied withdrawal rates

### Module 4 — Annual update engine
Purpose:
- update a case after a year of realized spending and market movement

Outputs:
- refreshed spending bands for the next year
- change summary versus prior year

### Module 5 — Advanced analysis view
Purpose:
- provide optional deeper analysis for advanced users

Outputs:
- failure probability
- likely danger-zone starting window
- worst-case spending compression level
- marginal impact of higher / lower spending

## 5. Core outputs (default view)
The default view should show:
- Conservative band
- Normal band
- Aspirational band
- annual amount for each band
- success-rate label for each band
- short explanation of how this year compares with last year

## 6. Core outputs (advanced view)
The advanced view may show:
- failure probability distribution
- likely age or interval where risk rises meaningfully
- stress-case summary
- impact of spending above or below baseline
- sensitivity to major assumptions

## 7. Frequency of updates
MVP should assume **annual updates**.
It is not necessary to support monthly or daily re-optimization.

## 8. MVP constraints
- do not attempt to handle every real-world retirement asset structure
- do not attempt to replace legal / tax / trust planning
- do not optimize for highly customized edge cases in v1
- prefer robust simple assumptions over brittle complexity

## 9. Non-goals for MVP
- advisor billing workflows
- household multi-user collaboration
- portfolio trading or rebalancing execution
- tax-lot optimization
- recommendation engine for specific securities

## 10. What “good enough” means for MVP
The MVP is good enough if a user can:
1. input a clean retirement case
2. receive three useful annual spending ranges
3. record what they actually spent this year
4. see how that changes next year’s decision bands
5. inspect deeper risk only if they want to
```
