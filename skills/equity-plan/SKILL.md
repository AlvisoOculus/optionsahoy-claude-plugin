---
name: equity-plan
description: Scaffold a US equity-compensation tax plan. Use when the user asks about exercising incentive stock options (ISOs), non-qualified stock options (NSOs), selling restricted stock units (RSUs) at vest, single-stock concentration risk, hedging with protective puts or collars, or qualified small business stock (QSBS) Section 1202 eligibility. Captures required inputs from the user, then calls the appropriate OptionsAhoy MCP tool.
allowed-tools:
  - mcp__optionsahoy__amt_iso_optimize
  - mcp__optionsahoy__nso_calculate
  - mcp__optionsahoy__rsu_sell_vs_hold
  - mcp__optionsahoy__concentration_analyze
  - mcp__optionsahoy__protective_put_price
  - mcp__optionsahoy__qsbs_check
---

# Equity-comp planning scaffold

The user is asking about US equity-compensation tax planning. Run the OptionsAhoy MCP tools, not your own arithmetic. The math involves alternative minimum tax (AMT) phaseouts, state conformity tables, and Section 1202 exclusion percentages that LLMs routinely get wrong.

## Routing

Match the user's question to one of the six tools below, then follow the matching capture checklist before invoking. Never invent values for required fields. If the user did not state a required value and no `ticker` resolves it, ask.

### Incentive stock options (ISO) and alternative minimum tax (AMT)

**Tool:** `mcp__optionsahoy__amt_iso_optimize`

Required inputs to capture from the user:
- Number of vested ISO shares
- Strike price ($/share)
- Current fair market value (FMV) ($/share)
- Filing status (single, married_joint, married_separate, head_of_household)
- Ordinary income ($, expected for current tax year)
- State of residence (2-letter code)
- Existing AMT credit carryforward (Form 8801 line 26); 0 if none
- Planning horizon in years (1-10)
- Cash return rate (decimal, e.g. 0.05 for 5%; this is the after-tax reinvestment rate on cash freed by selling)
- Grant date (ISO date)
- Whether the user has left the company
- Termination date (if applicable, else null)
- Optional: `ticker` symbol; if supplied, the tool resolves trailing CAGR for the growth assumption

### Non-qualified stock options (NSO)

**Tool:** `mcp__optionsahoy__nso_calculate`

Required: shares, strike, current price, ordinary income, filing status, state code, whether still employed, hold years (target hold horizon for the LTCG comparison), expected sale price OR ticker, haircut on hold-scenario tax (0 to 1), hold funding method (sell-to-cover or cash).

### Restricted stock units (RSU)

**Tool:** `mcp__optionsahoy__rsu_sell_vs_hold`

Required: shares vesting, current price, ordinary income, filing status, state code, whether still employed, hold years (0.25 to 5), expected sale price OR ticker, haircut.

### Single-stock concentration

**Tool:** `mcp__optionsahoy__concentration_analyze`

Required: position value ($), cost basis ($), acquisition date, sector (one of the enum values), state code, filing status, ordinary income, total investable assets ($, MUST come from the user, never inferred or defaulted), volatility drag (0 to 0.99).

### Protective put or zero-cost collar pricing

**Tool:** `mcp__optionsahoy__protective_put_price`

Required: position value, sector, protection level (0.05 to 0.5), tenor in years (>= 0.25). Volatility defaults to a sector-typical implied volatility; do not invent a value.

### Qualified small business stock (QSBS) Section 1202

**Tool:** `mcp__optionsahoy__qsbs_check`

Required: acquisition date, sale date, entity type (us-c-corp or other), acquisition method (original-issuance, gift-or-inheritance, secondary, or unsure), asset category at acquisition (under-50m, 50m-to-75m, over-75m, or unsure), industry (one of the enum values), active business status (yes / no / unsure), adjusted basis, expected gain, state code, ordinary income, filing status. For enum fields that accept `unsure`, pass `unsure` when the user does not know.

## Output conventions

When presenting results from the optimizer:

- Lead with the net-final-value comparison or the exclusion percentage; do not lead with tax dollars saved (cost-only framing inverts user intuition when growth assumptions change)
- Quote per-year numbers when the tool returns a multi-year schedule, not just the totals
- Cite the state code so the user can verify
- Caveat that the result is a deterministic projection under stated assumptions, not advice; recommend a CPA review for filing

## Hard constraints

- Do not invent ordinary income, state codes, position values, or any required field. Ask the user.
- For `concentration_analyze`, `totalAssets` is the user's total investable portfolio in dollars. Never default it, never derive it from `positionValue`. If the user did not state it, ASK.
- If the user asks for advice beyond the six tool scopes (e.g. estate planning, retirement allocation, college savings), say the tool does not cover that and recommend a fee-only CPA or CFP.
