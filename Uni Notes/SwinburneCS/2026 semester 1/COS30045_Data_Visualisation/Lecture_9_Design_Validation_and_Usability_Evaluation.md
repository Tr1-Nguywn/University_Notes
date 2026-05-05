## Part 1: Design Validation and User Research

_Reference: Munzner, T. (2014) Visualization Analysis and Design, CRC Press, Ch 4_

---

### What is Design Validation?

**Design validation** is the process of checking that a visualisation design actually solves the right problem in the right way for the right users, at each level of the design process.

In simple terms: it is how you confirm your visualisation works before and after building it.

Think of it like: building a bridge and checking the blueprints, the structural calculations, and the load capacity at each stage rather than waiting until it collapses.

Why it matters: a visualisation that looks polished can still fail completely if it answers the wrong question, shows the wrong data, or uses an ineffective encoding. Validation catches these failures early.

### The Munzner Nested Levels of Design

Munzner's model organises visualisation design into four nested levels, each of which can go wrong in a distinct way and each of which requires its own validation approach.

The outermost level is the **domain situation**: understanding who the target users are and what problems they face. The threat here is that you have misunderstood their actual needs. The validation method is to observe and interview target users before designing anything.

Inside that sits **data/task abstraction**: translating the domain problem into a formal statement of what data needs to be shown and what tasks users need to perform on it. The threat is showing users the wrong thing entirely, even if the visualisation is technically correct.

Inside that is the **visual encoding and interaction idiom**: the choices about how to visually represent the data and how users interact with it. The threat is that the way you show the data simply does not work perceptually or cognitively. Validation here means justifying your design choices against alternatives and, where needed, conducting qualitative analysis or lab studies.

The innermost level is the **algorithm**: the code that implements the idiom. The threat is that the code is too slow or uses too much memory. Validation means analysing computational complexity and measuring system time and memory in practice.

A crucial insight from Munzner is that errors at outer levels cascade inward: a perfectly fast algorithm implementing a beautifully chosen idiom is worthless if the abstraction or domain framing was wrong.

### Domain Research

Before designing anything, you need to invest time understanding the domain. This means learning the relevant mathematics, statistics, history, and political context of the data; reviewing what tools and visualisations already exist for similar problems; becoming familiar with the vocabulary practitioners use; and reviewing academic literature through sources like Google Scholar.

The slide example shows an energy ratings dataset for televisions. A designer unfamiliar with the domain would not know which power consumption measure (passive standby, active standby, or average mode power) is most meaningful for their users' goals. Domain research answers these questions cheaply, before wasting precious user time.

A practical principle: collect background information through desk research first and only ask users questions that genuinely cannot be answered any other way.

### Domain-Related User Research: Interviews and Observations

When the task is complex or the cost of getting the visualisation wrong is high, desk research is not enough. Two primary methods are used.

**Interviews** are structured conversations with target users designed to surface their mental models, workflows, and priorities. A single interview gives deep insight into one person's perspective; multiple interviews across different roles or levels of expertise reveal where views converge and diverge. Good interviews use neutral language so as not to lead the participant, allow deliberate silences so people can think, deploy **probes** ("anything else?", "can you say more about that?") to elicit fuller responses, and use **prompts** such as screenshots or scenarios to anchor abstract discussions in concrete examples. Notes should be recorded verbatim where possible and confirmed with the participant afterwards.

**Observation and field studies** involve watching users do their actual work in their actual environment. This reveals what people genuinely do rather than what they say they do, which often diverge. The main limitations are that events of interest may be rare, that observing alone cannot reveal why a user is making a choice, and that the presence of an observer can change behaviour: this last effect is called the **Hawthorne effect** and should be anticipated in study design.

Interviews and field studies are most valuable when the task domain is complex, the stakes of misunderstanding user needs are high, and the target users have specialised knowledge the designer lacks.

### Validation Map Across Levels

Pulling the ideas together, Munzner maps validation methods onto the threat at each level:

- Domain situation threat: observe and interview target users
- Data/task abstraction threat: justify task/data choices relative to alternatives
- Visual encoding/interaction idiom threat: qualitative analysis, informal usability studies, lab studies measuring time and errors
- Algorithm threat: complexity analysis, system time and memory benchmarks
- After implementation: test on target users and collect anecdotal evidence; deploy and conduct field studies; measure adoption rates

This creates a practical checklist that pairs every design decision with an appropriate validation step.

### Key Takeaways

1. Each of Munzner's four nested design levels has a characteristic failure mode; validation strategies must be matched to the level.
2. Domain research and user research come before design, not after, to prevent building the right interface for the wrong problem.
3. Interviews give depth; observations give behavioural truth; both are needed when the stakes are high.
4. The cost of fixing a domain-level error after implementation is far higher than fixing it during early research.

**Remember this:** validation is not a final check; it is a discipline applied continuously at every level of design.

**One analogy to remember it by:** treating each nested level like a Russian doll where the outer layer must be correct before the inner ones are even worth opening.

---

## Part 2: Usability Evaluation

_Reference: Hartson, R. & Pyla, P.S. (2018) The UX Book: Agile UX Design for a Quality User Experience, Morgan Kaufmann, Part V_

---

### What is Usability Evaluation?

**Usability evaluation** is a structured method for assessing how well a visualisation or interface supports users in completing their tasks effectively, efficiently, and with satisfaction.

In simple terms: you watch people use your design and record where they succeed, struggle, or fail.

Think of it like: a test drive with a real driver on a real road rather than a theoretical review of the car's specifications.

Why it matters: designers develop blind spots from familiarity with their own work. Evaluation by real or representative users surfaces problems invisible to the designer and provides evidence for iterative improvement.

### Types of Evaluation

There are two broad categories of evaluation that differ in who does the assessing.

**Inspection evaluation** involves an expert evaluator examining a prototype or deployed system without end users present. The evaluator applies domain knowledge and established guidelines to identify likely problems. This is faster and cheaper but limited to what the expert can anticipate.

**User evaluation** involves representative users: real people who match the target audience, interacting with the system while evaluators observe. This surfaces real-world failures that inspection cannot predict.

Both types are valuable at different stages. Inspection is practical early in design when recruiting users would be expensive; user evaluation is essential before final release and after major design changes.

### Inspection: Visualisation Heuristics

A widely used inspection framework for data visualisation is the set of heuristics developed by **Zuk and Carpendale (2006)**. These 13 principles give evaluators a structured lens for critiquing a visualisation without user participants:

1. Ensure visual variables have sufficient discriminable length or range.
2. Do not rely on colour to convey reading order.
3. Account for the fact that colour perception changes with the size of coloured regions.
4. Local contrast affects how colours and greys appear relative to their surroundings.
5. Design for colour blindness.
6. Pre-attentive benefits (pop-out effects) are stronger when the display fills more of the visual field.
7. Quantitative comparison requires position or size variation, not just hue.
8. Preserve the dimensionality of the data in the graphic (do not map 1D data onto 2D area).
9. Maximise the data-ink ratio: put the most information in the least space.
10. Remove extraneous ink that does not encode data.
11. Apply Gestalt laws of perceptual grouping deliberately.
12. Provide multiple levels of detail so users can explore at different granularities.
13. Integrate explanatory text directly into the visualisation where it aids interpretation.

### User Evaluation: Method Components

Designing a user evaluation requires specifying four components.

**Participants** defines who will take part and how they will be recruited. The target group should reflect the actual intended users in terms of relevant demographics and domain expertise. Recruiting from the correct population is essential: testing a clinical dashboard on computer science students will not reveal the failures that matter.

**Materials** covers everything needed to run the session: the prototype or system itself, task sheets, questionnaires, consent forms, recording equipment, and any physical facilities needed.

**Procedure** is the step-by-step plan for running each session. A standard sequence runs as follows: greet the participant; administer informed consent; deliver the participant briefing; run a demographic questionnaire; have the participant complete the visualisation tasks with post-task questions after each one; administer a post-test satisfaction questionnaire; and debrief and thank the participant. If recording, the recording window typically runs from when the user begins the first task to when they finish the last.

**Ethics and privacy** governs how participants are protected from harm and how their data is handled.

### Task Design

Tasks are the instructions given to participants during the session. Good task design follows clear principles.

A task should give the user a concrete goal rather than a procedure. It should be written in everyday language, not the vocabulary of the interface: telling a user to "hover over the tooltip" is instructing them how to use the tool, which bypasses the entire point of the evaluation. Task completion requires a pre-defined correct answer or observable criterion so the evaluator can record success or failure objectively.

The Walmart expansion visualisation examples from the slides illustrate this well. A bad task reads: "Find the first Walmart in a major city. It is easy to find more detailed information in this visualisation. If you hover your mouse over a city name you will trigger a tool tip..." This is bad because it reveals the interaction mechanism in the task description, removing the possibility of observing whether users can discover it on their own. A good version of the same task reads simply: "When did Houston get its first Walmart?" A further good task asks: "What year did Walmart stores start expanding rapidly?" This tests pattern recognition in the visualisation without prescribing a method.

### Ethics of Human Research

Any study involving human participants carries ethical obligations. The fundamental requirement is that no harm comes to a participant as a result of their participation. Harm can take several forms: physical (pain or injury), psychological (distress, guilt, fear), social (damage to relationships or employment), economic (costs to the participant), or legal.

Three core principles govern ethical practice.

**Freedom to withdraw** means participants must be told before the session that they can leave at any time without giving a reason and without facing any penalty. If a participant appears distressed during a session, the evaluator should check in and consider ending early.

**Informed consent** means participation must be voluntary and that participants must understand what they are agreeing to. The consent process involves two documents: an explanatory statement the participant can keep, and a signed consent form the investigator retains. The explanatory statement must cover the purpose of the study, an overview of what the participant will be asked to do, how long it will take, what data will be collected and how it will be used, and how to withdraw or raise concerns. The consent form records the participant's explicit agreement to each relevant item (being interviewed, being recorded, making themselves available for follow-up).

**Privacy** requires that recordings and notes only be shared with people named on the consent form, that participants not be identified in any reporting, and that anonymisation protocols such as participant code numbers are used in place of real names.

### Key Takeaways

1. Inspection evaluation is fast and expert-driven; user evaluation is slower but reveals real-world failure modes that inspection misses.
2. Tasks given to participants must state a goal, not a procedure, and must be written in plain language free of interface-specific terminology.
3. Ethical obligations are non-negotiable: freedom to withdraw, informed consent, and privacy protection must be built into every study design.
4. The standard user evaluation procedure follows a fixed sequence from greeting to debrief, with recording spanning only the task completion phase.
5. The Zuk and Carpendale heuristics provide a practical checklist for inspection without users, covering colour, space, pre-attentive processing, and data-ink efficiency.

**Remember this:** a task that tells users how to use your interface is not testing your interface; it is testing their ability to follow instructions.

**One analogy to remember it by:** a good usability task is like a fitness test that measures your ability to run, not a tutorial that teaches you the running technique right before you try.