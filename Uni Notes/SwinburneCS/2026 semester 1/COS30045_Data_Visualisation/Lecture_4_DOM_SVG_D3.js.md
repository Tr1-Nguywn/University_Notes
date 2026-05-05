## Part 1: The DOM and SVG

### What is the DOM?
![[Pasted image 20260323135658.png]]
The **Document Object Model (DOM)** is a tree-structured representation of an HTML document that the browser constructs at load time.

In simple terms: the DOM is the live version of your HTML that JavaScript can read and change.

Think of it like: a family tree where each HTML tag is a family member - parent elements contain child elements, and siblings sit side by side at the same level.

Why it matters: D3.js works entirely by selecting and manipulating DOM elements. Understanding the DOM is the foundation for understanding how D3 builds visualisations.

### Structure and Manipulation
Elements in the DOM are nested and rendered in the order they appear. You can manipulate three categories of things on any element: 
- **Style** (colours, layout, fonts, borders)
- **Attributes** (IDs, classes, hrefs, src)
- **Properties** (dynamic content like innerHTML, class lists, and event listeners).

### What is SVG?
**SVG (Scalable Vector Graphics)** is a format for describing 2D graphics using mathematical expressions rather than pixel grids.

In simple terms: instead of storing a picture as a grid of coloured pixels, SVG stores instructions for drawing shapes - so it scales to any size without blurring.

Think of it like: the difference between a PDF and a JPEG. The PDF is always sharp; the JPEG gets blocky when you zoom in.

Why it matters: D3 draws all its charts as SVG elements inside the DOM, which means they are selectable, styleable, and interactive by default.

### SVG Setup
All SVG shapes live inside a parent `<svg>` container. The container defines a coordinate system with the origin (0, 0) at the top-left, x increasing rightward, and y increasing downward - opposite to standard maths convention.

```html
<svg viewBox="0 0 900 300" style="border: 1px solid black;"></svg>
```

Using `viewBox` instead of fixed `width`/`height` makes the SVG responsive - it scales with its container. Wrapping it in a `div` with `width: 100%; max-width: 1200px` centres it and constrains it on wide screens.

Any SVG element with coordinates outside the viewBox boundary will not be displayed.

### SVG Shapes
The core shapes used in data visualisation:

**Rectangle** - positioned by its top-left corner (x, y), then sized by width and height.
![[Pasted image 20260323141003.png]]
```html
<rect x="260" y="25" width="120" height="60" fill="#6ba5d7" />
```

**Circle and Ellipse** - positioned by centre (cx, cy) with radius r (or rx/ry for ellipse).
![[Pasted image 20260323140728.png]]
```html
<circle cx="530" cy="80" r="50" fill="none" stroke="#81c21c" stroke-width="3" />
<ellipse cx="530" cy="205" rx="50" ry="30" fill="#81c21c" />
```


**Line** - drawn from (x1, y1) to (x2, y2). Lines have no fill; they require a stroke.
![[Pasted image 20260323140441.png]]
```html
<line x1="-20" y1="45" x2="140" y2="225" stroke="black" />
```

**Path** - the most flexible shape. Uses command codes like:
- M = moveto (move from one point to another point)
- L = lineto (create a line)
- H = horizontal lineto (create a horizontal line)
- V = vertical lineto (create a vertical line)
- C = curveto (create a curve)
- S = smooth curveto (create a smooth curve)
- Q = quadratic Bézier curve (create a quadratic Bézier curve)
- T = smooth quadratic Bézier curveto (create a smooth quadratic Bézier curve)
- A = elliptical Arc (create a elliptical arc)
- Z = closepath (close the path)
![[Pasted image 20260323140822.png]]
```html
<path d="M680 150 C 710 80, 725 80, 755 150 S 810 220, 840 150"
      fill="none" stroke="#773b9a" stroke-width="3" />
```

**Text** - positioned at (x, y) where y is the text baseline, not the top of the letter. Use `text-anchor` to control alignment: `start`, `middle`, or `end`.
![[Pasted image 20260323140836.png]]
```html
<text x="380" y="155" text-anchor="middle">label</text>
```

**Grouping with `<g>`** - groups elements so you can move or style them together. The `transform="translate(x, y)"` attribute shifts all children relative to the group's origin.
![[Pasted image 20260323140935.png]]
```html
<g transform="translate(260, 175)">
  <rect x="0" y="0" width="60" height="60" />
  <text x="0" y="85">rect</text>
</g>
```

### Styling Priority
When multiple styling sources conflict, inline `.style()` wins over CSS stylesheets, which win over `.attr("fill", ...)`. This order matters when D3 applies both attributes and inline styles to the same element.

### Key Takeaways
1. The DOM is a live tree the browser builds from HTML; D3 reads and writes to this tree.
2. SVG uses a top-left origin coordinate system where y increases downward.
3. Elements outside the SVG viewBox are clipped and not rendered.
4. Grouping with `<g>` and `transform="translate()"` is central to D3's layout strategy.

**Remember this:** SVG is just HTML for shapes - it lives in the DOM and can be manipulated like any other element.

**One analogy to remember it by:** The SVG canvas is like a whiteboard in a coordinate grid - you place shapes by telling it where to start and how big to draw, and the grid origin is always the top-left corner.

---

## Part 2: Introduction to D3.js

### What is D3.js?
![[Pasted image 20260323141124.png]]
**D3.js (Data-Driven Documents)** is an open-source JavaScript library for binding data to DOM elements and translating that data into visual attributes.

In simple terms: D3 lets you say "for each row in my dataset, draw a rectangle whose width equals the data value."

Think of it like: a translator between your spreadsheet and your SVG canvas - it converts numbers into pixels.

Why it matters: D3 is the industry standard for custom, interactive, web-based data visualisations used in journalism, business intelligence, and research. It is not a charting library - it gives you low-level control to build anything.

### What D3 is Not
D3 is not a drag-and-drop charting tool. You cannot tell it to "make a bar chart." You build every element individually. This makes it verbose but infinitely flexible. Tools like Highcharts or Tableau abstract this away; D3 exposes it fully.

D3 is also not suitable for data exploration, processing, or analysis - use Python or R for that. Work out your story first, then build it in D3.

With that said, D3 can be an great tool nonetheless:
![[Pasted image 20260323142015.png]]
vs
![[Pasted image 20260323142040.png]]
### Project Setup
![[Pasted image 20260323141444.png]]
A D3 project has a standard folder structure: `index.html`, a `css/` folder, a `data/` folder (for CSV files), and a `js/main.js` file. Load the D3 library and your script at the end of `<body>` to reduce page load time.
```html
<script src="https://d3js.org/d3.v7.min.js"></script>
<script src="js/main.js"></script>
```

Create a responsive SVG container in `index.html`:
```html
<div class="responsive-svg-container"></div>
```

In CSS:
```css
.responsive-svg-container {
  margin-right: auto;
  margin-left: auto;
  width: 100%;
  max-width: 1200px;
}
```

Then create the SVG canvas in `main.js`:
```javascript
const svg = d3.select(".responsive-svg-container")
  .append("svg")
  .attr("viewBox", "0 0 1200 1600")
  .style("border", "1px solid black");
```

Note that `viewBox` is camelCase in D3's `.attr()` method.

### Selecting Elements
D3 uses CSS selector syntax to grab DOM elements.
```javascript
d3.select("h1")           // first matching element
d3.selectAll("circle")    // all matching elements
d3.select("#my-id")       // by ID
d3.select(".my-class")    // by class
```
![[Pasted image 20260323141822.png]]
Selections are then chained with methods to modify style, attributes, or append children.
![[Pasted image 20260323141729.png]]

### Appending and Modifying
Use `.append()` to add child elements to a selection, then chain `.attr()` and `.style()` to define their properties.
```javascript
d3.select("svg")
  .append("rect")
  .attr("x", 10)
  .attr("y", 10)
  .attr("width", 414)
  .attr("height", 16)
  .attr("fill", "turquoise");
```
![[Pasted image 20260323142134.png]]
`.style()` sets inline CSS and takes priority over `.attr()` and external stylesheets.

### Key Takeaways
1. D3 is a DOM manipulation library, not a charting library - you assemble charts element by element.
2. Load D3 and your scripts at the bottom of `<body>` to avoid blocking page rendering.
3. `d3.select()` grabs the first match; `d3.selectAll()` grabs all matches.
4. Chain `.attr()` for SVG attributes and `.style()` for CSS inline styles.

**Remember this:** D3 is a vocabulary for translating data into DOM elements - you write the grammar, it speaks to the browser.

**One analogy to remember it by:** D3 is like a mail merge for SVG - you have a template (shape), a dataset (rows), and D3 stamps one copy of the template for each row.

---

## Part 3: Loading Data and Building Charts

### The D3 Data Workflow
The full D3 pipeline has five steps: Find, Load, Format/Measure, Bind, Scale. Each step in the workflow feeds the next.
![[Pasted image 20260323142219.png]]

| Step        | What happens                                  |
| ----------- | --------------------------------------------- |
| 1. Find     | Locate a suitable dataset                     |
| 2. Load     | Fetch the file with a D3 load function        |
| 3a. Format  | Convert string values to numbers, parse dates |
| 3b. Measure | Calculate max, min, length for scaling        |
| 4. Bind     | Join each data row to a DOM element           |
| 5. Scale    | Map data values to pixel dimensions           |

### Loading CSV Data
D3 uses `d3.csv()` to load CSV files. Because fetching a file is asynchronous, the code that uses the data must live inside the `.then()` callback.
```javascript
d3.csv("../data/VegetationHab.csv", d => {
  return {
    habitat: d.habitat,
    sighting: +d.sighting  // "+" converts string to number
  };
}).then(data => {
  console.log(data.length);
  console.log(d3.max(data, d => d.sighting));
  data.sort((a, b) => b.sighting - a.sighting);
  drawBarChart(data);
});
```

Key points: the file path must be correct relative to the HTML file, column names must exactly match the CSV headers, and CSV values are always loaded as strings - use `+` to coerce to numbers. You need VS Code's Live Server extension to serve files locally (browsers block file:// fetch requests).

Pass the cleaned data to a separate `drawBarChart(data)` function rather than building the chart inside `.then()`. This keeps concerns separated.

### Arrow Function Syntax
D3 uses arrow functions extensively. The rules:
```javascript
// Single parameter, single expression - no parens, no braces
.attr("width", d => d.sighting)

// Multiple parameters - wrap in parens
.attr("y", (d, i) => (barHeight + 5) * i)

// Multi-line body - use braces and explicit return
.attr("class", d => {
  console.log(d);
  return `bar bar-${d.sighting}`;
})
```

Template literals (backticks) let you embed expressions with `${}` - much cleaner than string concatenation.

### Binding Data to the DOM
The modern D3 pattern for binding data is `.selectAll().data().join()`:
![[Pasted image 20260323142411.png]]
```javascript
svg
  .selectAll("rect")   // empty selection (nothing exists yet)
  .data(data)          // pair each row to a (future) rect
  .join("rect")        // create one rect per row, add to DOM
```
The old pattern used `.enter().append()` - you may see this in older code, but `.join()` is preferred in D3 v5+.

### Building a Bar Chart
Once data is bound, set attributes using accessor functions. The second argument `i` gives the element's index in the dataset.
```javascript
const drawBarChart = data => {
  const barHeight = 20;

  svg
    .selectAll("rect")
    .data(data)
    .join("rect")
    .attr("class", d => `bar bar-${d.sighting}`)
    .attr("width", d => d.sighting)     // raw value - needs scaling later
    .attr("height", barHeight)
    .attr("x", 0)
    .attr("y", (d, i) => (barHeight + 5) * i)
    .attr("fill", d => d.habitat === "Rainforest and related scrub" ? "blue" : "green");
};
```
The ternary operator `condition ? valueIfTrue : valueIfFalse` is the idiomatic way to conditionally set attributes in D3.

Without scaling, the bar widths equal the raw data values in pixels. A sighting count of 1673 would extend 1673px wide - off the visible SVG. This is what Part 4 solves.

### Key Takeaways
1. `d3.csv()` is asynchronous - all chart code must be inside `.then()`.
2. Use `+d.fieldName` to convert CSV strings to numbers during the row conversion function.
3. The `.selectAll().data().join()` pattern is D3's core data-binding idiom.
4. Raw data values as pixel widths only work if the data range fits within the SVG - otherwise scale.

**Remember this:** The `.then()` callback is a promise being fulfilled - D3 is saying "once I have the data, then do this."

**One analogy to remember it by:** Binding data to DOM elements is like assigning a seat to each passenger on a plane - `.data()` holds the passenger list, `.join("rect")` hands out the seats.

---

## Part 4: Scaling and Labelling

### Why Scaling Matters
Raw data values used directly as pixel coordinates will overflow the SVG when data ranges are large. A dataset where the max value is 1673 and the min is 19 cannot be displayed meaningfully as raw pixel widths inside a 1000px canvas. Scaling maps the data domain proportionally to the pixel range.

### Linear Scale
**`d3.scaleLinear()`** maps a continuous numeric domain (data values) to a continuous pixel range.
```javascript
const xScale = d3.scaleLinear()
  .domain([0, 1620])   // input: data min and max
  .range([0, 1000]);   // output: pixel min and max

// Use it as a function in .attr()
.attr("width", d => xScale(d.sighting))
```

The scale is just a function. Passing a domain value returns its proportional pixel position. A value of 810 (halfway through the domain) returns 500 (halfway through the range).

### Band Scale
**`d3.scaleBand()`** maps a discrete list of categories to evenly divided bands across a pixel range. This replaces the manual `(barHeight + 5) * i` calculation for positioning bars on the y-axis.
```javascript
const yScale = d3.scaleBand()
  .domain(data.map(d => d.habitat))  // array of category labels
  .range([0, 500])                   // total pixel height
  .padding(0.1);                     // 10% gap between bands
```

`yScale("Rainforest and related scrub")` returns the y pixel position for that band. `yScale.bandwidth()` returns the computed height of each band (accounting for padding) - use this for bar height.
```javascript
.attr("y", d => yScale(d.habitat))
.attr("height", yScale.bandwidth())
```

### Adding Labels with Groups
To attach text labels to each bar, group the rectangle and its label together using `<g>` elements. This way you can move the whole group (bar + label) together.
```javascript
const barAndLabel = svg
  .selectAll("g")
  .data(data)
  .join("g")
  .attr("transform", d => `translate(0, ${yScale(d.habitat)})`);
```
Each `<g>` is translated to its bar's y position. Within each group, coordinates start at (0, 0) relative to the group origin - so `y="0"` inside the group means "top of this bar's row."

Then append the rectangle and text as children of each group:
```javascript
// Rectangle
barAndLabel
  .append("rect")
  .attr("width", d => xScale(d.sighting))
  .attr("height", yScale.bandwidth())
  .attr("x", 250)
  .attr("y", 0)
  .attr("fill", d => d.habitat === "Rainforest and related scrub" ? "blue" : "green");

// Category label (left of bar)
barAndLabel
  .append("text")
  .text(d => d.habitat)
  .attr("x", 240)
  .attr("y", 33)
  .attr("text-anchor", "end")
  .style("font-family", "sans-serif")
  .style("font-size", "13px");

// Value label (right of bar)
barAndLabel
  .append("text")
  .text(d => d.sighting)
  .attr("x", d => 250 + xScale(d.sighting) + 4)
  .attr("y", 33)
  .style("font-family", "sans-serif")
  .style("font-size", "13px");
```
The value label's x position is `barStartX + scaledBarWidth + 4` - placing it just to the right of the bar's end.

### Scale Comparison

|Scale|Use case|Domain|Range|
|---|---|---|---|
|`scaleLinear()`|Continuous numeric data|`[min, max]` numbers|`[pxMin, pxMax]`|
|`scaleBand()`|Discrete categories|Array of strings|`[pxMin, pxMax]`|

### Key Takeaways
1. `scaleLinear()` converts numeric data values to proportional pixel positions within a defined range.
2. `scaleBand()` evenly distributes categorical labels across a pixel range, with optional padding between bands.
3. `yScale.bandwidth()` computes bar height automatically from the number of categories and available space.
4. Grouping with `<g>` and `translate()` keeps bar and label coordinates relative to each other rather than absolute to the SVG.

**Remember this:** A D3 scale is just a function - it takes a data value in, and returns a pixel value out.

**One analogy to remember it by:** `scaleLinear()` is like a ruler - you stretch it to fit your data on one end and your canvas on the other, then read off where any value lands.