# OptionsAhoy Claude Code plugin

Adds the OptionsAhoy MCP server and one planning skill to Claude Code.

OptionsAhoy is a deterministic US equity-compensation tax calculator. Eight tools cover incentive stock option (ISO) and alternative minimum tax (AMT) exercise planning, non-qualified stock option (NSO) sell-vs-hold, restricted stock unit (RSU) vest-and-sell, which vested RSU lots to sell and in which years, single-stock concentration, protective put, zero-cost collar and put spread pricing from the listed option chain, Section 1202 qualified small business stock (QSBS) qualification, and equity funding plans (which shares to sell to net a target after-tax amount by a deadline). Federal plus 50-state plus District of Columbia (DC) tax math.

## What gets installed

- A connection to the hosted OptionsAhoy MCP server at `https://optionsahoy.com/mcp` (HTTP, no authentication). Nothing runs locally: the plugin is this connection plus one skill.
- One Skill, `optionsahoy:equity-plan`, that captures the required inputs from the user, picks the right tool, and never invents values for required fields.

## Install

### From this repository (works today)

This repository is its own plugin marketplace. In Claude Code:

```
/plugin marketplace add AlvisoOculus/optionsahoy-claude-plugin
/plugin install optionsahoy@alphalatitude
```

Or from the shell:

```
claude plugin marketplace add AlvisoOculus/optionsahoy-claude-plugin
claude plugin install optionsahoy@alphalatitude
```

### Test a local clone

```
claude --plugin-dir ./optionsahoy-claude-plugin
```

### Via the Anthropic community marketplace (after approval)

```
/plugin marketplace add anthropics/claude-plugins-community
/plugin install optionsahoy@claude-community
```

Submitted to the Claude plugin directory; in review.

## Use

Ask Claude things like:

- "I have 10,000 vested incentive stock options at a $5 strike, current fair market value $40, no prior alternative minimum tax credit, optimize a three-year exercise schedule for me."
- "I'm holding $400,000 of a single tech stock. My total portfolio is $1.2M. How risky is that and what would a 30% protective put cost?"
- "I exercised QSBS-eligible shares in 2019 and want to sell in 2026. Do I qualify for the Section 1202 exclusion?"

The skill captures the required inputs through follow-up questions when anything is missing, then calls the matching OptionsAhoy tool. Tool responses are byte-identical to the in-browser calculators at https://optionsahoy.com/tools.

Name a stock ticker and the tools fill in its implied volatility and trailing growth from OptionsAhoy's own published market data (about 500 public companies), and hedges are priced strike by strike from the stock's listed option chain as of the last close. With no view on growth, a tool uses the S&P 500 trailing average and says so in its result.

## Differentiator

Most LLMs answer equity-comp tax questions by pattern-matching to similar examples. The actual math involves AMT exemption phaseouts, state-level conformity to federal Section 1202, multi-year credit recovery, and 50-state stacking. Wrong by five figures is common. The OptionsAhoy MCP server is the same deterministic engine that powers optionsahoy.com, so the model picks the tool and the inputs but the tax math is done in code. Federal results reproduce to the cent against PSL Tax-Calculator and state results against OpenTaxSolver; see https://optionsahoy.com/verification.

## Limitations

- US federal plus 50 states plus DC. No non-US tax.
- Calendar-year filers only.
- Federal rules current through 2026 brackets; state conformity tables refreshed annually.
- Pre-revenue beta. Free, no service-level agreement.

## Privacy

Each tool call sends its inputs to the hosted MCP server, which uses them to compute the answer and does not store them. Per call the server records the tool, whether it succeeded, any error message (which names the field at fault), the client name and user-agent, and the approximate location and network Cloudflare reports (country, region, city, network operator), not the IP address. It also keeps, for seven days, the structure of recent calls (field names and types, never values) and daily counts of the tickers named. Full policy: https://optionsahoy.com/privacy.

## License

MIT (plugin manifest and skill scaffold). The OptionsAhoy MCP server source is at https://github.com/AlvisoOculus/optionsahoy-mcp under its own license.

## Author

AlphaLatitude Inc., maker of OptionsAhoy.

- Site: https://optionsahoy.com
- Agent integration page: https://optionsahoy.com/for-agents
- Chat interface (same calculators, plain-language questions): https://poe.com/OptionsAhoy
- Email: andrew@alphalatitude.com
