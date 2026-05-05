## Part 1: WCAG and Accessibility Principles

### What is Web Accessibility?
**Web accessibility** is the practice of designing digital content so that people with a wide range of disabilities can perceive, understand, navigate, and interact with it.

In simple terms: an accessible website works for everyone, including people who use screen readers, keyboard-only navigation, or who have visual, motor, cognitive, or auditory impairments.

Think of it like: a building with ramp access. The ramp serves wheelchair users primarily, but also parents with prams, delivery workers with trolleys, and anyone with a temporary injury. Accessible design frequently benefits everyone.

Why it matters: there is both an ethical and legal dimension. Many jurisdictions impose a legal responsibility to make websites accessible, particularly for government services. Beyond compliance, accessible design produces cleaner, more usable interfaces for all users.

### Types of Disability That Affect Web Use
![[Pasted image 20260428220225.png]]
Firth (2024) identifies four broad categories:
- **Auditory** disabilities affect processing of audio content.
- **Cognitive** disabilities affect reading comprehension, attention, and memory.
- **Physical and motor** disabilities affect the ability to use a mouse or standard input device.
- **Visual** disabilities range from colour vision deficiency through low vision to total blindness.

Critically, disabilities exist on a spectrum of permanence. Microsoft's inclusive design framework above illustrates this with four sensory channels (touch, see, hear, speak), each with a permanent form (e.g., one arm), a temporary form (e.g., arm injury), and a situational form (e.g., holding a baby). A person holding a phone in one hand while navigating a website has a situational motor disability; designing for the permanent case helps all three groups simultaneously.

### The WCAG Framework
The **Web Content Accessibility Guidelines (WCAG)** are the international standard for web accessibility, published by the W3C. They are organised around four principles:
**Perceivable**, **Operable**, **Understandable**, and **Robust**. In short, **POUR**.
- **Perceivable** requires that all information be presentable to users in ways they can sense. Key requirements include text alternatives for non-text content, adaptable content structure, and sufficient contrast and distinguishability.
- **Operable** requires that all interface components and navigation be usable without a mouse. Key requirements include full keyboard accessibility, sufficient time for users to read and interact, avoidance of content that could trigger seizures (e.g., no rapidly flashing red), and navigable page structure.
- **Understandable** requires that information and operation be clear and unambiguous. Key requirements include readable text, predictable behaviour, and error prevention.
- **Robust** requires that content be reliably interpreted by a wide range of user agents, including assistive technologies. Key requirements include valid HTML, accessible APIs, and compatibility with current and future tools.

WCAG defines three conformance levels.
- **Level A** is the minimum baseline: must-have features including text alternatives for non-text content, keyboard accessibility, and seizure prevention.
- **Level AA** is the recommended standard for most organisations: includes all Level A criteria plus contrast ratios, resizable text, navigation landmarks, and descriptive headings.
- **Level AAA** is the highest level: includes sign language alternatives, enhanced contrast, and accessible authentication.

### Adaptive Strategies and Assistive Technologies
Users with visual impairments employ both adaptive strategies (enlarging text, zooming, reducing mouse speed, using video captions) and assistive technologies.
Common assistive technologies include screen readers (Narrator, VoiceOver, ChromeVox, JAWS), screen magnifiers, speech recognition systems, and switch control devices that allow full device operation from a single button.
Screen readers read page content aloud in a sequence determined by the document's semantic structure. They navigate via keyboard using Tab, arrow keys, Shift, Ctrl, and Option. They interpret semantic HTML tags to understand page layout and the role of each element.

### Key Takeaways
1. Accessibility benefits everyone: designing for permanent disabilities also helps temporary and situational users.
2. WCAG is organised around four principles (Perceivable, Operable, Understandable, Robust) across three conformance levels (A, AA, AAA).
3. Level AA is the recommended minimum for most professional and government-facing work.
4. Screen readers rely entirely on semantic HTML structure and ARIA attributes to convey meaning; visual appearance is invisible to them.

**Remember this:** Accessibility is not a checklist bolted on at the end; it is a set of design constraints that, when respected from the start, produce cleaner and more usable interfaces for everyone.

**One analogy to remember it by:** WCAG levels are like building codes: Level A is the fire exit, Level AA is the disability ramp, Level AAA is the full universal design standard. Every level builds on the one below.

---

## Part 2: Making Visualisations Accessible

### What is Accessible Visualisation?
**Accessible visualisation** is the practice of designing charts and interactive data graphics so that users with visual or other disabilities can access the equivalent informational content without necessarily having an identical visual experience.

In simple terms: a blind user does not need to see the bars; they need to understand what the bars mean.

Think of it like: an audio description track on a film. It does not recreate every frame, but it conveys the narrative meaning so the listener reaches the same understanding as a sighted viewer.

Why it matters: data visualisations are primarily visual artefacts, so they risk becoming completely inaccessible to screen reader users or users with colour vision deficiency if accessibility is not actively designed in.

### The Chartability Framework
**Chartability** (Fizz Studio) is a heuristic framework for evaluating chart accessibility that maps directly onto the WCAG POUR principles, extended with two additional heuristics.

![[Pasted image 20260429012243.png]]
**Perceivable** failures in charts include: low contrast between bars or lines and background; content that is only visual (no text equivalent); text size below minimum thresholds; and animations that could trigger photosensitive reactions.


**Operable** failures include:
![[Pasted image 20260429012321.png]]
Interaction modalities that accept only mouse input (no keyboard equivalent).

![[Pasted image 20260429012454.png]]
No instructions telling users how to interact.

![[Pasted image 20260429012509.png]]
Custom JavaScript controls that intercept and override screen reader keyboard shortcuts such as Tab or arrow keys.

**Understandable** failures include: no title, summary, or caption; no explanation of how to read the chart or what it is showing; and text written at an inappropriately high reading level. For general public-facing content, text should target grade 9 or below, verifiable with tools like the Hemingway App.

**Compromising** (Understandable yet Robust) failures include: providing a chart with no accompanying data table.
![[Pasted image 20260429012632.png]]
An accessible chart should offer the underlying data in tabular form so users who cannot extract meaning from the visual can still access the numbers.

![[Pasted image 20260429013707.png]]
**Assistive** (Understandable and Perceivable but labour-reducing) failures include: inappropriate data density where too many overlapping elements prevent meaningful navigation, and interaction designs that require tedious sequential navigation through every data point.

**Flexible** (Perceivable and Operable yet Robust) failures include: designs that override user stylesheet preferences, for example by applying inline CSS that breaks a user's high-contrast theme.

### Textual Accessibility
![[Pasted image 20260429012721.png]]
Every chart should include a clear explanation of its purpose and how to read it. This can be provided through an introduction paragraph above the chart, a legend that explains encoding, and a descriptive caption. For interactive charts, an explicit instructions div should tell users that they can interact via click, mouse-over, or the Tab key.
![[Pasted image 20260429012801.png]]

Text size in SVG-based charts scales with the SVG container. Because SVG uses a viewBox without fixed pixel dimensions, all graphical elements (including text) scale proportionally with the container width. 
![[Pasted image 20260429012855.png]]
At a large screen width (1200 px), a 15 px label may render correctly; at a small screen width (315 px), the same label shrinks to approximately 4 px (calculated as `315 × 15 ÷ 1200 ≈ 4`). To ensure a minimum of 15 px on small screens, the maximum SVG font size must be scaled up: `15 × 1200 ÷ 315 ≈ 57 px`.
![[Pasted image 20260429013253.png]]

In D3, this is implemented with a `fontSizeScale` using `d3.scaleLinear()` mapped from a domain of screen widths to a range of font sizes, with `.clamp(true)` to prevent values outside the defined range. A `resizeChart()` function listens to the window resize event and updates font sizes dynamically.
![[Pasted image 20260429013049.png]]

### Contrast
![[Pasted image 20260429013343.png]]
WCAG requires a contrast ratio of at least 4.5:1 for regular text and 2:1 for large geometric shapes. Contrast can be checked with tools including [Adobe Colour Contrast Checker](https://color.adobe.com/create/color-contrast-analyzer), [WebAIM's Contrast Checker](https://webaim.org/resources/contrastchecker/), the [TPGi desktop app](https://www.tpgi.com/color-contrast-checker/), [Contrast Grid](https://contrast-grid.eightshapes.com/?version=1.1.0&background-colors=&foreground-colors=%23FFFFFF%2C%20White%0D%0A%23F2F2F2%0D%0A%23DDDDDD%0D%0A%23CCCCCC%0D%0A%23888888%0D%0A%23404040%2C%20Charcoal%0D%0A%23000000%2C%20Black%0D%0A%232F78C5%2C%20Effective%20on%20Extremes%0D%0A%230F60B6%2C%20Effective%20on%20Lights%0D%0A%23398EEA%2C%20Ineffective%0D%0A&es-color-form__tile-size=compact&es-color-form__show-contrast=aaa&es-color-form__show-contrast=aa&es-color-form__show-contrast=aa18&es-color-form__show-contrast=dnp), and the [accessible colour palette builder](https://toolness.github.io/accessible-color-matrix/). Browser-level auditing is available through the WAVE extension (WebAIM) and [Chrome's Lighthouse DevTools](https://developer.chrome.com/docs/lighthouse/) panel.

For colour blindness, the solution is **double encoding**: combine colour with a second pre-attentive attribute such as shape or pattern. Encoding each conservation status category with both a unique hue and a unique fill pattern (dots, lines, crosshatch) means colour-blind users can distinguish categories through pattern alone (Dufour & Meeks Fig 10.12).
![[Pasted image 20260429013603.png]]
Colours should also be adjusted to accessible palettes that have sufficient luminance differences between them even when collapsed to greyscale.

### Screen Reader Accessibility in D3
SVG content is mostly opaque to screen readers. Without explicit accessibility markup, a screen reader may read out all tick label values as raw text, skip the chart entirely, or provide no useful information.

The recommended approach involves three layers of markup. First, give the SVG element `role="img"` to signal to the screen reader that this is an image-like object, which stops it from reading tick labels sequentially as if they were body text. Pair this with `aria-labelledby` pointing to the IDs of a `<title>` element and a `<desc>` element inside the SVG.

```js
const svg = d3.select("#histogram")
  .append("svg")
  .attr("viewBox", `0, 0, ${width}, ${height}`)
  .attr("role", "img")
  .attr("aria-labelledby", "histogramTitle histogramDescription");

svg.append("title")
  .attr("id", "histogramTitle")
  .text("Visualisation of TV screen time data for children in hours");

svg.append("desc")
  .attr("id", "histogramDescription")
  .text("This is a histogram plotting frequency of hours of TV watched by children...");
```
The `<title>` provides a brief label (also shown as a tooltip on hover). The `<desc>` provides a longer summary of the chart's main finding, giving screen reader users the equivalent of the visual insight without requiring them to navigate every bar individually. For more granular access, a `generateDescription()` function can programmatically iterate over bins and build a string like "0 to 1 hours: 29 children. 1 to 2 hours: 47 children…" which is then set as the `<desc>` text.

### Semantic Markup for Page Structure
![[Pasted image 20260429013844.png]]
Proper semantic HTML helps screen readers understand and announce page layout. Heading elements (`<h1>` through `<h3>`) create a navigable document outline; there should be only one `<h1>`. Landmark elements like `<header>`, `<nav>`, `<main>`, `<section>`, `<article>`, and `<footer>` help screen readers announce the structural logic of the page. Interactive elements should use native `<button>`, `<a>`, and `<form>` tags rather than `<div>` elements styled to look interactive, because native elements carry built-in keyboard focus and ARIA role information automatically.

### ARIA for Interactive Controls
For interactive filter buttons in D3, use ARIA attributes to communicate group identity and toggle state. Apply `role="group"` and `aria-label` to the button container so the screen reader announces "Filter by gender group" when the user tabs into it. Apply `type="button"` to individual buttons to clarify they are not form submission controls. Apply `aria-pressed="true"` or `aria-pressed="false"` to each toggle button so the screen reader announces the current state. Update `aria-pressed` in the click handler alongside the CSS active class:
```js
d3.select("#filters_gender")
  .attr("role", "group")
  .attr("aria-label", "Filter by gender");

// On click:
d3.selectAll("#filters_gender .filter")
  .classed("active", filter => filter.isActive)
  .attr("aria-pressed", filter => filter.isActive);
```

### Keyboard-Accessible Tooltips
Standard mouseover tooltips are inaccessible to keyboard users. The accessible solution requires three additions. First, create an `aria-live="polite"` region (a visually hidden `div` with class `sr-only`) that the screen reader monitors for updates. Second, refactor tooltip show and hide logic into shared `showTooltip()` and `hideTooltip()` functions that update both the visual tooltip and the live region text simultaneously. Third, bind these functions to both `mouseenter`/`mouseleave` and `focus`/`blur` events on data elements, and add `tabindex="0"` to each interactive element so keyboard users can tab to it. Keyboard navigation between bars can be implemented by handling `keydown` events for ArrowRight and ArrowLeft to shift focus to sibling elements.

The `sr-only` CSS class hides the live region visually while keeping it accessible to the screen reader:
```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

### Other Considerations
Avoid overriding user stylesheets with D3's inline `.style()` calls for properties that users may have customised (such as font size or colour). Prefer CSS classes over inline styles so that user-defined stylesheets can take precedence. Never use flashing red animations: rapidly flashing red content can trigger photosensitive epileptic seizures and is prohibited at WCAG Level A.

### Key Takeaways
1. Accessible visualisation requires equivalent access to information, not an identical visual experience.
2. Use `role="img"`, `<title>`, and `<desc>` on SVG elements to give screen readers a meaningful description of the chart.
3. Double-encode categorical data with colour plus shape or pattern to support colour-blind users.
4. Make tooltips keyboard-accessible by binding them to `focus`/`blur` as well as `mouseenter`/`mouseleave`, and announce them via an `aria-live` region.
5. Responsive font sizing in SVG requires explicit scaling logic (rule of three) because SVG text scales with the container.

**Remember this:** If a screen reader user has to tab through every individual axis tick to understand a chart, the chart has failed; the `<desc>` element exists precisely to prevent this.

**One analogy to remember it by:** An accessible chart is like a well-captioned photograph: a sighted person sees the image, a blind person reads the caption, and both leave with the same understanding.