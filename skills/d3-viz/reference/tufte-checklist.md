# Tufte Post-Generation Audit Checklist

Run this checklist after generating every D3 visualization. Fix all violations before presenting to the user.

---

## Data-Ink Ratio

- [ ] **No chartjunk**: Remove decorative elements that don't encode data (3D effects, gradient fills, drop shadows on data elements, background images)
- [ ] **No gridlines**: Remove default gridlines. If reference lines are needed, use light `stroke-opacity: 0.15` and only at meaningful thresholds
- [ ] **Minimal axis chrome**: Remove outer axis lines (`domain`). Tick marks point inward or are absent. Axis labels are small and muted (`fill: #888`)
- [ ] **No redundant encoding**: Don't use color AND shape AND size to encode the same variable. One encoding per variable
- [ ] **Remove axis if labels suffice**: If every bar is directly labeled with its value, the numeric axis is redundant — remove it
- [ ] **White space is intentional**: Margins serve data comprehension, not decoration. Every pixel of padding should aid readability

## Labels & Legends

- [ ] **Direct labels over legends**: Label data elements directly (line ends, bar tops, notable points) instead of using a separate legend box
- [ ] **No legend box if ≤5 series**: Use direct labels with matching colors. Reserve legends for >5 series where direct labeling is impractical
- [ ] **Axis labels present and clear**: Every axis has a label describing the variable and unit (e.g., "Revenue ($M)" not just "value")
- [ ] **Title is a finding, not a description**: Prefer "Sales doubled after launch" over "Sales over Time". If descriptive title is more appropriate, add a subtitle with the key finding

## Color

- [ ] **Color carries meaning**: Every color used must encode data. No decorative color variation
- [ ] **Sequential scales for ordered data**: Use `d3.interpolateBlues` or similar for quantities. Never use rainbow/spectral for sequential data
- [ ] **Diverging scales for data with meaningful midpoint**: Use `d3.interpolateRdBu` or similar when zero/mean is semantically meaningful
- [ ] **Categorical palette limited**: Max 7–8 distinct colors. Beyond that, use faceting or interaction
- [ ] **Accessible palette**: Ensure sufficient contrast. Test with colorblind simulation. Prefer palettes that work in grayscale (vary lightness, not just hue)

## Density & Context

- [ ] **High information density**: The chart should communicate more information per square inch than a table of the same data would. If it doesn't, consider adding annotations, small multiples, or using a more compact chart type
- [ ] **Reference context provided**: Include relevant baselines, averages, benchmarks, or historical comparisons as light reference lines/areas
- [ ] **Annotation on notable points**: Unusual spikes, drops, crossovers, or milestones get direct text annotation on the chart
- [ ] **Small multiples over interaction**: If comparing 4+ subgroups, prefer small multiples (one chart per group, shared axes) over dropdown/toggle interaction that hides data

## Annotation

- [ ] **At least one annotation**: Every chart should have at least one call-out annotating a key data point, trend, or event — placed directly on the chart area
- [ ] **Annotations are concise**: 3–8 words per annotation. Point to the exact data element with a thin line or arrow
- [ ] **Subtitle provides context**: Include a subtitle with methodology, data source, time range, or key takeaway

## Interaction

- [ ] **Interaction reveals, not decorates**: Tooltips show additional data dimensions not in the static view. Hover effects dim non-hovered elements for focus
- [ ] **No interaction-gated essential data**: The static (non-interactive) view must be comprehensible on its own. Interaction adds depth, not basic readability
- [ ] **Transitions are fast**: Transition duration ≤300ms. Easing is `d3.easeCubicOut`. No bouncing or elastic effects
