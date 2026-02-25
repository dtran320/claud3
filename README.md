# d3-viz

A Claude Code plugin that creates D3.js data visualizations grounded in Edward Tufte's information design principles.

## What it does

When you ask Claude to visualize data, this skill:

1. **Selects the right chart** by analyzing your data question (not chart name) — "how does X change over time?" maps to line charts, "how is X spread?" maps to histograms, etc.
2. **Generates a single-file HTML** with inline D3 v7, dark theme, responsive SVG, and clean Tufte styling
3. **Applies Tufte principles automatically** — direct labels instead of legends, annotations on key points, high data-ink ratio, no chartjunk
4. **Opens in your browser** for immediate viewing

## Features

- **80+ chart types** organized by data question (temporal, comparison, distribution, correlation, composition, flow, spatial)
- **Tufte audit checklist** — every generated chart is checked against 25 design rules before output
- **Playground mode** — say "explore this data" to get an interactive playground with chart type cards, field dropdowns, and Tufte toggles
- **Smart data handling** — works with CSV files, JSON, inline data, URLs, or just a description of your data

## Installation

Add the marketplace, then install the plugin:

```
/plugin marketplace add dtran320/claud3
/plugin install d3-viz@claud3
```

Or test locally by cloning:

```bash
git clone https://github.com/dtran320/claud3.git
claude --plugin-dir ./claud3
```

## Usage

```
/claud3:d3-viz
```

Or just ask Claude to visualize data — the skill triggers on keywords like "visualize", "chart", "plot", "graph", or "explore data".

### Examples

- "Visualize this CSV of monthly revenue by region"
- "Show me how departments compare on headcount"
- "Let me explore this sales dataset" (triggers playground mode)
- "What does the distribution of response times look like?"

## Included references

| File | Purpose |
|------|---------|
| `d3-gallery.md` | 80+ chart types organized by data question |
| `tufte-checklist.md` | 25-item post-generation audit |
| `d3-modules.md` | D3 v7 module patterns and recipes |
| `playground-integration.md` | D3 playground template for `/playground` integration |
