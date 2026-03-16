# Retirement Dashboard — Engine Specification (v1 Draft)

## 1. Purpose

This document defines the scope, semantics, and input/output contract of the core retirement withdrawal engine used by the Retirement Dashboard.

The engine is not a generic financial calculator and not a full retirement advisor system.
Its purpose is to solve a recurring yearly decision problem:

> Given a user’s current retirement state, estimate sustainable annual withdrawal bands for the remaining retirement horizon under a defined simulation rule.

This engine is part of a larger system.
It does not own UI workflows, raw user input handling, or portfolio bookkeeping.
It only consumes normalized inputs and returns simulation-driven decision outputs.

---

## 2. Scope of the engine in v1

In v1, the engine supports:

- annual re-evaluation of retirement withdrawal capacity
- three spending bands:
  - Conservative
  - Normal
  - Aspirational
- rolling-horizon solving
- broad investable asset buckets:
  - equity
  - bond
  - cash
- fixed retirement income as a parallel input for dashboard display
- scenario-based simulation using standardized scenario inputs

In v1, the engine assumes:

- annual time steps
- one solve per year
- one standardized withdrawal rule
- fixed planning end age once a case is initialized
- a fixed inflation assumption for scheduled withdrawal growth
- annual rebalancing to target weights within simulation

---

## 3. Core mathematical objective

At decision year `t`, given the user’s current normalized retirement state, the engine solves for an annual withdrawal amount `W_t` such that the portfolio can sustain a scheduled withdrawal path through the remaining retirement horizon with at least a target success probability.

In v1, the engine solves this problem three times using three success thresholds:

- Conservative band: target success probability >= 99%
- Normal band: target success probability >= 95%
- Aspirational band: target success probability >= 90%

The result is a set of three annual withdrawal bands for the current year.

These bands are conditional on the current state and the v1 simulation rule.
They are not permanent guarantees and are re-solved each year after the case state is updated.

---

## 4. Planning horizon rule

### 4.1 Initial planning end age

At initial case setup, the system determines a `planning_end_age` using:

- the user’s starting age (or retirement-start age, depending on the case setup mode)
- a life-expectancy lookup table
- an additional longevity buffer of 10 years

Conceptually:

`planning_end_age = start_age + expected_remaining_life_years + 10`

The exact lookup source belongs to assumptions / data configuration, not to the engine itself.

### 4.2 Remaining horizon

At each annual solve, the engine uses:

`remaining_horizon = planning_end_age - current_age`

The engine only evaluates the remaining horizon.
It does not independently re-derive a new planning end age on every solve in v1.

---

## 5. Candidate withdrawal path definition

The engine solves for a current-year annual withdrawal amount, not primarily for a percentage.

For a candidate current-year withdrawal amount `W_t`, the engine constructs a scheduled withdrawal path over the remaining horizon:

- year 1 withdrawal = `W_t`
- each subsequent year grows by the inflation assumption used by the engine

Conceptually:

- withdrawal in year 1 = `W_t`
- withdrawal in year k = `W_t * (1 + inflation_assumption)^(k-1)`

In v1:

- the search variable is annual withdrawal amount
- implied withdrawal rate is a derived output
- the inflation assumption is treated as a fixed model parameter
- the scheduled withdrawal path is evaluated against each simulation path

---

## 6. Definition of success and failure

### 6.1 Failure rule in v1

V1 uses a scheduled withdrawal failure definition.

A simulation path is considered a **failure** if, before the planning horizon ends, the investable portfolio can no longer fund the required scheduled withdrawal for that year.

This means failure occurs when:

- the required annual withdrawal amount for a year exceeds the amount the portfolio can support under the simulation rule

This failure rule is based on whether the investable portfolio can continue funding the scheduled withdrawal path.

In v1, failure is not defined by whether the user can maintain some separately defined lifestyle spending target.
It is also more specific than a simple “portfolio reaches zero” definition.

### 6.2 Success rule

A simulation path is considered a **success** if the investable portfolio can support the full scheduled withdrawal path through the full remaining horizon.

### 6.3 Success probability

For a candidate withdrawal amount, success probability is:

`successful_paths / total_paths`

A candidate amount satisfies a band if its success probability is at least the required threshold for that band.

---

## 7. Band semantics

The engine outputs three annual withdrawal bands:

### 7.1 Conservative
- target success probability >= 99%
- intended for high safety preference

### 7.2 Normal
- target success probability >= 95%
- intended as a baseline lifestyle band

### 7.3 Aspirational
- target success probability >= 90%
- intended for higher spending with lower safety margin

These are annual decision bands, not static lifetime labels.
They are conditional on:

- the current case state
- the remaining horizon
- the scenario set used
- the v1 withdrawal and simulation rules

---

## 8. Future-year simulation convention

The engine does not manage real-world portfolio bookkeeping or user transaction history.
Those concerns belong to the annual update / orchestration layer.

The engine only operates after it has received the current year’s normalized engine state.

For simulation purposes, once a normalized engine state is received, the engine evaluates each future year using a fixed annual convention.

In v1, the yearly simulation sequence is:

1. Begin with the portfolio state at the start of the simulated year
2. Rebalance to target asset weights
3. Withdraw the scheduled amount for that year
4. Apply that year’s simulated asset returns to the remaining invested assets
5. Carry the resulting portfolio forward to the next simulated year

This convention applies only inside the simulation engine.
It does not describe how real-world user portfolios are updated or reconstructed.
Those workflow concerns are handled outside the engine.

---

## 9. Engine inputs

The engine accepts normalized inputs only.
It does not directly consume raw UI form data or fetch database records on its own.

### 9.1 Required normalized inputs

A normalized engine input should include at least:

- `current_age`
- `planning_end_age`
- `remaining_horizon`
- `current_portfolio_value`
- `bucket_amounts` for:
  - equity
  - bond
  - cash
- `target_weights` for:
  - equity
  - bond
  - cash
- `annual_fixed_income`
- `inflation_assumption`
- `scenario_set_reference`
- `withdrawal_rule_version`

### 9.2 Scenario input expectation

The engine expects scenario data to be pre-standardized.

At minimum, each scenario path must provide annual values for:

- equity return
- bond return
- cash return

In v1, inflation may be treated as a fixed model parameter instead of a stochastic path input.

### 9.3 Input ownership

The normalized input object is prepared outside the engine by the orchestration / application service layer.

---

## 10. Engine outputs

The engine returns model outputs for the current decision year.

### 10.1 Default outputs

The default output should include:

- `conservative_amount`
- `normal_amount`
- `aspirational_amount`
- implied withdrawal rates for each band

### 10.2 Dashboard-friendly derived outputs

The engine may also return:

- estimated spendable income for each band after adding annual fixed income
- short summary values needed by the default dashboard

### 10.3 Advanced outputs

The engine may additionally return optional advanced metrics such as:

- estimated failure probability
- likely danger-zone starting window
- worst-case spending compression level
- marginal impact of spending above or below baseline

In v1, advanced outputs are secondary and may be partial.

---

## 11. Relationship to annual update flow

The engine solves only from the current year’s normalized engine state forward.
It does not infer or reconstruct that state by itself.

The engine does not manage the raw annual update workflow.

The annual update workflow is responsible for:

- collecting user-provided updated values
- validating those values
- converting them into a new normalized engine state
- calling the engine again for the next solve

The engine’s role begins only after the updated annual state has already been normalized.

This means the engine does not itself:

- reconstruct real-world transaction history
- infer raw holdings from user-entered security-level data
- fetch historical prices to rebuild trade-level account evolution

Those concerns belong to the orchestration / annual-update layer in v1.

---

## 12. Relationship to market scenarios

The engine is scenario-consumer logic, not scenario-generation logic.

It assumes scenario inputs have already been prepared by the market / assumption data layer.

This allows the engine to work with:

- small historical rolling fixtures during development
- later Monte Carlo scenario sets
- future alternative scenario engines

As long as the scenario input format remains consistent.

The engine should be able to solve for any valid scenario set whose horizon is at least the required remaining horizon.

---

## 13. Fixed income treatment in v1

Fixed retirement income is retained in the normalized input and may be used for dashboard-facing spendable-income summaries.

However, in v1, the core failure rule is based on the investable portfolio’s ability to support the scheduled withdrawal path.

This means:

- fixed income is available as a parallel annual amount
- dashboard output may show:
  - portfolio withdrawal band
  - fixed income
  - combined estimated annual spendable income
- but fixed income does not redefine the core scheduled withdrawal failure rule in v1

This simplification keeps the first engine version aligned with the initial withdrawal-path modeling approach.

This is an intentional v1 simplification, not a statement that fixed income is unimportant to retirement planning.

---

## 14. Explicit non-goals for v1

The engine does not attempt to support the following in v1:

- non-US multi-pack portfolio solving
- free-mix multi-country portfolio decomposition
- concentrated single-stock retirement portfolios
- security-level look-through ETF decomposition
- tax optimization
- transaction-cost optimization
- tax-lot-aware sell rules
- dynamic sell-order recommendation
- multiple withdrawals within a year
- monthly or daily re-optimization
- stochastic inflation modeling
- regime-switching return models
- valuation-aware expected return adjustments
- utility-maximizing optimal control
- guaranteed-income indexation modeling
- legal / trust / inheritance planning logic

---

## 15. Open questions / deferred extensions

The following are intentionally deferred beyond the first engine version:

- whether inflation should later become stochastic
- whether fixed income should be integrated into a spending-gap failure model
- whether to support alternative yearly simulation order conventions
- whether to support additional market packs beyond US pack
- whether to support scenario-generation plugins beyond historical fixtures and Monte Carlo
- whether to support richer danger-zone and path-shape diagnostics

These are valid extension directions, but are not required for v1 engine implementation.