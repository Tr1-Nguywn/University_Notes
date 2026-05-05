## Part 1: Sensation, Perception, and Memory

### What is Perception
**Sensation** is receiving physical stimulation and encoding it into the nervous system.
**Perception** is the process of interpreting and recognising that sensory information, it's the act of understanding what the sensation was.

In simple terms: sensation is the raw signal; perception is the brain's interpretation of it.

Think of it like: a camera sensor captures light (sensation), but the image processing software decides what that light means (perception).

Why it matters: perception is not a faithful recording of reality. It is shaped by past experience, current context, and future goals. This means the same chart can be read differently by different people, and design choices must account for how the brain distorts raw input.

### Perception is Constructive, Not Objective
The brain does not receive a perfect picture of the world. Visual perception is an active process of inference and prediction. The classic table-top illusion from Johnson (2013) illustrates this well: 
![[Pasted image 20260426235651.png]]
Two tabletop surfaces are objectively identical in shape and size, yet the brain perceives them as different because it applies learned rules about perspective and depth. We literally see with our brains, not our eyes.

This constructive nature of vision has three key biases:
- Our past experience primes us to expect familiar patterns
- The current context warps how we interpret what is in front of us
- Our future goals shift attention toward information relevant to what we are trying to accomplish.

### The Three-Stage Visual System
![[Pasted image 20260427000950.png]]
Ware (2010) describes vision as a three-stage pipeline.
In the first stage, **features** (colour, orientation, edge, motion) are processed in parallel across the entire visual field simultaneously. Millions of features are processed at once without conscious effort.
In the second stage, **patterns** are assembled from those features, tuned by attention, which reinforces the most relevant patterns and suppresses noise.
In the third stage, a small number of **objects** (typically one to three) are held in visual working memory, where they can be associated with non-visual meaning like labels and categories.

This pipeline has a direct implication for chart design: anything that can be picked up at the feature stage (colour, size, orientation) is processed essentially for free, while anything requiring the pattern or object stage costs cognitive effort.

### Memory and Its Limits
A simplified but useful model of memory distinguishes three systems. 
![[Pasted image 20260427001637.png]]
- **Sensory memory** (specifically **iconic memory** for vision) is a very brief, high-capacity store that holds a snapshot of the visual scene for less than a second. It is the substrate for pre-attentive processing. 
- **Short-term memory** (working memory) is severely capacity-limited, holding around 7 plus or minus 2 items (roughly 5 to 9 chunks) before information is displaced or forgotten.
- **Long-term memory** is effectively unlimited in capacity but requires deliberate encoding through rehearsal or meaningful association.

The working memory limit is the most practically important constraint for data visualisation design. Users cannot hold more than about 7 distinct chunks in mind simultaneously, which is why using a legend with 11 colour-coded categories fails: by the time the eye reaches the tenth bar, the first category's colour has been forgotten. Placing labels directly on chart elements eliminates the need to hold colour-to-category mappings in working memory entirely.

### Iconic Memory and Pre-attentive Processing
**Pre-attentive processing** is the ability to detect certain low-level visual differences before conscious attention is engaged. It operates in under 200 to 250 milliseconds, processes targets in parallel across the visual field, and is independent of the number of distractors meaning the search time doesn't scale with the quantity of surrounding items. The classic example from Knaflic (2015) is spotting the number 3 in a field of grey digits:
![[Pasted image 20260427143155.png]]
Without any highlighting it requires slow serial search, and search time scales with the number of distractors (more items = longer to find it).

![[Pasted image 20260427143048.png]]
With the 3s bolded in black they pop out instantly and effortlessly. In this case, pre-attentive processing kicks in, it's fast, and crucially, that speed doesn't degrade as you add more distractors.

![[Pasted image 20260427143824.png]]
The pre-attentive attributes identified by Stephen Few (cited in Knaflic 2;015) include orientation, shape, line length, line width, size, curvature, added marks, enclosure, hue, intensity, spatial position, and motion. These attributes can be deliberately deployed in chart design to create visual hierarchy and direct attention to the most important elements of the data story without the viewer needing to consciously search for them.
![[Pasted image 20260427143956.png]]

### Applying These Principles: Practical Design Rules
Three direct applications follow from the psychology above.

First, reduce working memory load by placing labels near the data they describe rather than in a separate legend, especially when there are more than four or five categories.

Second, use pre-attentive attributes strategically. If only one bar in a chart represents a concerning value, colouring that bar differently from all others causes it to pop out immediately (Knaflic Fig 4.8).
![[Pasted image 20260428130850.png]]
The same technique works in text: bolding a single critical sentence draws the eye before conscious reading begins (Knaflic Fig 4.7).
![[Pasted image 20260428130857.png]]

Third, use contrast deliberately. As Cairo (2012) demonstrates with the wolf-in-trees example, a figure is only visible against a background when there is sufficient contrast in luminance, hue, or both. Too many competing colours or lines (Cairo Fig 1.5, fertility rate spaghetti chart)
![[Pasted image 20260428130941.png]]
This overwhelm the iconic memory system and make pattern extraction impossible. Reducing all non-focal lines to grey and highlighting only one or two key series (Cairo Fig 1.6) restores clarity.
![[Pasted image 20260428131033.png]]

### Key Takeaways
1. Perception is not objective: the brain actively constructs what we see, biased by context, experience, and goals.
2. Short-term memory holds only 5 to 9 items; eliminate the need to remember colour-to-label mappings by labelling data directly.
3. Pre-attentive attributes (colour, size, orientation, position) are processed in parallel in under 250 ms and can be used to guide attention effortlessly.
4. Contrast drives detectability: a feature is only visible when it differs sufficiently from its surroundings in at least one pre-attentive dimension.

**Remember this:** The viewer's brain, not the designer's intent, determines what a visualisation communicates.

**One analogy to remember it by:** A chart is like a forest trail: signposting the key turn with a bright red arrow means the hiker never has to memorise the map.

---

## Part 2: Colour in Data Visualisation

### What is Colour in the Context of Visualisation?
**Colour** in data visualisation refers to the deliberate use of the three dimensions of colour (hue, saturation, and luminance) as visual encoding channels to represent categories, quantities, or emphasis.

In simple terms: colour is a tool, not decoration. Each dimension of colour works differently in the brain and is suited to different data types.

![[Pasted image 20260428212153.png]]
Think of it like: a musician choosing between melody, harmony, and rhythm to carry different aspects of a song. Each colour dimension is a separate channel that the visual system reads independently.

Why it matters: misusing colour (wrong dimension for the data type, too many hues, no accommodation for colour blindness) is one of the most common causes of charts that mislead or confuse readers.

### The Three Dimensions of Colour
![[Pasted image 20260428214339.png]]
**Hue** is what most people call "colour" in everyday language: red, blue, green, and so on. The visual system does not interpret hue as ordered or quantitative. There is no natural sense that blue is "more than" red. This makes hue appropriate for encoding nominal categories but inappropriate for encoding magnitude.

![[Pasted image 20260428214359.png]]
**Saturation** (sometimes called chroma) describes the purity or vividness of a colour, ranging from grey through pastel to fully vivid. The visual system does interpret saturation as ordered, so it can encode magnitude: a pale blue to a vivid blue naturally reads as "more of something."

![[Pasted image 20260428214408.png]]
**Luminance** (value, brightness) describes lightness or darkness. Like saturation, it is interpreted as ordered by the perceptual system. A light-to-dark scale reads as a quantity scale. Luminance difference is also the primary driver of contrast, which governs detectability independently of hue.

The practical rule from Ware (2010): use high-saturation, vivid hues for small marks (symbols, lines, dots) and low-saturation, light hues for large areas (backgrounds, fills). Reversing this makes small marks hard to discriminate due to simultaneous contrast effects.

### Colour Perception is Relative, Not Absolute
![[Pasted image 20260428214435.png]]
Colour is perceived relative to its surrounding context, not as an absolute property. The same physical grey appears much darker when surrounded by white than when surrounded by black (the classic Adelson checkerboard). This simultaneous contrast means the same bar colour will look different depending on the background and adjacent colours. Designers cannot assume a colour will look identical on every screen or in every layout.

This relativity also explains why rainbow (hue-only) colour scales are problematic for quantitative data (Munzner 2014): the brain cannot interpret hue differences as ordered magnitudes, so the reader must consciously decode the legend for every data point rather than reading magnitude directly from the visual.

### Colour Use Guidelines
![[Pasted image 20260428215123.png]]
Keep the number of distinct hues to no more than 6 to 12. Beyond this, working memory cannot maintain the legend mapping and the chart becomes unreadable. Colour is also harder to discriminate on smaller marks, so small dots or thin lines should use high-saturation hues with sufficient luminance contrast.

![[Pasted image 20260428215145.png]]
Use luminance and saturation for ordered (magnitude) data; use hue only for nominal (categorical) data. Avoid the rainbow colour map for sequential or diverging data: a blue-to-yellow divergent scale based on saturation is far easier to interpret than a hue-based rainbow (Munzner 2014).

![[Pasted image 20260428215052.png]]
Use colour sparingly and deliberately: a single contrasting hue on one bar draws attention; the same hue applied to every bar draws attention nowhere (Knaflic Fig 4.16).

Be aware of cultural colour associations. Red means danger in Western contexts but good fortune in Chinese culture. Colour associations vary substantially across cultures (InformationIsBeautiful colours-in-cultures), so charts for international audiences should not rely on culturally specific colour meanings.

### Colour Blindness
![[Pasted image 20260428215234.png]]
Approximately 8 to 9 percent of males and under 1 percent of females have some form of colour vision deficiency. The most common forms are red-weak (protanomaly), red-blind (protanopia), green-weak (deuteranomaly), and green-blind (deuteranopia). All of these collapse red-green distinctions, which is precisely the contrast most commonly exploited in charts (red for bad, green for good).

The practical response is double encoding:
![[Pasted image 20260428215543.png]]Use colour plus at least one other pre-attentive attribute (shape, pattern, position, label) to convey the same distinction. A chart where category can be identified by pattern alone, as well as by colour, remains readable for colour-blind users (Dufour & Meeks 2024, Fig 10.12). WCAG guidelines specify that colour must not be the only visual means of conveying information, and that where colour differences encode data, the colours should also differ in luminance by a contrast ratio of at least 3:1 to be distinguishable.

### Key Takeaways
1. Use hue for categories (nominal data) and saturation/luminance for magnitude (ordered/quantitative data).
2. Limit distinct hues to 6 to 12; beyond that, working memory cannot maintain the mapping.
3. Colour is perceived relatively: always check how colour choices look in context, not in isolation.
4. Double-encode with shape or pattern for colour-blind accessibility; do not rely on red-green distinctions alone.
5. Colour blindness affects roughly 1 in 12 males; designing for it is not an edge case.

**Remember this:** Colour is the most overused and misused encoding channel: treat it as a scarce resource to be spent only where it earns attention.

**One analogy to remember it by:** Hue is the flag's colour (identifies the team); saturation and luminance are the brightness of the lights (shows intensity). Use each for what it is built for.