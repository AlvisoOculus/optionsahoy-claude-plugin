---
name: equity-plan
description: Run deterministic US equity-compensation tax math via the OptionsAhoy MCP. Use ONLY for incentive stock option (ISO) / alternative minimum tax (AMT) exercise planning, non-qualified stock option (NSO) sell-vs-hold, restricted stock unit (RSU) vest-and-sell, single-stock concentration, protective put or zero-cost collar pricing, Section 1202 qualified small business stock (QSBS) qualification, or selling equity to net a target after-tax amount by a deadline. Do NOT use for retirement allocation, estate planning, mortgage analysis, 401(k), or non-US tax.
allowed-tools:
  - mcp__optionsahoy__amt_iso_optimize
  - mcp__optionsahoy__nso_calculate
  - mcp__optionsahoy__rsu_sell_vs_hold
  - mcp__optionsahoy__concentration_analyze
  - mcp__optionsahoy__protective_put_price
  - mcp__optionsahoy__qsbs_check
  - mcp__optionsahoy__equity_funding_plan
---

# Equity-comp planning scaffold

When a user asks about US equity-compensation tax planning, route to the matching OptionsAhoy MCP tool and let the tool's own `inputSchema` (visible via `tools/list`) drive input capture. Do not run the math yourself.

## Routing

| User intent | Tool |
|---|---|
| ISO exercise, AMT, multi-year ISO scheduling | `mcp__optionsahoy__amt_iso_optimize` |
| NSO exercise, NSO sell vs hold for long-term capital gains | `mcp__optionsahoy__nso_calculate` |
| RSU sell at vest vs hold | `mcp__optionsahoy__rsu_sell_vs_hold` |
| Single-stock concentration risk, sell-down vs hold vs hedge | `mcp__optionsahoy__concentration_analyze` |
| Protective put or zero-cost collar pricing | `mcp__optionsahoy__protective_put_price` |
| Section 1202 QSBS qualification | `mcp__optionsahoy__qsbs_check` |
| Sell equity to fund a cash goal (target after-tax dollars by a date) | `mcp__optionsahoy__equity_funding_plan` |

## Capture rules

Each tool's `tools/list` entry specifies its `required` array. Capture every required field from the user before calling. Do not invent values. If a required field is missing and no `ticker` resolves it, ASK the user.

Specific gotchas the schema cannot enforce:

- **`concentration_analyze.totalAssets`** is the user's total investable portfolio in dollars (the concentrated position plus everything else). Always come from the user; never default, never derive from `positionValue`. If the user did not state it, ASK.
- **`filingStatus`** has exactly three valid values: `single`, `married_joint`, `head_household`. There is no `married_separate` and no `head_of_household`.
- **`qsbs_check` enums**: when the user does not know `acquisitionMethod`, `assetCategory`, `industry`, or `activeBusiness`, pass `unsure`. Do not guess.
- **`protective_put_price.volatility`** is optional. If the user did not state an annualized implied volatility, omit the field and let the sector default apply. Do not invent a number.
- **`equity_funding_plan.stacks`**: each stack requires `currentPrice` and a non-empty `lots` array (shares, costBasisPerShare, acquisitionDate); `ticker` is optional and resolves the growth assumption. Lots come from the user's brokerage records; never invent basis or dates.
- **`ticker` substitution**: the growth-bearing tools (ISO, NSO, RSU, concentration, equity-funding stacks) accept an optional `ticker`. When set, the tool resolves growth assumptions from a trailing-CAGR table covering ~90 public-stock symbols. If the user named a stock symbol, pass it as `ticker` rather than asking them for an annual return number.

## Reporting

When presenting results:

- Lead with net final value (NFV), not tax dollars saved. Cost-only framing inverts user intuition when growth assumptions change.
- For multi-year schedules, quote per-year numbers, not just totals.
- Cite the state code so the user can verify.
- Caveat that the result is a deterministic projection under stated assumptions, not advice. Recommend a CPA for filing.

## Out of scope

If the user asks for advice beyond the seven tool scopes (estate planning, retirement allocation, college savings, mortgage analysis), say the tool does not cover that and recommend a fee-only CPA or CFP.
