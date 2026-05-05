## 1. Concept Definition and Context

### What is Data Visualisation?
![[image 33.png|image 33.png]]
**Data visualisation** is the practice of representing data graphically so that patterns, trends, and insights that are difficult to detect in raw numbers become perceptible to the human eye and mind.

In simple terms: it is the translation of data into pictures that let your brain do what it does naturally - spot patterns, compare shapes, and understand stories.

Think of it like a translator. Raw data is a foreign language; visualisation converts it into something your visual system already speaks fluently.

Why it matters: data volumes are growing faster than our ability to read tables and spreadsheets. Good visualisation compresses complexity, supports decision-making, and makes insights communicable to both technical and non-technical audiences. As Stuart Card (pioneering HCI researcher) put it, visualisation is about **external cognition** - using resources outside the mind to boost the cognitive capabilities inside it.

### Where Does Visualisation Fit in the Data Science Pipeline?
![[image 1 9.png|image 1 9.png]]
The typical data science pipeline (Schutt and O'Neil, 2014) runs from raw data collection through processing and cleaning, into exploratory data analysis and modelling, and finally to communicating findings and making decisions.

Visualisation appears at two key stages:
1. **Exploratory Data Analysis (EDA):** used internally by the analyst to understand what the data contains before modelling.
2. **Communicate Findings:** used externally to present insights to decision-makers, stakeholders, or the public.

The unit focuses on the full loop described by Ware (2010): data undergoes transformations, is passed through a graphics engine with visual mappings, reaches a human analyst for visual and cognitive processing, who then manipulates the view or gathers more data - all within a physical and social environment.

---

## 2. Key Principles and Core Theory

### Why Visualisation Over Statistics Alone?
![[image 2 7.png|image 2 7.png]]
**Anscombe's Quartet** is the classic demonstration of why statistics are not enough. Four datasets have nearly identical summary statistics (mean, variance, correlation coefficient, and linear regression line) yet when plotted, they look completely different.

![[image 3 5.png|image 3 5.png]]
One is a clean linear relationship, another is a perfect curve, a third has an outlier pulling the regression line, and a fourth is almost a vertical cluster with one extreme outlier.

The lesson: summary statistics can lie by omission. Visualisation reveals the true shape of data. You must always plot before you trust a number.

This reinforces a key principle introduced in COS10022: **always visualise your data before drawing conclusions from statistics alone.**

### Why Good Visualisation Design Matters
![[image 4 4.png|image 4 4.png]]
Bad visualisations can be actively misleading. The lecture showed a "World of Drugs" pie chart using expanding circle sizes across years - the visual encoding made it nearly impossible to correctly read which market was growing fastest or declining. When the same data was redrawn as a simple line chart, the trends became immediately obvious (Junk Charts, 2012).

The design space for visualisation is enormous. A given dataset can be represented as: parallel coordinates, chord diagrams, choropleth maps, streamgraphs, bubble charts, tree maps, calendar views, and many more. Most of these choices are wrong for any given problem. The goal is not novelty - it is clarity.

---

## 3. Examples and Applications

**Historical Examples**
Visualisation is not a modern invention.
![[image 5 4.png|image 5 4.png]]
**C. R. Minard (1869):** his flow map of Napoleon's Russian campaign (1812-1813) is widely regarded as one of the greatest statistical graphics ever produced. It encodes six variables simultaneously: army size, direction of march, geographic location, temperature, date, and time - all in a single image.

**Modern Classics**
![[image 6 3.png|image 6 3.png]]
**The Guardian's Gun Violence in America (2017):** a modern example of data storytelling using layered choropleth maps. The visualisation started with raw homicide dots distributed randomly, then progressively overlaid poverty rates, education levels, and racial demographics - each layer revealing a deeper structural dimension of the problem.

---

## 4. Munzner's Nested Levels of Design

![[image 7 3.png|image 7 3.png]]
Tamara Munzner (2014) proposed a four-level nested model for thinking about visualisation design. Wrong choices at any level cascade downward to corrupt the entire output.

### Level 1: Domain Situation
The outermost level. Understand the **users**, their **interests**, their **questions**, and their **data**. This requires learning specialised vocabulary, conducting interviews, observation, and literature review. The output is a set of requirements.

In practice, this is the **storytelling** phase - figuring out what story the data can tell and who needs to hear it.

### Level 2: Data/Task Abstraction
Translate the domain-specific problem into domain-independent terms. Instead of "find which Broad Street pump is causing cholera", frame it as "search for a target at an unknown location" or "identify a correlation." Decide what data to use and how to encode or transform it.

This is the **data processing** phase.

### Level 3: Visual Encoding / Interaction Idiom
Determine the visual representation - which chart type, which graphical marks, which colour scales. An **idiom** is a distinct visual approach. This level also includes **interaction idiom**: how users can change, filter, or drill down into what they see.

This maps to choosing **chart types and graphical primitives**.

[https://www.youtube.com/watch?v=UQsgYI6MMr4](https://www.youtube.com/watch?v=UQsgYI6MMr4)

### Level 4: Algorithm
The lowest level - the actual code that implements the visualisation. In this unit, D3.js is the primary tool used for this layer.

|Level|Question|Real-world phase|
|---|---|---|
|Domain situation|Who are the users, what do they need?|Requirements and storytelling|
|Data/task abstraction|What is the task in general terms?|Data processing and encoding|
|Visual encoding/interaction|What chart type and interactions?|Chart and graphical design|
|Algorithm|How do we implement it?|D3 code|

---

## 5. Types of Visualisation by Purpose

The design space is large. The lecture illustrated the following major types:
![[image 9 2.png|image 9 2.png]]
**Parallel Coordinates:** each variable is a vertical axis; each data point is a line crossing all axes. Good for high-dimensional data.

![[image 10 2.png|image 10 2.png]]
**Chord Diagram:** circular layout showing flows or relationships between categories.

![[image 11 2.png|image 11 2.png]]
**Choropleth Map:** geographic regions coloured by a data value. Good for spatial comparisons.

![[image 12 2.png|image 12 2.png]]
**Streamgraph:** stacked, flowing area chart showing changes in composition over time.

![[image 13 2.png|image 13 2.png]]
**Tree Map:** nested rectangles where area encodes a quantitative value within a hierarchy.

![[image 14 2.png|image 14 2.png]]
**Bubble Chart:** scatter plot where a third variable is encoded as circle size.

![[image 15 2.png|image 15 2.png]]
**Calendar View:** data laid out in a calendar grid to reveal temporal patterns.

Most of these are poor choices for most tasks. Choosing correctly requires working through all four of Munzner's levels.

---

## 6. Applications and Benefits

**Exploratory Data Analysis:** visualisation reveals distribution shapes, outliers, missing data, and unexpected clusters before modelling begins. Without it, Anscombe's Quartet shows how you can fit a perfect linear regression to a dataset that is actually a curve.

**Public Health and Policy:** Nightingale and Snow demonstrated that visualisation can change government behaviour and save lives. A well-designed chart communicates what a dense statistical report cannot.

**Investigative Journalism and Storytelling:** The Guardian's gun violence series showed how layered, progressive visualisation can guide a reader through a complex social argument - each added data layer deepening the narrative.

**Business and Finance:** Playfair's original motivation was trade economics. Modern dashboards, equity charts, and market heatmaps all descend from this tradition. Clear visualisation of financial data enables faster and better-informed decisions.

---

## 7. Key Takeaways and Summary

1. **Visualisation extends cognitive capacity** - it offloads pattern recognition to the visual system, which is massively parallel and processes shape, colour, and motion pre-attentively. This is Stuart Card's "external cognition" insight.
2. **Statistics alone are not sufficient** - Anscombe's Quartet shows that four structurally different datasets can share identical summary statistics. Always plot your data.

3. **Design is a search problem** - Munzner's search space diagram shows that the space of possible visualisations is large, most are poor, and good ones are rare. Good design is not about making something pretty; it is about navigating toward the rare solutions that actually work.
4. **The four design levels are nested and ordered** - misidentifying the domain need corrupts every level below it. Start with the users and their questions, not with the chart type.
5. **Visualisation is also a form of rhetoric** - bad or misleading visualisations (like the expanding drug market circles) are common. Studying visualisation teaches you both how to make honest ones and how to critically read dishonest ones.

**Remember this:** A visualisation is not a decoration on top of data - it is a cognitive tool. The goal is always to help the right person answer the right question as quickly and accurately as possible.

**One analogy to remember it by:** A great visualisation is like a good pair of glasses. It does not change what is there - it just lets you see what you could not see before.