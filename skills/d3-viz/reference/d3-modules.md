# D3 v7 Module Quick Reference

Patterns and recipes for the most-used D3 modules. All examples assume `import * as d3 from "https://cdn.jsdelivr.net/npm/d3@7/+esm"` or the UMD bundle via `<script src="https://cdn.jsdelivr.net/npm/d3@7"></script>`.

---

## Scales

```javascript
// Linear (quantitative → pixel)
const x = d3.scaleLinear().domain([0, max]).range([0, width]);

// Time (Date → pixel)
const x = d3.scaleTime().domain([startDate, endDate]).range([0, width]);

// Band (categorical → pixel, for bar charts)
const x = d3.scaleBand().domain(categories).range([0, width]).padding(0.2);

// Point (categorical → pixel, for dot plots)
const x = d3.scalePoint().domain(categories).range([0, width]).padding(0.5);

// Ordinal (categorical → color)
const color = d3.scaleOrdinal().domain(groups).range(d3.schemeTableau10);

// Sequential (quantitative → color)
const color = d3.scaleSequential().domain([min, max]).interpolator(d3.interpolateBlues);

// Diverging (quantitative with midpoint → color)
const color = d3.scaleDiverging().domain([min, mid, max]).interpolator(d3.interpolateRdBu);

// Sqrt (for bubble/circle area encoding — never use linear for area)
const r = d3.scaleSqrt().domain([0, maxValue]).range([2, 30]);

// Log (wide-range quantitative data)
const y = d3.scaleLog().domain([1, max]).range([height, 0]);
```

## Axes

```javascript
// Create axes
const xAxis = d3.axisBottom(x).ticks(6).tickSize(-height); // inner gridlines if needed
const yAxis = d3.axisLeft(y).ticks(5).tickFormat(d3.format(",.0f"));

// Render and style (Tufte-clean)
svg.append("g")
  .attr("transform", `translate(0,${height})`)
  .call(xAxis)
  .call(g => g.select(".domain").remove())           // remove axis line
  .call(g => g.selectAll(".tick line").attr("stroke-opacity", 0.15)) // subtle ticks
  .call(g => g.selectAll(".tick text").attr("fill", "#888").attr("font-size", "11px"));

// Axis label
svg.append("text")
  .attr("x", width / 2).attr("y", height + 36)
  .attr("fill", "#aaa").attr("font-size", "12px").attr("text-anchor", "middle")
  .text("Revenue ($M)");
```

## Shape Generators

```javascript
// Line
const line = d3.line()
  .x(d => x(d.date))
  .y(d => y(d.value))
  .curve(d3.curveMonotoneX); // smooth, non-overshoot

// Area
const area = d3.area()
  .x(d => x(d.date))
  .y0(height)
  .y1(d => y(d.value))
  .curve(d3.curveMonotoneX);

// Arc (pie/donut)
const arc = d3.arc().innerRadius(radius * 0.5).outerRadius(radius);
const pie = d3.pie().value(d => d.value).sort(null);

// Stacking
const stack = d3.stack().keys(groupKeys).order(d3.stackOrderNone).offset(d3.stackOffsetNone);
const series = stack(data);

// Links (tree, hierarchy)
const link = d3.linkHorizontal().x(d => d.y).y(d => d.x); // swap for horizontal tree
```

## Layout Algorithms

```javascript
// Hierarchy (tree, treemap, sunburst, pack)
const root = d3.hierarchy(data).sum(d => d.value).sort((a, b) => b.value - a.value);

// Tree layout
const treeLayout = d3.tree().size([height, width]);
treeLayout(root);

// Treemap
const treemap = d3.treemap().size([width, height]).padding(2).round(true);
treemap(root);

// Pack (circle packing)
const pack = d3.pack().size([width, height]).padding(3);
pack(root);

// Partition (icicle, sunburst)
const partition = d3.partition().size([2 * Math.PI, radius]); // sunburst
// const partition = d3.partition().size([height, width]);       // icicle

// Force simulation (networks)
const simulation = d3.forceSimulation(nodes)
  .force("link", d3.forceLink(links).id(d => d.id).distance(60))
  .force("charge", d3.forceManyBody().strength(-200))
  .force("center", d3.forceCenter(width / 2, height / 2))
  .force("collision", d3.forceCollide().radius(d => r(d.value) + 2));
```

## Interaction Behaviors

```javascript
// Tooltip pattern
const tooltip = d3.select("body").append("div")
  .attr("class", "tooltip")
  .style("position", "absolute")
  .style("pointer-events", "none")
  .style("opacity", 0);

selection
  .on("mouseover", (event, d) => {
    tooltip.style("opacity", 1)
      .html(`<strong>${d.name}</strong><br>${format(d.value)}`)
      .style("left", (event.pageX + 12) + "px")
      .style("top", (event.pageY - 12) + "px");
  })
  .on("mouseout", () => tooltip.style("opacity", 0));

// Brush (1D)
const brush = d3.brushX()
  .extent([[0, 0], [width, height]])
  .on("brush end", ({selection}) => {
    if (!selection) return;
    const [x0, x1] = selection.map(x.invert);
    // filter or highlight data in [x0, x1]
  });

// Brush (2D)
const brush2d = d3.brush()
  .extent([[0, 0], [width, height]])
  .on("brush end", ({selection}) => {
    if (!selection) return;
    const [[x0, y0], [x1, y1]] = selection;
    // filter data within rectangle
  });

// Zoom + pan
const zoom = d3.zoom()
  .scaleExtent([1, 10])
  .translateExtent([[0, 0], [width, height]])
  .on("zoom", ({transform}) => {
    g.attr("transform", transform);
    // or: rescale axes
    gX.call(xAxis.scale(transform.rescaleX(x)));
  });

svg.call(zoom);

// Voronoi hover (nearest-point detection for scatter/line)
const delaunay = d3.Delaunay.from(data, d => x(d.x), d => y(d.y));
const voronoi = delaunay.voronoi([0, 0, width, height]);

svg.on("mousemove", (event) => {
  const [mx, my] = d3.pointer(event);
  const i = delaunay.find(mx, my);
  // highlight data[i]
});
```

## Color Schemes

```javascript
// Categorical (discrete groups)
d3.schemeTableau10     // 10 colors — best default
d3.schemeCategory10    // 10 colors — classic D3
d3.schemeDark2         // 8 colors — muted
d3.schemePaired        // 12 colors — paired light/dark

// Sequential (single hue, light → dark)
d3.interpolateBlues
d3.interpolateGreens
d3.interpolateOranges
d3.interpolatePurples
d3.interpolateGreys

// Sequential multi-hue (perceptually uniform)
d3.interpolateViridis   // yellow → green → blue → purple
d3.interpolatePlasma    // yellow → magenta → blue
d3.interpolateTurbo     // rainbow but perceptually uniform
d3.interpolateWarm      // magenta → yellow
d3.interpolateCool      // cyan → magenta

// Diverging (two extremes with neutral midpoint)
d3.interpolateRdBu      // red ↔ blue
d3.interpolateRdYlGn    // red ↔ green (via yellow)
d3.interpolateBrBG      // brown ↔ teal
d3.interpolatePiYG      // pink ↔ green
```

## Data Transforms

```javascript
// Binning (histograms)
const bins = d3.bin().domain(x.domain()).thresholds(20)(data.map(d => d.value));

// Rolling / moving average
function movingAverage(data, n) {
  return data.map((d, i, arr) => {
    const start = Math.max(0, i - n + 1);
    const subset = arr.slice(start, i + 1);
    return { ...d, avg: d3.mean(subset, d => d.value) };
  });
}

// Group and aggregate
const grouped = d3.group(data, d => d.category);
const rolled = d3.rollup(data, v => d3.mean(v, d => d.value), d => d.category);

// Flatten hierarchy
const flat = root.descendants(); // all nodes
const leaves = root.leaves();    // leaf nodes only

// Extent, min, max, mean, median
const [min, max] = d3.extent(data, d => d.value);
const avg = d3.mean(data, d => d.value);
const mid = d3.median(data, d => d.value);

// Number formatting
d3.format(",.0f")(12345)    // "12,345"
d3.format("$.2s")(1500000)  // "$1.5M"
d3.format("+.1%")(0.123)    // "+12.3%"

// Date formatting
d3.utcFormat("%b %Y")(date)    // "Jan 2024"
d3.utcFormat("%Y-%m-%d")(date) // "2024-01-15"

// Date parsing
d3.utcParse("%Y-%m-%d")("2024-01-15") // Date object
```

## Margin Convention

```javascript
const margin = { top: 40, right: 30, bottom: 50, left: 60 };
const width = 800 - margin.left - margin.right;
const height = 500 - margin.top - margin.bottom;

const svg = d3.select("#chart").append("svg")
  .attr("viewBox", `0 0 ${width + margin.left + margin.right} ${height + margin.top + margin.bottom}`)
  .append("g")
  .attr("transform", `translate(${margin.left},${margin.top})`);
```

## Responsive Pattern

```javascript
// Use viewBox instead of fixed width/height for responsive SVG
const svg = d3.select("#chart").append("svg")
  .attr("viewBox", `0 0 ${totalWidth} ${totalHeight}`)
  .style("max-width", "100%")
  .style("height", "auto");
```
