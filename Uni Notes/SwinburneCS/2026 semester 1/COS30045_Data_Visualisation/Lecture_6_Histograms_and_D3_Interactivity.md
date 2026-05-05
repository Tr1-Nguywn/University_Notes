## Part 1: Histograms in D3

### What is a Histogram
![[Pasted image 20260418132517.png]]
A **histogram** is a chart type that visualises the frequency distribution of a dataset by grouping continuous values into equally-sized ranges called **bins**, and drawing a rectangle for each bin whose height reflects how many data points fall within it.

In simple terms: it answers the question "how often does each range of values occur?"

Think of it like: sorting a deck of cards by rank into piles, then stacking the piles to see which ranks appear most.

Why it matters: distributions reveal shape (skewed, normal, bimodal) that summary statistics like averages hide entirely. Before building any interactive chart, understanding the underlying distribution is essential.

### The Process of Constructing a Histogram
Building a histogram in D3 follows three sequential steps.
![[Pasted image 20260418132618.png]]
First, you start with the original flat dataset:
![[Pasted image 20260418180941.png]]
Second, you pass it through `d3.bin()`, which groups data points into bins and returns an array of arrays, where each inner array holds the data points for that bin along with two special properties: 
- `x0` (the lower boundary)
- `x1` (the upper boundary).
![[Pasted image 20260418181127.png]]
![[Pasted image 20260418181209.png]]
Third, you use those binned arrays as data to draw SVG rectangle elements, sizing and positioning them using D3 scales derived from the bin boundaries.

The key insight is that `d3.bin()` transforms your data into a new dataset structure specifically shaped for histogram rendering. You configure the bin generator by chaining `.value()` to tell it which field to bin on:
```javascript
const binGenerator = d3.bin()
    .value(d => d.AverageScreenTime);

const bins = binGenerator(data);
```

### Bin Count and Granularity
**Bin count** controls how much detail the histogram reveals. Fewer bins (e.g., 5) give a broad overview but can obscure meaningful variation. More bins (e.g., 30) reveal finer detail but can make the chart noisy and harder to read. 
![[Pasted image 20260418181257.png]]

The D3 default for the screen time dataset yields 9 bins, which KNIME also defaults to 20 for the same data, so it is worth experimenting. There is no universally correct number; the right choice depends on how much data exists and what patterns you want to surface.

### Creating Scales for the Histogram
Two scales are needed: one for the x-axis (the value range across bins) and one for the y-axis (frequency/count within bins).
![[Pasted image 20260418181342.png]]
For `xScale`, the domain runs from the lower boundary of the first bin (`bins[0].x0`) to the upper boundary of the last bin (`bins[bins.length - 1].x1`), mapped to `[0, innerWidth]`:
```javascript
const minTime = bins[0].x0;
const maxTime = bins[bins.length - 1].x1;

const xScale = d3.scaleLinear()
    .domain([minTime, maxTime])
    .range([0, innerWidth]);
```

For `yScale`, the domain runs from zero up to the length of the tallest bin (`d3.max(bins, d => d.length)`). Critically, the range is **inverted** because SVG coordinates increase downward, meaning `[innerHeight, 0]` maps zero frequency to the bottom of the chart and maximum frequency to the top. Chaining `.nice()` rounds the axis to clean numbers:
```javascript
const binsMaxLength = d3.max(bins, d => d.length);

const yScale = d3.scaleLinear()
    .domain([0, binsMaxLength])
    .range([innerHeight, 0])
    .nice();
```

### Drawing the Histogram Bars
![[Pasted image 20260418183012.png]]
Bars are drawn as SVG `<rect>` elements. Each rectangle's horizontal position is derived from `d.x0` (where the bin starts), its width from the difference between `xScale(d.x1)` and `xScale(d.x0)`, and its height from `innerHeight - yScale(d.length)`. The `y` attribute sets the top edge of the bar, which is `yScale(d.length)`:
```javascript
innerChart
    .selectAll("rect")
    .data(bins)
    .join("rect")
    .attr("x", d => xScale(d.x0))
    .attr("y", d => yScale(d.length))
    .attr("width", d => xScale(d.x1) - xScale(d.x0))
    .attr("height", d => innerHeight - yScale(d.length))
    .attr("fill", slateGray)
    .attr("stroke", white)
    .attr("stroke-width", 2);
```

### Adding Axes and Labels
![[Pasted image 20260418183129.png]]
The x-axis is constructed with `d3.axisBottom(xScale)` and appended to a `<g>` element translated to the bottom of the inner chart (`translate(0, ${innerHeight})`).

![[Pasted image 20260418183145.png]]
The y-axis uses `d3.axisLeft(yScale)` and is appended at the default position (no translation needed since it sits at x = 0 within the inner chart). Labels are added as `<text>` elements on the outer SVG.

### File Structure
For maintainability, the course project separates concerns across multiple JS files: `shared-constants.js` holds layout dimensions and globally shared D3 scales and generators; `load-data.js` handles CSV loading and data cleaning via `d3.csv()`; `histogram.js` contains the `drawHistogram(data)` function; and `interactions.js` manages all event-driven behavior. All files are loaded in `index.html` in this order, since later files depend on the constants and data from earlier ones.

### Key Takeaways
1. `d3.bin()` is the engine that converts flat data into a structure D3 can use to draw bars; its output arrays carry `x0`, `x1`, and `length` properties that drive every scale and attribute.
2. The y-scale range must be inverted (`[innerHeight, 0]`) because SVG y-coordinates grow downward.
3. Bar height is always `innerHeight - yScale(d.length)`, not `yScale(d.length)` alone.
4. Bin count is a design decision: too few obscures distribution shape, too many creates visual noise.
5. Separating constants, data loading, and chart drawing into distinct files makes the codebase easier to extend with filtering and interaction.

**Remember this:** the histogram is a two-stage transformation: raw data into bins via `d3.bin()`, then bins into rectangles via D3's data-binding pattern.

**One analogy to remember it by:** binning is like sorting coins into a coin sorter by denomination, then stacking each column to see which coin you have the most of.

---

## Part 2: D3 Interactivity

### What is Interactivity in Data Visualisation?
![[Pasted image 20260418183602.png]]
**Interactivity** in data visualisation refers to mechanisms that allow a viewer to explore a dataset on their own terms, rather than being locked into a single static view chosen by the author. It is particularly valuable for large datasets and datasets with many attributes, where no single view can tell the whole story.

In simple terms: interactivity lets the viewer ask their own questions of the data.

Think of it like: the difference between watching a guided museum tour and being handed a map to wander freely.

Why it matters: a static chart forces a single narrative. An interactive chart lets domain experts, analysts, and general audiences each extract what is relevant to them.

The three basic forms of interaction covered in this week are filtering, tooltips, and animations.

### Filtering
**Filtering** lets users reduce the dataset to a subset of interest, causing the visualisation to update to reflect only the selected data.

The setup involves three files. In `shared-constants.js`, you define an array of filter option objects, each with an `id` (used internally), a `label` (displayed to the user), and an `isActive` boolean (tracking which filter is currently selected):
```javascript
const filters_gender = [
    { id: "all",    label: "All",    isActive: true  },
    { id: "female", label: "Female", isActive: false },
    { id: "male",   label: "Male",   isActive: false },
    { id: "other_prefer_not_to_say", label: "Other/Prefer Not to Say", isActive: false }
];
```

By default, "all" is active. In `index.html`, you add a `<div id="filters"></div>` after the chart call to serve as the button container. In `load-data.js`, you call `populateFilters(data)` inside the `.then()` block so the filter function has access to the loaded dataset.
![[Pasted image 20260418184755.png]]
The `populateFilters` function in `interactions.js` uses D3's data-binding pattern to create `<button>` elements from the filters array. It conditionally adds the CSS class `active` to whichever button is currently active:
```javascript
const populateFilters = (data) => {
    d3.select("#filters")
        .selectAll(".filter")
        .data(filters)
        .join("button")
        .attr("class", d => `filter ${d.isActive ? "active" : ""}`)
        .text(d => d.label);
};
```

### Capturing User Events
![[Pasted image 20260418185911.png]]
To respond to user clicks, you chain a `.on()` method to the D3 selection. The `.on()` method takes an event type as a string (such as `"click"`, `"mouseover"`, `"touch"`, or `"keydown"`) and a callback function that receives two parameters: `e` (the DOM event object, which contains information about cursor position and the target element) and `d` (the datum attached to the element by D3's data-binding):
```javascript
selection.on("event type", (e, d) => { /* ... */ });
```

For the filter buttons, clicking should only trigger an update if the clicked filter is not already active. The handler iterates through the filters array to update `isActive` state, then uses `.classed()` to add or remove the `active` CSS class on each button, and finally calls `updateHistogram()` with the new filter id and the full dataset.

### The `.classed()` Method
**`.classed()`** is a D3 method that dynamically adds or removes a CSS class on selected elements based on a boolean condition.
![[Pasted image 20260418185925.png]]
Its first argument is the class name string; its second is a boolean or a function that returns a boolean:
```javascript
d3.selectAll("#filters .filter")
    .classed("active", filter => filter.isActive);
```

If the function returns `true`, the class is added. If it returns `false`, the class is removed. This is the standard D3 pattern for reflecting application state visually through CSS.

### Updating the Histogram on Filter Change
The `updateHistogram` function receives the selected filter id and the full dataset. It uses a ternary to either return all data (when `filterId === "all"`) or filter using JavaScript's native `.filter()` array method to return only rows matching the selected gender:
```javascript
const updateHistogram = (filterId, data) => {
    const updatedData = filterId === "all"
        ? data
        : data.filter(respondent => respondent.gender === filterId);

    const updatedBins = binGenerator(updatedData);

    d3.selectAll("#histogram rect")
        .data(updatedBins)
        .attr("y", d => yScale(d.length))
        .attr("height", d => innerHeight - yScale(d.length));
};
```

Note that `binGenerator` and both scales must be accessible from `shared-constants.js`, not scoped locally inside the histogram drawing function.

### Animations with Transitions
D3 transitions allow attribute changes to animate smoothly rather than snap instantaneously. A transition is inserted into the method chain using `.transition()`, followed by optional configuration:
- `.duration(ms)` sets how long the animation takes in milliseconds.
- `.delay(ms)` sets how long to wait before starting.
- `.ease(easingFunction)` controls the rate of change over time.

Only properties chained **after** `.transition()` are animated; properties chained before it are applied immediately. This means you can set the final state of some attributes instantly while animating others.
![[Pasted image 20260418185941.png]]
![[Pasted image 20260418185956.png]]
D3 provides a rich library of easing functions across four behavioural families. 
1. Linear easing (`d3.easeLinear`) changes at a constant rate. 
2. "In" family (e.g., `d3.easeQuadIn`, `d3.easeCubicIn`) starts slowly and accelerates toward the end.
3. "Out" family (e.g., `d3.easeQuadOut`) starts quickly and decelerates.
4. "InOut" family (e.g., `d3.easeCubicInOut`) starts and ends slowly with faster movement in the middle, giving a polished, natural feel.
5. There is also an expressive family: `d3.easeElastic`, `d3.easeBack`, and `d3.easeBounce`, which overshoot or oscillate past the target value before settling.

![[Pasted image 20260418190133.png]]
For the histogram update with a smooth bar height transition:
```javascript
d3.selectAll("#histogram rect")
    .data(updatedBins)
    .transition()
    .duration(500)
    .ease(d3.easeCubicInOut)
    .attr("y", d => yScale(d.length))
    .attr("height", d => innerHeight - yScale(d.length));
```

### Tooltips
A **tooltip** is a small informational overlay that appears near a data element when the user hovers over it and disappears when the cursor leaves. The standard D3 approach builds the tooltip as an SVG group (`<g>`) appended to the inner chart, containing a styled `<rect>` for the background and a `<text>` element for the label.
![[Pasted image 20260418190140.png]]
The tooltip is built in four steps.
1. First, create the tooltip group in `createTooltip()` and set its initial opacity to 0 so it is invisible but present in the DOM.
2. Second, append a `<rect>` with the desired dimensions, corner radius (via `rx` and `ry`), and fill colour.
3. Third, append a `<text>` element with a placeholder value, centred within the rectangle using `text-anchor: middle` and `alignment-baseline: middle`.
4. Fourth, attach `mouseenter` and `mouseleave` event listeners to each data point element.

On `mouseenter`, the handler does three things:
1. It updates the tooltip text with the hovered data point's value (formatted using `d3.format()`).
2. Reads the circle's `cx` and `cy` attributes from the DOM event target to determine where to position the tooltip.
3. Translates the tooltip group to sit centred above the circle while transitioning opacity to 1. On `mouseleave`, it resets opacity to 0 and translates the tooltip off-screen to prevent hover interference:
```javascript
// mouseenter
d3.select(".tooltip text")
    .text(`${d3.format(".3")(d.ave_temp)}°C`);

const cx = e.target.getAttribute("cx");
const cy = e.target.getAttribute("cy");

d3.select(".tooltip")
    .attr("transform", `translate(${cx - 0.5 * tooltipWidth}, ${cy - 1.5 * tooltipHeight})`)
    .transition()
    .duration(200)
    .style("opacity", 1);

// mouseleave
d3.select(".tooltip")
    .style("opacity", 0)
    .attr("transform", "translate(0, 500)");
```

The tooltip group, its dimensions (`tooltipWidth`, `tooltipHeight`), and the `innerChart` reference must all be accessible in `shared-constants.js` for the interaction functions to use them.

### File Loading Order in index.html
Because later scripts depend on constants declared in earlier ones, script tag order in `index.html` is critical:
1. `d3.v7.min.js` (the D3 library itself)
2. `shared-constants.js` (dimensions, scales, colour constants, filter arrays)
3. `interactions.js` (uses filter arrays and scales)
4. `line-chart.js` or `histogram.js` (chart drawing functions)
5. Any additional visualisation files (e.g., `arcs.js`)
6. `loading-data.js` (triggers all chart and filter calls last, once data is ready)

### Key Takeaways
1. Interactivity transforms a chart from a fixed story into an exploratory tool; filtering, tooltips, and transitions are the three foundational mechanisms.
2. Event listeners attach to D3 selections via `.on("event type", (e, d) => { })`, receiving the DOM event (`e`) and the bound datum (`d`).
3. `.classed()` is the standard way to reflect JS state changes back into the DOM via CSS classes.
4. D3 transitions animate only the attributes chained after `.transition()`; anything chained before is immediate.
5. Tooltips follow a create-then-show-hide pattern: build the SVG group once with opacity 0, then toggle visibility and position on mouse events.
6. Shared state (scales, generators, innerChart reference) must live in `shared-constants.js` so interaction functions in separate files can access it.

**Remember this:** interactivity in D3 is state management: you update JS data or flags, then re-bind those changes to the DOM using D3's selection methods and transitions.

**One analogy to remember it by:** a filter button is like a light switch: clicking it changes a boolean, which D3 then uses to redecorate the whole room.