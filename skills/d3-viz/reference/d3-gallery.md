# D3 Chart Gallery — Organized by Data Question

Select charts based on what the user is asking about their data, not by chart name. When multiple chart types fit, prefer the one highest in each section (listed by general effectiveness).

---

## Temporal — "How does X change over time?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Line chart** | Continuous variable over continuous time | Fewer than 5 time points; categorical x-axis | `d3.line`, `d3.scaleTime`, `d3.curveMonotoneX` | Direct-label end of each line; no legend |
| **Multi-line (spaghetti)** | Compare 2–7 series over same time range | >7 series (use small multiples instead) | `d3.line`, `d3.scaleOrdinal` | Highlight one line, gray the rest on hover |
| **Area chart** | Emphasize magnitude of a single series | Comparing multiple series (overlap hides data) | `d3.area`, `d3.curveMonotoneX` | Fill with low opacity; baseline at zero |
| **Stacked area** | Show part-to-whole composition over time | Comparing individual magnitudes (hard to read non-bottom layers) | `d3.stack`, `d3.area` | Consider streamgraph for symmetry |
| **Streamgraph** | Organic/flowing composition over time | Precise value reading needed | `d3.stack(offset: d3.stackOffsetWiggle)` | Annotation essential — readers can't extract exact values |
| **Horizon chart** | Many (10–50+) time series in limited vertical space | Few series (use line chart) | Custom bands with clipping | Extreme data density; explain encoding in subtitle |
| **Sparklines** | Inline micro time-series in tables or text | Need precise axis labels | `d3.line` at tiny scale | Tufte's invention — pure data-ink |
| **Candlestick / OHLC** | Financial open-high-low-close data | Non-financial continuous data | `d3.scaleBand` + custom rect/line | Minimal, no fills unless signaling direction |
| **Gantt / timeline** | Events with start and end times | Continuous measurements | `d3.scaleBand`, `d3.scaleTime` | Direct-label bars; skip legend |
| **Calendar heatmap** | Daily patterns over months/years | Non-daily data | `d3.utcDay`, `d3.scaleSequential` | GitHub-style; annotate outlier days |
| **Small multiple lines** | Compare 7+ series individually | Few series (use multi-line) | `d3.line` in a grid | Shared axes; one panel per series |

---

## Comparison — "How does X compare to Y?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Bar chart** | Compare discrete categories by one measure | >20 categories (consider dot plot) | `d3.scaleBand`, `d3.scaleLinear` | Sort by value, not alphabetically; direct-label |
| **Horizontal bar** | Long category labels; ranked list | Few short labels (vertical bar fine) | Same as bar, swap axes | Natural reading order for labels |
| **Grouped bar** | Compare 2–4 sub-categories per category | >4 groups (too crowded) | `d3.scaleBand` nested | Direct-label or legend only if >3 groups |
| **Dot plot (Cleveland)** | Precise value comparison across many categories | Showing magnitude (bar better) | `d3.scalePoint`, circles | Tufte-preferred over bars for precision |
| **Lollipop** | Like bar chart but lighter visual weight | Dense or crowded layouts | `d3.scaleBand`, line + circle | High data-ink ratio alternative to bars |
| **Dumbbell / range** | Compare two values per category (before/after, min/max) | More than 2 values per item | Line + 2 circles | Excellent for showing change or gap |
| **Slope chart** | Compare rankings or values at exactly 2 time points | >2 time points (use line chart) | `d3.scalePoint` (2 columns) | Direct-label both ends |
| **Bullet chart** | Actual vs target with qualitative ranges | No target/benchmark context | Custom rect layering | Tufte-designed; maximum information density |
| **Radar / spider** | Compare multivariate profiles (3–8 variables) | >8 variables; precise comparison needed | `d3.lineRadial`, `d3.scaleLinear` | Controversial — use carefully, consider parallel coordinates |
| **Parallel coordinates** | Compare items across many (5–30) quantitative dimensions | Categorical data; few dimensions | `d3.scaleLinear` per axis, `d3.line` | Interactive brushing essential for readability |

---

## Distribution — "How is X spread?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Histogram** | Distribution of a single continuous variable | Categorical data; comparing distributions | `d3.bin`, `d3.scaleLinear` | Annotate mean/median directly on chart |
| **Density plot (KDE)** | Smooth distribution estimate | Small sample sizes (<30) | `d3.line` + kernel density estimation | Rug plot underneath adds data-ink |
| **Box plot** | Compare distributions across categories | Single distribution (use histogram) | Custom (whiskers + box + median) | Show individual points alongside |
| **Violin plot** | Distribution shape comparison across groups | Few data points per group | Mirror density + `d3.area` | More informative than box plot |
| **Strip / jitter plot** | Show individual points by category | >500 points per group (overplotting) | `d3.scalePoint` + jitter | Every point visible — maximum data-ink |
| **Beeswarm** | Show individual points without overlap | Very large datasets | `d3.forceSimulation` or dodge | Beautiful but expensive to compute |
| **Ridgeline (joy plot)** | Compare many distributions with overlap | Precise value reading | Offset `d3.area` per group | Dramatic visual; annotate peaks |
| **Dot histogram** | Small dataset distribution | >100 points | Stack circles in bins | Wilkinson-style; every point represented |
| **Q-Q plot** | Compare distribution to theoretical or another dataset | General audience (specialized) | `d3.line`, sorted quantiles | Reference diagonal line essential |

---

## Correlation — "How do X and Y relate?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Scatter plot** | Two continuous variables, <5000 points | Categorical axes; massive datasets | `d3.scaleLinear`, circles | Direct-label notable points; regression line if meaningful |
| **Bubble chart** | Scatter + a third variable as size | Too many overlapping bubbles | `d3.scaleSqrt` for radius | Area, not radius, should encode value |
| **Hexbin** | Dense scatter (5000+ points) | Sparse data (use scatter) | `d3.hexbin` | Color encodes count; shows density patterns |
| **Contour density** | Show density in 2D continuous space | Sparse data; need individual points | `d3.contourDensity` | Topographic metaphor; annotate peaks |
| **Connected scatter** | Two variables changing over time | No temporal ordering | `d3.line` + circles, arrows for direction | Label time points along path |
| **Heatmap (matrix)** | Correlation matrix or dense 2D grid data | Sparse data; continuous axes | `d3.scaleSequential`, rect grid | Sequential color scale; annotate values in cells |
| **Pair plot (scatter matrix)** | Explore correlations across 3–6 variables | >6 variables (too many panels); single pair | Grid of scatter plots | Small multiples principle |

---

## Composition — "What are the parts of the whole?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Stacked bar** | Part-to-whole at discrete points | >6 categories (hard to track) | `d3.stack`, `d3.scaleBand` | Direct-label segments when possible |
| **100% stacked bar** | Compare proportions across categories | Absolute values matter | `d3.stack(offset: d3.stackOffsetExpand)` | Show % labels on segments |
| **Waffle chart** | Part-to-whole with countable units | Precise percentages needed | Grid of rects | Each square = 1%; very readable |
| **Treemap** | Hierarchical part-to-whole by area | Deep hierarchies (>3 levels); few items | `d3.treemap`, `d3.hierarchy` | Direct-label; avoid tiny unreadable slices |
| **Sunburst** | Hierarchical with drill-down interaction | Flat data; comparison needed | `d3.partition`, `d3.arc` | Interactive zoom essential |
| **Pie/donut** | Only if exactly 2–3 slices | >4 slices; any precise comparison | `d3.pie`, `d3.arc` | Almost always prefer bar chart instead |
| **Icicle / partition** | Hierarchical data, rectangular layout | Flat data | `d3.partition`, `d3.hierarchy` | Good for file trees, org charts |
| **Marimekko (mosaic)** | Two categorical variables, proportional | Precise reading needed | Variable-width stacked bars | Annotate both dimensions |

---

## Flow & Network — "How do things connect or move?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Sankey diagram** | Flow quantities between stages | >20 nodes (too crowded) | `d3-sankey` | Width = quantity; direct-label nodes |
| **Chord diagram** | Bidirectional flows between categories | Unidirectional flow; many categories | `d3.chord`, `d3.ribbon` | Interactive hover to highlight one group |
| **Force-directed graph** | Explore network structure | >500 nodes (performance); precise layout needed | `d3.forceSimulation`, `d3.forceLink` | Size nodes by degree; label key nodes |
| **Arc diagram** | Network with clear ordering on one axis | Dense networks | Arcs between points on a line | Low visual weight; sort meaningfully |
| **Adjacency matrix** | Dense networks where structure matters | Sparse networks; need to see paths | Rect grid, color = connection | Reorder rows/cols to reveal clusters |
| **Tree layout** | Hierarchical parent-child data | Cyclic graphs | `d3.tree`, `d3.hierarchy` | Tidy layout; direct-label |
| **Radial tree** | Hierarchy with space efficiency | Need to read labels easily | `d3.tree` + polar transform | Leaves on outside; center is root |
| **Cluster dendrogram** | Hierarchical clustering results | Non-hierarchical data | `d3.cluster`, `d3.hierarchy` | Distance on axis meaningful |

---

## Spatial — "Where is X?"

| Chart | When to use | When NOT to use | Key D3 modules | Tufte note |
|-------|-------------|-----------------|-----------------|------------|
| **Choropleth** | Values by geographic region | Point data; comparing distant regions | `d3.geoPath`, `d3.geoProjection`, TopoJSON | Sequential color scale; annotate outliers |
| **Bubble map** | Point values at geographic locations | Area/region data | `d3.geoPath` + scaled circles | Area encodes value, not radius |
| **Dot density map** | Distribution of individual items | Aggregated region data | Random point placement within regions | One dot = N items; state in subtitle |
| **Cartogram** | Distort geography to encode a variable | Need accurate geography | Custom or Dorling circles | Annotate what distortion means |
| **Hexbin map** | Uniform spatial binning | Need precise locations | `d3.hexbin` + geo projection | Equal-area bins avoid geographic bias |
| **Connection map** | Routes, flows between locations | No geographic relationship | `d3.geoPath` + great circle arcs | Width or color for flow magnitude |
| **Tile grid map** | Equal representation per region (e.g., US states) | Need geographic accuracy | Rect grid positioned manually | One tile per region; avoids area bias |

---

## Index by Interaction Type

Use this when the user emphasizes a specific interaction need:

| Interaction | Best chart types |
|-------------|-----------------|
| **Tooltip on hover** | Scatter, line, bar, map, treemap — nearly all |
| **Brush to filter** | Scatter, parallel coordinates, time series, histogram |
| **Zoom + pan** | Maps, dense scatter, treemap, network graphs |
| **Drill-down** | Treemap, sunburst, icicle, hierarchy trees |
| **Linked views** | Scatter + bar, map + histogram, any pair via shared selection |
| **Animated transitions** | Stacked bar ↔ grouped bar, choropleth over time, scatter with morphing |
| **Search / highlight** | Network graphs, parallel coordinates, dense scatter, large bar charts |
| **Voronoi hover** | Scatter, multi-line (nearest-point detection) |
