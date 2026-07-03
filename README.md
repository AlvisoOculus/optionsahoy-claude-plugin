# OptionsAhoy Claude Code plugin

Adds the OptionsAhoy MCP server and one planning skill to Claude Code.

OptionsAhoy is a deterministic US equity-compensation tax optimizer. Seven tools cover incentive stock option (ISO) and alternative minimum tax (AMT) exercise planning, non-qualified stock option (NSO) sell-vs-hold, restricted stock unit (RSU) vest-and-sell, single-stock concentration, protective put and zero-cost collar pricing, Section 1202 qualified small business stock (QSBS) qualification, and equity funding plans (which shares to sell to net a target after-tax amount by a deadline). Federal plus 50-state plus District of Columbia (DC) tax math.

## What gets installed

- A connection to the hosted OptionsAhoy MCP server at `https://optionsahoy.com/mcp` (HTTP, no authentication, no rate limit during beta).
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

Submitted to the Claude plugin directory 2026-05-27; awaiting Anthropic review. The current submission form is clau.de/plugin-directory-submission.

## Use

Ask Claude things like:

- "I have 10,000 vested incentive stock options at a $5 strike, current fair market value $40, no prior alternative minimum tax credit, optimize a three-year exercise schedule for me."
- "I'm holding $400,000 of a single tech stock. My total portfolio is $1.2M. How risky is that and what would a 30% protective put cost?"
- "I exercised QSBS-eligible shares in 2019 and want to sell in 2026. Do I qualify for the Section 1202 exclusion?"

The skill captures the required inputs through follow-up questions when anything is missing, then calls the matching OptionsAhoy tool. Tool responses are byte-identical to the in-browser calculators at https://optionsahoy.com/tools.

## Differentiator

Most LLMs answer equity-comp tax questions by pattern-matching to similar examples. The actual math involves AMT exemption phaseouts, state-level conformity to federal Section 1202, the kiddie tax, multi-year credit recovery, and 50-state stacking. Wrong by five figures is common. The OptionsAhoy MCP server is the same deterministic optimizer that powers optionsahoy.com, so the model picks the tool and the inputs but the tax code is enforced in code.

## Limitations

- US federal plus 50 states plus DC. No non-US tax.
- Calendar-year filers only.
- Federal rules current through 2026 brackets; state conformity tables refreshed annually.
- Pre-revenue beta. Free, no service-level agreement.

## Privacy

The hosted MCP server logs only structural data (tool name, success or failure, request timestamp). It does not store or retain the dollar amounts, share counts, or other personal financial figures in your conversations.

## License

MIT (plugin manifest and skill scaffold). The OptionsAhoy MCP server source is at https://github.com/AlvisoOculus/optionsahoy-mcp under its own license.

## Author

AlphaLatitude Inc., maker of OptionsAhoy.

- Site: https://optionsahoy.com
- Agent integration page: https://optionsahoy.com/for-agents
- Chat interface (same calculators, plain-language questions): https://poe.com/OptionsAhoy
- Email: andrew@alphalatitude.com
