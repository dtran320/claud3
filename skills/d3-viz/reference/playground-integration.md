# D3 Visualization Playground Integration

How to create a D3 data exploration playground using the `/playground` skill conventions. Use this mode when the user wants to interactively explore data, try different chart types, or tune visualization parameters.

---

## When to Use Playground Mode

Trigger playground mode when the user says:
- "explore this data", "play with", "try different views"
- "let me adjust", "interactive", "experiment with"
- "what chart works best", "compare chart types"

For direct requests like "make me a bar chart of X", use standalone generation instead.

## State Object Structure

Follow the playground skill's single `state` object + `updateAll()` pattern. For D3 playgrounds, the state includes:

```javascript
const DEFAULTS = {
  chartType: 'line',
  xField: null,           // auto-detected
  yField: null,           // auto-detected
  colorField: null,       // optional grouping
  xScale: 'auto',        // 'linear' | 'time' | 'band' | 'log' | 'auto'
  yScale: 'linear',      // 'linear' | 'log' | 'sqrt'
  colorScheme: 'tableau', // 'tableau' | 'blues' | 'viridis' | 'rdbu'
  sortBy: 'none',        // 'none' | 'ascending' | 'descending'
  showAnnotations: true,
  showDirectLabels: true,
  showGrid: false,        // off by default (Tufte)
  showAvgLine: false,
  darkTheme: true,
};
const state = { ...DEFAULTS };
```

## Control Types for D3 Playgrounds

| Control | Implementation | Use for |
|---------|---------------|---------|
| **Chart type cards** | Clickable card grid with chart icons (SVG thumbnails) | Switching between compatible chart types |
| **Field dropdowns** | `<select>` populated from data column names | X axis, Y axis, color/group field |
| **Scale radios** | Radio button group | Linear / log / time scale selection |
| **Color swatches** | Clickable color circles or small ramps | Color scheme selection |
| **Tufte toggles** | Toggle switches | Annotations, direct labels, grid, average line |
| **Range slider** | `<input type="range">` | Bin count, point size, opacity, transition speed |
| **Presets** | Named buttons | "Tufte minimal", "Annotated", "Dense", "Presentation" |

## Layout

```
+-------------------+------------------------------------------+
|                   |                                          |
| Controls          |  D3 SVG Preview                          |
|                   |  (re-renders on every state change)      |
| [Chart Type]      |                                          |
| [X Field ▼]      |                                          |
| [Y Field ▼]      |                                          |
| [Color Field ▼]  |                                          |
| [Scale: ○ Lin ○ Log] |                                      |
| [Colors: ● ● ● ●]|                                          |
|                   |                                          |
| ── Tufte ──       |                                          |
| [✓] Annotations  |                                          |
| [✓] Direct labels |                                          |
| [ ] Grid          |                                          |
|                   +------------------------------------------+
| ── Presets ──     |  Prompt output                           |
| [Tufte] [Dense]  |  "Create a line chart of revenue over    |
| [Present] [Raw]  |   time, grouped by region, with direct   |
|                   |   labels and annotations on peaks..."    |
|                   |  [ Copy Prompt ]                         |
+-------------------+------------------------------------------+
```

## D3 Re-render Pattern

The preview area contains an SVG that is fully re-rendered on every state change. Do NOT use D3 transitions during re-render (they conflict with rapid control changes). Only use transitions for data-driven interactions like hover.

```javascript
function renderPreview() {
  const container = document.getElementById('preview');
  container.innerHTML = ''; // clear previous

  const margin = { top: 40, right: 30, bottom: 50, left: 60 };
  const width = container.clientWidth - margin.left - margin.right;
  const height = container.clientHeight - margin.top - margin.bottom;

  const svg = d3.select(container).append('svg')
    .attr('viewBox', `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`)
    .append('g')
    .attr('transform', `translate(${margin.left},${margin.top})`);

  // Route to chart-specific renderer
  const renderers = { line: renderLine, bar: renderBar, scatter: renderScatter, /* ... */ };
  const render = renderers[state.chartType] || renderers.line;
  render(svg, width, height);

  // Apply Tufte toggles
  if (state.showAnnotations) addAnnotations(svg, width, height);
  if (state.showDirectLabels) addDirectLabels(svg);
  if (state.showAvgLine) addAverageLine(svg, width, height);
}

function updateAll() {
  renderPreview();
  updatePrompt();
}
```

## Prompt Output for D3 Playground

Generate a natural-language visualization specification that includes enough context to reproduce the chart without the playground:

```javascript
function updatePrompt() {
  const parts = [`Create a ${state.chartType} chart`];

  if (state.xField) parts.push(`with ${state.xField} on the x-axis`);
  if (state.yField) parts.push(`and ${state.yField} on the y-axis`);
  if (state.colorField) parts.push(`grouped by ${state.colorField}`);

  if (state.yScale !== 'linear') parts.push(`using a ${state.yScale} scale`);
  if (state.colorScheme !== DEFAULTS.colorScheme)
    parts.push(`with ${state.colorScheme} colors`);
  if (state.sortBy !== 'none')
    parts.push(`sorted ${state.sortBy}`);

  // Tufte features (only mention non-defaults)
  const tufteOn = [];
  const tufteOff = [];
  if (state.showAnnotations) tufteOn.push('annotations on key points');
  if (state.showDirectLabels) tufteOn.push('direct labels (no legend)');
  if (state.showGrid) tufteOn.push('subtle reference grid');
  if (state.showAvgLine) tufteOn.push('average reference line');
  if (!state.showAnnotations) tufteOff.push('annotations');
  if (!state.showDirectLabels) tufteOff.push('direct labels');

  if (tufteOn.length) parts.push(`with ${tufteOn.join(', ')}`);

  document.getElementById('prompt').textContent = parts.join(' ') + '.';
}
```

## Dark Theme CSS for D3 Playground

```css
:root {
  --bg: #1a1a2e;
  --surface: #16213e;
  --border: #2a2a4a;
  --text: #e0e0e0;
  --text-muted: #888;
  --accent: #4fc3f7;
  --accent-hover: #81d4fa;
}

body { background: var(--bg); color: var(--text); font-family: system-ui; }
.controls { background: var(--surface); border-right: 1px solid var(--border); }

/* SVG styles for dark theme */
.axis text { fill: var(--text-muted); }
.axis .domain { display: none; }
.axis .tick line { stroke: var(--border); stroke-opacity: 0.3; }
.annotation { fill: var(--text); font-size: 11px; }
.annotation-line { stroke: var(--text-muted); stroke-width: 1; }
```

## Presets

Define presets that snap all controls to cohesive combinations:

```javascript
const PRESETS = {
  tufte: {
    showAnnotations: true, showDirectLabels: true,
    showGrid: false, showAvgLine: false,
    colorScheme: 'tableau'
  },
  dense: {
    showAnnotations: true, showDirectLabels: true,
    showGrid: false, showAvgLine: true,
    colorScheme: 'viridis'
  },
  presentation: {
    showAnnotations: true, showDirectLabels: true,
    showGrid: false, showAvgLine: false,
    colorScheme: 'tableau'
  },
  raw: {
    showAnnotations: false, showDirectLabels: false,
    showGrid: true, showAvgLine: false,
    colorScheme: 'tableau'
  }
};
```

## How to Invoke from SKILL.md

When the d3-viz skill detects playground intent, instruct Claude to:

1. Analyze the data to determine available fields and types
2. Build the playground HTML following this template
3. Populate field dropdowns from actual data columns
4. Set intelligent defaults (auto-detect time columns, numeric columns)
5. Include 3–5 chart type options compatible with the data shape
6. Write the file and open with `open <filename>.html`
