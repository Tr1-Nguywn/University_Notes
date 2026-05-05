## Part 1: Common Chart Types and Their Use

### What is a Chart Type?
A **chart type** is a specific visual form used to represent data, chosen based on the nature of the data and the story you want to tell.

In simple terms: it's the "shape" of your visualisation - bar, line, scatter, pie, etc.

Think of it like: choosing the right tool for a job. A hammer works for nails, not screws. Similarly, a line chart works for time-series data, not categorical comparisons.

Why it matters: using the wrong chart type can mislead your audience, hide patterns, or make the data harder to understand than raw numbers.

### Overview of Common Chart Types
![[Pasted image 20260503232746.png]]
![[Pasted image 20260503232834.png]]

|Chart Type|Primary Use|Data Type|
|---|---|---|
|Simple text|Single key number|Any|
|Table|Multi-attribute comparison|Mixed|
|Heatmap|Table with relative magnitude cues|Quantitative|
|Scatter plot|Relationship between two variables|Quantitative|
|Line chart|Trends over continuous data|Continuous|
|Stacked line / area chart|Cumulative trends across series|Continuous|
|Slopegraph|Change between two time points|Ordinal/Quantitative|
|Vertical bar|Categorical comparison|Categorical + Quantitative|
|Horizontal bar|Categorical comparison, long labels|Categorical + Quantitative|
|Stacked bar|Part-to-whole within categories|Categorical + Quantitative|
|Proportional stacked bar|Relative proportions across categories|Categorical + Quantitative|
|Area / square area|Large-magnitude comparisons|Quantitative|
|Pie / donut chart|Part-to-whole (limited use)|Categorical + Quantitative|
|Waterfall|Sequential additive/subtractive changes|Quantitative|

### Key Chart Guidelines
![[image 1 10.png]]
**Text:** Use plain numbers for one or two key figures. A large "20%" is more impactful than a two-bar chart. Avoid when multiple values need simultaneous comparison.

![[image 2 8.png|image 2 8.png]]
**Tables:** Use verbal/reading processing rather than visual. Good for audiences needing to look up specific values or when mixing measurement levels. Use light borders or white space, not heavy lines.

![[image 3 6.png|image 3 6.png]]
**Heatmap:** A table enhanced with colour saturation to encode relative magnitude. Always include a legend.

![[image 4 5.png|image 4 5.png]]
**Scatter Plot:** Both axes quantitative. Annotate key points, use contrast to highlight subsets. Good for correlation and outliers; not for categorical axes.

![[image 5 5.png|image 5 5.png]]
**Line Chart:** Only connect continuous data. Nominal/categorical data on the x-axis should never be connected with lines. Beyond two series, consider small multiples.

![[image 6 4.png|image 6 4.png]]
**Bar Charts:** Critical rule: always start from zero. Bars should be wider than the white space between them. Stacked bars make only the bottom segment easy to compare across categories.

![[image 7 4.png|image 7 4.png]]
**Pie / Donut Charts:** Encode values as angles and areas - both are low-accuracy channels. Never use 3D pie charts. A horizontal bar chart almost always communicates more clearly.

![[b17b75b5-d4f0-4186-911a-59a043fbf097.png]]
**Secondary Axis:** Implies a relationship that may not exist and confuses which series belongs to which axis. Prefer direct labelling or stacked separate charts.

---

## Part 2: Visual Variables - Marks and Channels

### What are Marks and Channels?
![[image 8 3.png|image 8 3.png]]
**Marks** are the geometric/graphic primitives used to represent data items - the "what" you draw.

![[image 9 3.png|image 9 3.png]]
**Channels** are the visual properties that control how a mark looks - the "how" you style it to encode information.

Think of it like: marks are the pieces on a chessboard and channels are their properties (colour, size, shape) that tell you whose piece it is and how valuable it is.

Why it matters: mismatching channels and data types systematically misleads readers.

### Historical Context: Jacques Bertin
![[image 10 3.png|image 10 3.png]]
Jacques Bertin (1918-2010) was a French cartographer who developed the first systematic theory of visual encoding in his 1967 book _Semiology of Graphics_.

### Marks
- **Points:** 0-dimensional. Dots in a scatter plot.
- **Lines:** 1-dimensional. Connections, trends.
- **Areas:** 2-dimensional. Bars, map regions, treemap tiles.

![[image 11 3.png|image 11 3.png]]
![[image 12 3.png|image 12 3.png]]
For links in network data: **connection** (line mark) for pairwise relationships; **containment** (area mark) for group membership.

### Channels
![[image 13 3.png|image 13 3.png]]
**Magnitude channels** (ordered/quantitative data), ranked most to least effective:
1. Position on common scale
2. Position on unaligned scale
3. Length (1D size)
4. Tilt / angle
5. Area (2D size)
6. Depth (3D position)
7. Colour luminance
8. Colour saturation
9. Curvature
10. Volume (3D size) - least effective

**Identity channels** (categorical/unordered data):
1. Spatial region
2. Colour hue
3. Motion
4. Shape

**Expressiveness:** do not use magnitude channels for categorical data - using colour saturation to distinguish fruit types implies a nonexistent ordering.

**Effectiveness:** the most important attribute should use the most salient/noticeable channel.

### Stevens' Psychophysical Power Law
![[image 14 3.png|image 14 3.png]]
- Electric shock (exponent ~3.5): overestimated.
- Length (exponent 1.0): perceived accurately.
- Brightness (exponent ~0.5): underestimated.
- Depth (exponent ~0.67): underestimated.

**Examples:**
![[image 15 3.png|image 15 3.png|518]]

![[image 16 2.png|image 16 2.png]]

![[image 17 2.png|image 17 2.png]]

![[image 18 2.png|image 18 2.png]]

![[image 19 2.png|image 19 2.png]]

![[image 20 2.png|image 20 2.png]]
Brightness and depth are poor channels for precise* quantitative differences compare to other methods.

### Area vs Radius Coding
- **Radius coding:** doubling the value doubles the radius but quadruples the area (Area = πr²) - exaggerates differences.
- **Area coding:** doubling the value doubles the area, radius only grows by √2 - perceptually correct.

Design rule: always scale bubble area to the data value, never the radius.

### Lie Factor
Lie Factor = (size of effect in graphic) / (size of effect in data)

A value of 1.0 is honest. The most common cause of a high Lie Factor is a non-zero baseline on a bar chart.

---

## Part 3: Design Guidelines - Avoiding Unjustified 3D

### What is Unjustified 3D?
**Unjustified 3D** is the use of three-dimensional visual effects when the data has no meaningful third dimension - adding depth purely for aesthetic reasons.

Why it matters: 3D corrupts every channel simultaneously - position, size, colour, and legibility all degrade at once.

**Example:**
![[image 21 2.png|image 21 2.png]]
Think of it like: printing a map on a crumpled piece of paper. The geography is still technically there, but the surface distortions make distances impossible to judge correctly.

### The Five Problems with 3D
1. **Depth judgment is poor.** Steven's Power Law gives depth an exponent of ~0.67 - viewers systematically underestimate depth-encoded values.
2. **Occlusion.** Objects closer to the viewer block those behind them and the viewpoint is fixed.
3. **Perspective distortion.** Objects further away appear smaller even when they represent the same value - corrupts the position channel.
4. **Colour degraded by lighting.** Simulated lighting renders the same colour at different brightnesses on different faces, implying false magnitude differences.
5. **Tilted text is illegible.** Labels follow the perspective angle and become hard to read.

3D is justified only for genuinely volumetric scientific data (e.g., medical imaging, fluid dynamics) - never for business, survey, or time-series data.

---

## Part 4: Design Guidelines - Animation

### What is Animation in Data Visualisation?
**Animation** uses moving, time-evolving visuals to show how data changes across a dimension rather than showing all states simultaneously in a static chart.

Think of it like: watching a city time-lapse versus looking at a grid of decade photos. The time-lapse is engaging but the grid lets you compare 1960 and 2000 directly without relying on memory.

### The Core Problem: Eyes Beat Memory
Comparing two states visually side-by-side is far more accurate than watching one disappear and remembering it while the next plays. Human working memory holds roughly 3-4 chunks at once.

**Change blindness** is the perceptual phenomenon where viewers fail to notice changes occurring during a visual transition - meaning animation can cause the very changes you want to communicate to be missed.

### Animation vs Alternatives
![[image 22 2.png|image 22 2.png]]

|Situation|Recommendation|
|---|---|
|Comparing a small number of states|Side-by-side or small multiples|
|Showing a trend over many time points|Line chart or small multiples|
|Change across hundreds of frames|Animation may be appropriate|
|Spatial movement is the message|Animation appropriate|
|Audience needs to look up specific values|Static chart|

![[image 23 2.png|image 23 2.png]]
**Small multiples:** the same chart design repeated across a grid, each panel showing a different subset. All panels must use an identical visual structure so comparisons require only scanning, not relearning.

---

## Part 5: Bubble Charts - Design Critique

### What is a Bubble Chart?
![[image 24 2.png|image 24 2.png]]
A **bubble chart** is a scatter plot variant where circle size encodes a third quantitative variable. The x-axis, y-axis, and bubble size all carry data.

Think of it like: a scatter plot with a third dimension added via area - which is a low-accuracy channel.

Radius-coded bubbles make differences appear four times larger than they actually are. Always scale bubble area to the data value, never the radius. Most charting tools default to radius coding and must be corrected manually.

### Design Critique Framework (Munzner)
- **Who is the audience?** Expert vs non-expert determines tolerable complexity.
- **What question does this visualisation answer?** A chart without a clear question lacks focus.
- **What design principles describe why it is good or bad?** Connect judgements to nameable principles: channel effectiveness, Lie Factor, occlusion, baseline rules, etc.
- **What improvements would you suggest?** Constructive critique, not just diagnosis.

Bubble charts are appropriate when you genuinely need three simultaneous quantitative variables and their relationship is the message. They are inappropriate for precise magnitude comparison (use a bar chart).

---

## Part 6: Tufte's Integrity Principles

### What are Integrity Principles?
**Tufte's integrity principles** are a set of rules for ensuring that a visualisation honestly represents its underlying data - that the visual impression matches the actual numbers.

In simple terms: the graphic should not lie, mislead, or let decoration corrupt the data signal.

Think of it like: a weighing scale must show your actual weight, not a number the scale manufacturer thinks will make you feel better. The display should faithfully encode reality.

Why it matters: even technically accurate charts can be deeply misleading through distortion, selective framing, or design choices that amplify differences far beyond what the data supports.

### The Four Integrity Principles
1. **Clear, detailed, and thorough labelling** to prevent graphical distortion and ambiguity.
2. **Size of the physically measured graphic should be directly proportional to the numerical quantities** (the Lie Factor rule).
3. **Show data variation, not design variation** - visual changes should reflect data changes, not stylistic choices.
4. **Graphics must not quote data out of context** - cherry-picking a time window or axis range to support a narrative is a violation.

### Principle 1: Clear Labelling
![[image 25 2.png|image 25 2.png]]
Labels must be sufficient to prevent misinterpretation. Axes without units, unlabelled series, and missing baselines all open the door to graphical distortion. A bar chart without a zero baseline is not just poorly designed - it is misleading by omission.

### Principle 2: The Lie Factor
Lie Factor = (size of effect shown in graphic) / (size of effect in data)

A Lie Factor of 1.0 is honest. Values well above 1.0 exaggerate differences; values well below 1.0 understate them.

![[image 26 2.png|image 26 2.png]]
**Fuel economy example (Tufte):** The line representing 18 mpg in 1978 was 0.6 inches long; the line for 27.5 mpg in 1985 was 5.3 inches long. Data effect = (27.5 - 18) / 18 = 53%. Graphic effect = (5.3 - 0.6) / 0.6 = 783%. Lie Factor ≈ 14.8.

![[image 27 2.png|image 27 2.png]]
**Rule: bar charts must always start at zero.** The relative size of bars is what viewers judge. Starting anywhere other than zero destroys that ratio.

### Principle 3: Show Data Variation, Not Design Variation
Manipulating axis intervals, using unequal spacing between time points, or selecting "random" quarters rather than consistent intervals are all ways to make a data trend look steeper or flatter than it is.

![[image 28 2.png|image 28 2.png]]
**Fox News job loss chart:** The x-axis used Dec '07, Sep '08, Mar '09, Jun '10 - gaps of 9 months, 6 months, and 15 months. Equal spacing implied a constant rate of change that did not exist. The corrected chart with every month plotted shows a very different pattern.

![[image 29 2.png|image 29 2.png]]
**Gas price chart:** Showing only three cherry-picked data points (last year, last week, current) on a non-zero axis made a modest ~13% price increase look alarming. The broader 12-month context showed the current price was actually below its recent peak.

### Principle 4: No Out-of-Context Data
Cherry-picking the time window is one of the most common integrity violations.

![[image 30 2.png|image 30 2.png]]
**Temperature data example:** The Daily Mail plotted 15 years of global average temperature (1997-2012) on a narrow y-axis, making the trend appear flat or even declining. Plotting the same 50-year dataset (1961-2012) reveals a clear upward trend. The 15-year window was not randomly chosen - it began from an anomalously hot El Niño year, guaranteeing a flat-looking local segment of a longer upward trend.

### 3D Pie Charts and Integrity
3D pie charts introduce two simultaneous integrity violations: perspective projection makes front slices appear larger than back slices even when the underlying proportions are equal, and the tilt of the chart face makes area comparisons impossible.

**Example:**
![[55660ea6-6cfb-4fae-b540-8c25c35055a9.png]]
vs
![[image 31 2.png|image 31 2.png]]
**Apple vs RIM smartphone marketshare example:** In the 3D version, Apple's 19.5% slice (at the front) appeared nearly as large as RIM's 39.0% slice (at the back). The flat 2D version immediately revealed that RIM had roughly double Apple's share.

**Pie chart problems generally:**
- Hard to judge differences between similar-sized slices.
- Impossible to compare two pie charts side by side without relying on the printed percentages.
- Ordering slices (largest to smallest, clockwise from 12 o'clock) helps but does not fix the underlying angle/area perception problem.
- A horizontal bar chart of the same data almost always communicates more clearly.

**When pie charts are acceptable:** when there are very few categories (ideally 2-3), when the key message is simply "most of this" vs "a small slice", or when the audience is non-technical and the proportions are obvious.
![[image 32 2.png|image 32 2.png]]

**When to use a single number instead:** if the important insight is a single figure ("68% of kids expressed interest in science after the program, up from 44%"), just display that number prominently. It communicates more directly than a pie chart of five sentiment categories.
![[image 33 2.png|image 33 2.png]]

### Key Takeaways for Part 6
1. The Lie Factor quantifies dishonesty: graphic effect / data effect. Target 1.0.
2. Bar charts must always start at zero - the ratio of bar heights is what viewers read.
3. Non-zero y-axis on a line chart amplifies noise and suppresses the true signal level.
4. Cherry-picking a time window can reverse the apparent direction of a trend.
5. 3D pie charts violate both the Lie Factor and the "show data variation, not design variation" principles simultaneously.

One analogy to remember: integrity principles are like the rules for a weighing scale. The scale must start at zero, the unit increments must be equal, and you cannot hide the fact that you measured on a Monday morning before breakfast to get a more flattering reading.

---

## Part 7: Tufte's Design Principles

### What are Design Principles?
**Tufte's design principles** are guidelines for maximising the signal-to-noise ratio in a visualisation - ensuring every visual element earns its place by communicating data, not decoration.

In simple terms: remove everything that does not carry information. If you can delete an element without losing any data, delete it.

Think of it like: editing prose. Good writing removes redundant words until every word carries weight. Good visualisation removes redundant ink until every pixel carries information.

Why it matters: visual clutter forces the reader to mentally filter out noise before they can see the signal - adding cognitive load and reducing accuracy.

### The Four Design Principles
1. Maximise data-ink ratio
2. Avoid chart junk
3. Aim for high data density
4. Layering and separation of information

### Principle 1: Maximise Data-Ink Ratio
Data-Ink Ratio = data-ink / total ink used in graphic

**Data-ink** is ink that directly encodes a data value and cannot be removed without losing information. Everything else is non-data-ink.

The goal is to maximise this ratio - approaching 1.0, where every pixel communicates data.

**Step-by-step bar chart cleanup (tbray.org example):**
![[image 34 2.png|image 34 2.png]]
- Start: 9-bar chart with heavy grid, coloured background, bounding box, vertical grid lines, heavy horizontal ticks, baseline.
- Remove the vertical grid lines -> cleaner.
![[image 35.png]]
- Remove the coloured background -> cleaner.
- Remove the bounding box -> cleaner.
- Replace horizontal ticks with short gap cuts in the bars at each scale value ("range frame") -> every mark now communicates scale.
- Remove the baseline -> every remaining pixel encodes data.
![[image 36.png]]
- Final result: same 9 data points, far less ink, no information lost.

A 3D grouped bar chart is the opposite: simulated depth, lighting effects, perspective foreshortening, and rounded cylinder shapes all add ink that encodes nothing.
![[image 37.png]]

### Principle 2: Avoid Chart Junk
![[image 38.png]]
**Chart junk** is any extraneous visual element that distracts from the data message without adding information.

Common forms of chart junk include: grid lines heavier than necessary, coloured backgrounds, decorative fills, 3D effects, clip art embedded in charts, drop shadows, and redundant labels.

![[image 39.png]]
**The Onion parody:** cylindrical 3D bars with a small pie chart on top of each cylinder - every element is chart junk. The cylinders add depth that encodes nothing; the pies add angle encoding for data already encoded by height; the combined visual is nearly unreadable.

### Principle 3: High Data Density
Data Density = number of data items / area of data graphic

High data density is a goal, but must be balanced against legibility. Tufte's contrast examples:

![[image 40.png]]
- A simple two-bar chart covering 26.5 square inches with 4 data entries = **0.15 numbers per square inch** - "thin indeed."
- New York City weather history for 1980 in a similar area: daily high, daily low, normal high, normal low, precipitation, relative humidity, all encoded = **181 numbers per square inch** - highly dense and still readable due to good layering.

Other high-density examples: telephone books, the Genealogy of Pop/Rock Music timeline, the Google Music Timeline stacked area chart, Voronoi airport diagrams.

![[image 41.png]]
High density is achieved through small multiples, layering multiple variables on shared axes, and using space-efficient encodings.

### Principle 4: Layering and Separation
![[image 42.png]]
**Josef Albers' principle: 1 + 1 = 3.** When two elements are placed near each other, a third visual element is created - the space between them. If that negative space creates visual noise (implied lines, phantom shapes), it adds clutter even though nothing was drawn.

The goal is to layer information so that each layer is perceptually distinct and the layers do not interfere with each other.

![[image 43.png]]
**Train timetable redesign (Tufte):** The original used a full grid with heavy borders around every cell. The redesign used leader dots between station names and departure times, with bold type for major stops - same information, far less visual noise, each element clearly separated.

![[image 44.png]]
**Google Maps vs Austrian street map:** Google Maps uses layering - roads, labels, green spaces, and points of interest are visually separated by weight, colour, and scale so each can be read independently. The Austrian map packs all layers at equal weight, creating visual competition where no single layer can be extracted cleanly.

![[image 45.png]]
**Star Wars speech timeline (E. Gabasova):** Characters plotted as horizontal rows, speech events as marks along a timeline, size encoding speech volume - multiple information layers (who, when, how much) are visually separated so each is readable independently.

### Summary of Tufte's Design Principles

|Principle|Formula|Common Violation|
|---|---|---|
|Maximise data-ink ratio|data-ink / total ink|3D effects, heavy grids, decorative fills|
|Avoid chart junk|N/A|Clip art, backgrounds, unnecessary borders|
|High data density|data items / graphic area|Single-value charts, oversized whitespace|
|Layering and separation|N/A|Equal-weight elements competing visually|

### Key Takeaways for Part 7
1. Every pixel should earn its place - if removing an element loses no data, remove it.
2. Chart junk is not neutral - it actively increases cognitive load by forcing readers to filter noise.
3. High data density is a virtue, but only when layers are well-separated and legible.
4. Negative space is not free - adjacent elements create a perceived third element between them.
5. The data-ink ratio is a useful diagnostic: ask whether each visual element is encoding data or just filling space.

One analogy to remember: Tufte's design principles are like interior minimalism. A well-designed room has only furniture that serves a purpose. Remove the unnecessary, and the objects that remain communicate their function clearly. Add decorative clutter, and the room becomes hard to navigate.