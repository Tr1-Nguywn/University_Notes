## Part 1: Storytelling with Data

### What is Storytelling with Data?
**Storytelling with data** is the practice of structuring a data presentation around a clear narrative so the audience is guided toward a specific insight or action, rather than left to interpret raw findings on their own.

In simple terms: instead of dumping charts on your audience, you choose what to show, in what order, and frame it so the message lands clearly.

Think of it like: a tour guide vs a museum with no signs. Without guidance, visitors wander and may miss the most important exhibits. With a guide, everyone leaves having understood the key story.

Why it matters: most decisions hinge on whether decision-makers actually understand and act on data. A technically correct but poorly communicated analysis often gets ignored or misinterpreted.

### Exploratory vs Explanatory
The first distinction to make before building any visualisation is whether you are doing **exploratory** or **explanatory** analysis.

**Exploratory** analysis is for the analyst: finding patterns, working out what the data says. You might try 100 charts; none need to be polished.

**Explanatory** analysis is for the audience: communicating what you found, what was interesting. This is what presentations and reports are made of.

Most people make the mistake of sharing their exploratory process when they should only be sharing the explanatory output. Showing your full analytical journey confuses and bores the audience.

### The Tension in Storytelling
Knaflic (2015) takes a business-oriented stance: focus on informing and convincing the audience toward an action. The book is explicitly not about scientific or objective presentation - it is about clear, persuasive communication.

![[image 46.png|image 46.png]]
This creates a genuine tension. Krzywinski & Cairo (2013) argue that you should guide interpretation and leave out detail that does not advance the point. Katz (2013) pushes back harder, warning that storytelling encourages the unrealistic view that scientific projects fit a singular narrative, and can restrict data to only what agrees with the chosen story.

The fine line is between showing the most important data vs obscuring or manipulating the presentation to fit a pre-decided message. Both are possible with the same dataset. Being aware of this line is part of being an ethical communicator.

### Who, What, and How
Before designing anything, answer three questions about your audience.

**Who is the audience?** Consider their job role: marketer, executive, product team, scientist. This determines vocabulary, assumed knowledge, and what counts as a convincing argument.

**What is your relationship to them?** If you are part of an ongoing team, you need less methodology explanation - shared context already exists. If you are an outside consultant or agency, you need to establish credibility and explain your method more fully.

**What do you want them to do?** This is the most important question. Choose a specific action verb before designing a single slide: accept, agree, invest, change, recommend, understand. The verb anchors every subsequent design choice.

### Planning the Message
**3-Minute Story (Elevator Pitch)**
If you had only 3 minutes to tell the audience what they need to know, what would you say? This exercise forces you to identify the core message before you touch any presentation software. Most presentations fail because the presenter has not done this work first.

**The Big Idea**
Duarte (cited in Knaflic) defines the Big Idea as a single sentence that articulates your unique point of view, conveys what is at stake, and is a complete sentence.

Example: "Our pilot science program increased student interest from 44% to 68%, and expanding it district-wide requires $X in additional budget."

This sentence, written before opening a slide deck, becomes the test every chart must pass: does this visual advance the Big Idea, or is it exploratory noise that should be cut?

### Story Structure
A data presentation follows the same arc as any story. Knaflic maps this directly to a three-act structure:

**Setting (beginning):** Introduce the context. Who is the audience as a character, and what is their current world? This is where you establish the baseline - the state of things before the problem.

**Conflict:** Something has changed or is wrong. An incident creates an imbalance that matters to the audience. The data reveals this imbalance. This is where the key finding lives.

**Resolution:** The call to action. What does the audience need to do to restore balance? This is the recommendation, the ask, or the decision point.

Mapping your content to this arc before storyboarding prevents the common failure mode of building a presentation that has lots of data but no clear narrative direction.

### Storyboarding
Knaflic recommends planning with sticky notes rather than presentation software. The reason is important: presentation software encourages you to think about formatting before you have figured out structure. Sticky notes force you to think about what each beat of the story is before worrying about how it looks.

Each sticky note represents one idea or one chart. Arrange them in sequence and check: does each note advance the story toward the resolution? If not, remove it.

![[image 1 11.png|image 1 11.png]]
Example storyboard (Knaflic Fig 1.2):
1. Issue: Kids have bad attitudes about science
2. Demonstrate issue: Show student assignment grades over course of year
3. Ideas for overcoming the issue, including pilot program
4. Describe pilot program - goals, etc.
5. Show before and after survey data to demonstrate success of program
6. Recommendation: pilot was a success, let's expand it, we need $X

Each note maps cleanly to the three-act structure. The first two are setting, three and four are conflict and proposed resolution, five is evidence, six is the call to action.

### Choosing How to Present the Data
Once the story is planned, the chart type should serve the message. The same data can support different levels of emphasis depending on format:

**Single large number** (Knaflic Fig 9.29): "After the pilot program, 68% of kids expressed interest towards science, compared to 44% going into the program." One number, one comparison, maximum impact. Use this when the headline stat carries the whole argument.
![[image 2 9.png|image 2 9.png]]

**Simple grouped bar chart** (Knaflic Fig 9.30): Shows all five sentiment categories before and after. Good for an audience that wants to see the full distribution, not just the headline.
![[image 3 7.png|image 3 7.png]]

**100% stacked horizontal bar** (Knaflic Fig 9.31): Shows the proportion of each sentiment across before and after as a whole. Easier to compare the shift in the positive-sentiment cluster (Kind of interested + Excited) visually.
![[image 4 6.png|image 4 6.png]]

**Slopegraph** (Knaflic Fig 9.32): Plots each sentiment category as a line from before to after. Makes it immediately visible which categories went up, which went down, and by how much. Ideal for showing directional change across multiple categories.

![[image 5 6.png|image 5 6.png]]

**Annotated line chart** (Knaflic ticket volume example): When events explain a trend, annotate them directly on the chart. "2 employees quit in May" placed at the point of divergence between received and processed tickets makes the causal argument without needing a separate explanation.
![[image 6 5.png|image 6 5.png]]

Words used wisely as chart annotations add narrative without adding clutter (Knaflic Fig 5.7 - Facebook breakup timing chart). Labels like "Valentine's Day - Boyfriend forgot to book the restaurant?" are the explanatory layer that transforms a line chart into a story.

### Key Takeaways
1. Exploratory analysis is for you; explanatory is for your audience. Know which mode you are in before presenting.
2. Always identify the action verb you want the audience to take before designing anything.
3. Plan with sticky notes before slides - structure first, formatting second.
4. The story arc (setting, conflict, resolution) maps directly onto most business and scientific presentations.
5. There is a real ethical tension: guiding interpretation is good, but cherry-picking data to fit a narrative is manipulation. Be honest about what the data actually shows.

**Remember this:** A great data story does not just display what happened. It makes the audience feel why it matters and what to do next.

**One analogy to remember it by:** A data story is like a court case, not a museum exhibit. The lawyer does not show every document - they select and sequence evidence to make the argument clear and actionable. But unlike a lawyer, an honest data communicator does not hide contradictory evidence.

---

## Part 2: Data Governance and Privacy

### What is Data Governance and Privacy?
**Data governance** is the framework organisations use to manage whose data they collect, how they collect it, and what they are permitted to do with it after collection. **Privacy** refers to an individual's right to control information about themselves.

In simple terms: data governance is the set of rules and processes that determine how data flows through an organisation responsibly. Privacy is the reason those rules exist.

Think of it like: the rules of a hospital records system. Who can access patient files, under what conditions, who is responsible for security, how long records are kept, and what happens if a breach occurs. Without those rules, sensitive data leaks, gets misused, or harms people.

Why it matters: as more data is collected digitally, the potential for harm from misuse grows significantly. Organisations that handle data without governance frameworks face legal liability, reputational damage, and real harm to individuals.

### Types of Sensitive Data
- **Personally Identifiable Information (PII)** is data that is unique or nearly unique to a particular individual and can directly identify them. Often defined specifically by applicable regulations.
	- Examples: name, date of birth, email, home address, IP address, tax file number, credit card number, passport, health records, biometric data, social media profiles, car registration.
	- Some are less obvious: IP address and workplace may still qualify as PII under most legal definitions.
- **Person-related data** relates to a person but does not directly identify them. Includes interests, beliefs, online and offline behaviours, and location patterns.
	- Examples: browsing history, app usage patterns, purchase categories.
- **Proprietary and confidential data (trade secrets)** is non-person data that is sensitive for business or contractual reasons. Leaking it would endanger a legal relationship or competitive position.
	- Examples: internal pricing models, unreleased product roadmaps, merger plans.
- **Sensitive by combination or group:** Some data does not identify someone individually but can be used to target them as part of a group, or becomes identifying when combined with other data.
	- Examples: pregnancy status, gambling habits, LGBTIQA+ identity.
	- ![[image 7 5.png]]
	- The Kosinski et al. (2013) study showed that Facebook likes alone could predict sexual orientation (88% AUC) and political affiliation (85%), demonstrating that seemingly innocuous behavioural data can reveal deeply sensitive attributes when aggregated.

### Key Governance Questions
When handling any dataset, Jarmul (2023) recommends asking:
- When and where was this data collected?
- What consent was given at collection? How am I allowed to use it?
- What processing (cleaning, null removal, unit standardisation) has it already undergone?
- Where else is this data processed or stored?
- What should I keep in mind about its quality and origin?

### Data Documentation
![[image 8 4.png|image 8 4.png]]
When handling data in an organisation, record information across five dimensions:

|Dimension|What to document|
|---|---|
|Collection procedure|Where, when, how it was gathered|
|Quality|Known issues, completeness, reliability|
|Security|Access controls, encryption status|
|Privacy|Sensitivity level, consent obtained|
|Definitions|Data dictionary, field descriptions|

Jarmul's lineage and consent tracking pipeline shows this as a six-stage process: collect user preferences, add lineage data (what, where, when), transport and transform, assess quality, address data sensitivity, then store with full attribute documentation.

### Anonymisation
**Anonymisation** means removing identifying information from data so the original source cannot be known.

The **information-privacy continuum** from strongest to weakest privacy guarantee:
![[image 9 4.png|image 9 4.png]]
Erased data > Anonymisation methods > Pseudonymisation + minimisation > Encrypting data or removing PII > Raw data

**Simple methods** include removing columns containing sensitive data entirely, aggregating row attributes (e.g., replacing exact age with an age range), or transforming values (e.g., replacing exact salary with a salary band).

**More complex methods:**
- **Encryption:** data is unreadable without a key, but the original data technically still exists.
- **Pseudonymisation:** replace identifiers with pseudonyms. A mapping table still exists and re-identification is possible.
- **True anonymisation:** methods like k-anonymity or differential privacy that provide mathematical guarantees against re-identification.

Example of aggregation in practice: a staff survey asks "What is your gender? What is your age range? Are you part or full time? What department do you work in?" rather than "What is your name?" Each question collects person-related data without directly identifying anyone.

### Privacy Attacks
Anonymised data is not inherently safe. Known attack types include:
- **Linkage attack (Netflix Prize, 2008):** Netflix released an anonymised dataset of movie ratings for a competition. Narayanan & Shmatikoff linked it against public IMDb ratings to re-identify specific users, revealing private viewing history and implied political and religious preferences.
- **Singling out attack:** Using a combination of attributes (zip code + gender + birth date) to uniquely identify an individual in a supposedly anonymised dataset. Latanya Sweeney showed that 87% of Americans could be uniquely identified using just these three fields.
- **Strava heat map attack:** Strava published a global activity heat map using aggregated GPS data. In regions with sparse civilian activity, the map inadvertently revealed the routes and layouts of classified military bases, because soldiers were using Strava while on duty.
- **Membership inference attack:** Querying a machine learning model to determine whether a specific individual's data was used in its training set. A significant concern for models trained on health or financial records.

### Privacy Preferences

People have different comfort levels with different types of data. What one person openly shares - location, friendship groups, political affiliation - another person considers highly sensitive. This means:
- Sensitivity is not just a property of the data type; it depends on context and the individual.
- Data systems should be designed to accommodate variation in preferences, not assume uniformity.
- Categories like gender identity, religion, and political affiliation require particular care, as their exposure can lead to discrimination or harm.

### Legalities
**GDPR (General Data Protection Regulation)** applies to all EU citizens regardless of where the processing organisation is located.

|Right|Description|
|---|---|
|Right to be informed|People must know how their data is collected, used, and processed|
|Right to access|People can request to see what data is held about them|
|Right to rectification|People can correct false or misleading information|
|Right to deletion / erasure|People can demand companies delete their data|
|Right to restrict processing|People can opt out or limit how their data is used|
|Right to data portability|People can take their data to another service|
|Right to object|People can object to use of their data for marketing, research, etc.|
|Right to opt out of automated decision-making|People can opt out of algorithmic or ML-based decisions that affect them|

Other key regulations:
- **APP (Australian Privacy Principles):** Australia's federal privacy framework, governing how personal information is handled by government agencies and large private organisations.
- **CCPA (California Consumer Privacy Act):** US state-level regulation giving California residents rights similar to GDPR.
- **HIPAA (Health Insurance Portability and Accountability Act):** US federal law governing the handling of health information.

Know your relevant policies (Privacy Policy, Terms of Service agreements). Be prepared to work with legal professionals to understand and meet your obligations. Ignorance of applicable regulations is not a defence.

### Key Takeaways
1. Data governance is the framework that determines who can collect what, and what they can do with it.
2. PII, person-related data, and trade secrets each require different handling and protections.
3. "Anonymised" data is not inherently safe. Linkage, singling out, and inference attacks can re-identify individuals even from carefully processed datasets.
4. Privacy is not binary. It exists on a continuum from raw data to fully erased data.
5. Legal frameworks (GDPR, APP, CCPA, HIPAA) impose specific obligations, and non-compliance carries real consequences.

**Remember this:** Removing a name column does not make a dataset anonymous. Sensitive inference and re-identification are possible from combinations of innocuous-seeming fields. Privacy must be designed in, not bolted on after the fact.

**One analogy to remember it by:** Anonymisation without proper technique is like putting tape over your name on a package but leaving your address, phone number, and a photo of your house visible. The tape fools no one who looks carefully.