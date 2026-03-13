```md
# AGENTS.md

## Project purpose
This repository builds a retirement withdrawal decision dashboard.

The product is **not** a generic financial calculator and **not** a robo-advisor.
Its purpose is to help convert retirement assets, fixed income streams, and annual withdrawal decisions into next-year spending guidance.

## Product concept
The core UX is built around **three spending bands**:
- Conservative
- Normal
- Aspirational

The system should help users understand:
- how much they can likely spend next year
- how current market conditions affect that range
- how spending more or less this year changes future flexibility

## MVP scope
The first version supports:
- diversified stock / bond / cash retirement portfolios
- fixed retirement income streams
- annual update workflow
- three spending bands
- optional advanced analysis view

The first version does NOT support:
- concentrated single-stock portfolios as the primary planning case
- trust / inheritance / tax planning
- real estate valuation systems
- advisor CRM workflows
- direct investment recommendations

## Architecture
- `apps/web` — frontend dashboard
- `apps/api` — API / orchestration layer
- `packages/engine` — core withdrawal simulation and decision engine
- `packages/shared` — shared schemas and types
- `data/` — market datasets and processed data
- `docs/` — product and modeling documents

## Engineering rules
1. Keep all financial / simulation logic inside `packages/engine`
2. Do not place financial logic directly in frontend components
3. Keep modules small, typed, and testable
4. Add tests whenever engine behavior changes
5. Keep model inputs and outputs explicit
6. Prefer simple, robust assumptions over brittle complexity
7. Do not invent unsupported financial logic without documenting the assumption clearly

## UX rules
1. Default UI must stay simple
2. Show the three spending bands first
3. Keep advanced risk analysis behind advanced pages
4. Prioritize readability over flashy UI
5. Amounts matter more than abstract percentages in the default experience

## Data rules
1. Prefer cached / stored market data over repeated live API calls
2. Separate market data from user case data
3. Version important model assumptions where possible
4. Avoid coupling user results to undocumented external data behavior

## Collaboration rules for coding agents
When implementing, prefer this order:
1. make the architecture clean
2. make data flow explicit
3. make engine interfaces stable
4. make UI simple but usable

Do not overbuild unsupported edge cases into MVP.
If a requested change expands scope, flag it clearly instead of silently implementing a broader system.

## Definition of done for MVP tasks
A task is done when:
- code is placed in the correct architectural layer
- tests pass where relevant
- assumptions are documented if financial logic is introduced
- output matches the product concept of yearly retirement decision support
```
