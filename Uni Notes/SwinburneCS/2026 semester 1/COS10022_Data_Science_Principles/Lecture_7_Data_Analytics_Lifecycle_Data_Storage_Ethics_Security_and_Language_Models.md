## Part 1: Problem Transformation

### What is Problem Transformation?
**Problem transformation** is the process of converting a business problem into a clearly defined data science task by selecting the appropriate analytical technique.

In simple terms: you need to translate "what the business wants" into "what method the model will use."

Think of it like: ordering food at a restaurant. You know you're hungry (the business problem), but you have to pick from the menu (the category of techniques). You can't just say "I want something good" and expect a result.

Why it matters: without this step, data scientists can end up applying the wrong method to the wrong problem and producing results that are statistically valid but practically useless.

### Mapping Problems to Techniques
The first decision in any project is matching the business question to a class of analytical methods:
- If the goal is to group items by similarity or find structure in the data, **clustering** is appropriate.
- When the objective is discovering relationships between actions or items, **association rules** apply.
- To determine how input variables relate to an outcome, **regression** is the tool of choice.
- Assigning known labels to objects calls for **classification**. 
- When the data involves temporal patterns or forecasting, **time series analysis** is used.
- Text-heavy problems require **text analysis**.

The practical implication is that misidentifying the problem type is one of the most common and costly mistakes in data science projects. A team that frames a forecasting problem as a classification problem will never produce a useful model, regardless of the quality of their data.

### The HiPPO Effect
A related concept worth understanding here is the **HiPPO effect** (Highest Paid Person's Opinion). This refers to the tendency for authority figures' opinions to be treated as final truth and acted on even when the data contradicts them. Problem transformation is partly a discipline against this: by framing the problem analytically and testing hypotheses with data, the team creates an objective basis for decisions rather than deferring to hierarchy.

### Key Takeaways
1. Every business problem maps to a specific family of analytical techniques; the match must be made before any modelling begins.
2. Misclassifying the problem type is among the most impactful early mistakes a data science team can make.
3. Analytical framing protects against the HiPPO effect by replacing subjective authority with testable hypotheses.

**Remember this:** Before touching any data, know what kind of question you are actually trying to answer.

**One analogy to remember it by:** Problem transformation is the intake form at a hospital. Before any treatment starts, you describe your symptoms precisely so the right specialist and procedure can be selected.

**Quick note:**
![[Pasted image 20260604011121.png]]


---

## Part 2: Data Analytics Lifecycle

### What is the Data Analytics Lifecycle?
![[Pasted image 20260511193019.png]]
The **Data Analytics Lifecycle** is a six-phase **framework** developed by Dell EMC that defines best practices for carrying out a data science project from initial discovery through deployment and ongoing monitoring.

In simple terms: it is a structured roadmap that tells a data science team what to do, in what order, and when they are ready to move forward.

Think of it like: building a house. You do not start laying bricks before you have a blueprint, and you do not install the roof before the walls are up. The lifecycle prevents teams from jumping into modelling before the data and problem definition are ready.

Why it matters: the real world is chaotic, and so are its data problems. Without a guiding structure, teams often skip the foundational work and waste significant effort building models on poorly understood data. The lifecycle also provides measurable criteria for success at each stage.

### Core Principles
![[Pasted image 20260511193513.png]]
The lifecycle is explicitly iterative: teams may move forward or backward between most phases, and work can occur in multiple phases simultaneously. The most important discipline it enforces is that teams should not race through Phases 1 to 3 to get to the modelling work. Progress from one phase to the next is governed by a key question the team must be able to answer affirmatively before advancing.

### Phase 1: Discovery
**Discovery** is where the team learns the business domain, assesses available resources, and frames the problem as an analytical challenge **is called a framing problem**. The key activities are: 
1. Learning the business domain (to determine what domain knowledge the team needs).
	- ![[Pasted image 20260511194218.png]]
2. Assessing available resources (people, technology, tools, data, and time). 
	- ![[Pasted image 20260511194240.png]]
3. Framing the problem (writing a problem statement, identifying objectives, and establishing failure criteria).
	- ![[Pasted image 20260511194405.png]]
4. Identifying key stakeholders and their pain points.
	- ![[Pasted image 20260511194422.png]]
5. Interviewing the analytics sponsor to understand desired outcomes and constraints.
	- ![[Pasted image 20260511194440.png]]
6. Developing **Initial Hypotheses (IHs)** to test against the **data**.
	- ![[Pasted image 20260511194503.png]]
7. Identifying potential data sources.
	- ![[Pasted image 20260511194521.png]]

The readiness question for advancing to Phase 2 is: "Do I have enough information to draft an analytics plan and share it for peer review?"

### Phase 2: Data Preparation
![[Pasted image 20260511195739.png]]
**Data Preparation** requires establishing an analytic sandbox, a large isolated workspace (expected to be five to ten times the size of the original datasets) where the team can explore and transform data without affecting live production systems.

The central process here is **ETLT**, a combined approach drawing on both ETL (Extract, Transform, Load) and ELT (Extract, Load, Transform).
![[Pasted image 20260511195132.png]]
ETL is preferable when transformations must happen before loading, such as converting currencies and date formats from multiple source databases into a unified schema.

![[Pasted image 20260511195148.png]]
ELT is used when raw data is loaded first into the sandbox and transformations are applied afterward, which suits environments with powerful in-database processing.

The other key activities in this phase are: learning about the data (understanding acceptable value ranges, data entry errors, and building a dataset inventory), data conditioning (cleaning, normalising, merging datasets, and deciding what to keep or discard), and **surveying and visualising** the data to detect outliers, skewness, and distribution issues.

Shneiderman's visual analytics principle applies here: "Overview first, zoom and filter, then details-on-demand."

The readiness question is: "Do I have enough good quality data to start building the model?"

### Phase 3: Model Planning
**Model Planning** is where the team determines the methods, techniques, and workflow for the **subsequent model-building phase**. The two key activities are:
1. Data exploration and variable selection (understanding relationships among variables, questioning pre-existing assumptions from stakeholders, and selecting the **key predictors** (variable) rather than every possible variable)
2. **Model selection** (shortlisting analytical techniques based on the project's end goal and documenting modelling assumptions).

![[Pasted image 20260511200126.png]]
Common tools in this phase include R, SAS/Access, and SQL Analysis Services.

The readiness question is: "Do I have a good idea about the type of model to try? Can I refine the analytic plan?"

### Phase 4: Model Building
**Model Building** is where the team develops datasets for training, testing, and production, then builds and executes the models.
![[Pasted image 20260511200222.png]]
The training dataset is used to discover a predictive relationship; the test dataset, which must be independent and non-overlapping with the training set, is used to evaluate performance.

The team also assesses whether existing tools are sufficient or whether more robust infrastructure (parallel processing, faster hardware) is needed. The key questions guiding this phase include whether the model appears valid on test data, whether its parameter values make sense to domain experts, whether it meets accuracy requirements, and whether it can support runtime demands in production.

![[Pasted image 20260511200410.png]]
Tools span commercial packages (SAS Enterprise Miner, IBM SPSS Modeler, MATLAB)

![[Pasted image 20260511200440.png]]
Open-source options (R, Python with scikit-learn and pandas, WEKA, Octave).

### Phase 5: Communicate Results
**Communicate Results** is where the team, in collaboration with stakeholders, determines whether the project succeeded or failed against the criteria established in Phase 1. Importantly, failure is not treated as a true failure: it is viewed as the data's failure to confirm or reject a hypothesis, which is itself informative.

Key activities include comparing outcomes to success and failure criteria, determining statistical significance and validity, documenting the three most significant findings and their business implications, and reflecting on what can be improved in future iterations.

### Phase 6: Operationalise
**Operationalise** is the final phase, where the team delivers reports, code, and technical documentation, and moves the models from the sandbox into a production environment. Best practice is to begin with a limited **pilot deployment** (limited operational env) rather than a full enterprise rollout. This allows the team to manage risk, observe real-world model performance, and make adjustments before scaling.

Ongoing monitoring is essential: alerts should be designed to detect when model inputs fall outside the allowable range and when accuracy degrades, triggering retraining.

### Expected Outputs by Role
Each stakeholder group expects different outputs from a data science project.

|Role|Expected Output|
|---|---|
|Business User|Benefits and business implications of findings|
|Project Sponsor|Business impact, risk, ROI, and how to evangelise results|
|Project Manager|On-time, on-budget completion; goals met|
|BI Analyst|Impact on reports and dashboards|
|Data Engineer / DBA|Code and technical implementation documents|
|Data Scientist|Sharable code and model explanations for peers and management|

### Key Takeaways
![[Pasted image 20260511193019.png]]
1. The lifecycle is **iterative** and non-linear: teams may revisit earlier phases and work on multiple phases concurrently.
2. Phases 1 to 3 are often undervalued; skipping the foundational work makes modelling harder and less reliable.
3. Operationalisation begins with a pilot project, not a full deployment, to manage risk and enable fine-tuning.

**Remember this:** The lifecycle exists because real data science is chaotic; the framework provides the order and success criteria the chaos cannot provide on its own.

**One analogy to remember it by:** Think of the lifecycle as a product launch pipeline. Skipping discovery and planning is like releasing a product without market research; you might ship something technically impressive that nobody actually needs.

---

## Part 3: Data Storage

### What is Data Storage in Data Science?
**Data storage** in a data science context refers to the process of deciding what data to retain, in what format, and using which management system, in alignment with the analytical goals of the project.

In simple terms: before you can analyse data, you need to know where it lives and how to get it out.

Think of it like: choosing the right kind of filing system for a library. A library organised alphabetically by title is fine for novels but terrible for scientific papers that need to be searched by topic, method, or author. The filing structure needs to match how the material will be retrieved.

Why it matters: poor storage choices create bottlenecks in every downstream phase of the lifecycle, from data preparation through to model deployment.

### Data Representations
![[Pasted image 20260513172340.png]]
The fundamental unit in IBM PC is **bit**, which represents two states (0 or 1). Eight bits form one byte, giving 256 possible combinations. All data types are ultimately encoded in binary, though the structure and dimensionality of that encoding varies substantially by type.

![[Pasted image 20260513172504.png]]
**Text data** is one-dimensional, written in a limited symbol set, and encoded by standards like ASCII (7-bit, 128 characters) or Unicode (8, 16, or 32-bit, supporting multiple languages). Information is extracted via semantic analysis.

**Audio data** is one-dimensional and can be treated as time-series data. The amplitude of the signal corresponds to volume; the frequency corresponds to pitch. Audio signals contain a Direct Current (DC) component (a bias that affects volume level) and an Alternating Current (AC) component; the DC component is typically removed before signal6 analysis.

![[Pasted image 20260513172751.png]]
**Image data** is two-dimensional. The basic unit is a **pixel**. Digital images are subsampled from continuous real-world space. Some colour models separate brightness from colour channels (e.g., YCbCr); others process them together (e.g., RGB).

![[Pasted image 20260513172805.png]]
**Video and streaming data** is three-dimensional (two spatial dimensions plus time). Each frame is an image combined along a timeline. Like images, colour and brightness may be processed independently.

![[Pasted image 20260513172817.png]]
**3D image data** uses **voxels** (volume pixels) rather than pixels. Different layers can be retrieved by slicing along any axis.

![[Pasted image 20260513172836.png]]
**Trajectory data** is typically collected by GPS and logs the movement of an object as a series of points: latitude, longitude, and timestamp. It is most meaningful when combined with other data sources in analysis.

### Structured vs. Unstructured Data
![[Pasted image 20260513173032.png]]
**Structured data** is organised in tables with predefined columns and consistent data types. It is well-suited to relational databases like MySQL or PostgreSQL.

**Semi-structured data** does not follow a strict relational schema but contains markers such as XML or JSON tags to enforce some hierarchy and field separation.

**Unstructured data** has no predefined model or organisation. It is typically text-heavy and is stored in NoSQL databases such as MongoDB.

### Storage Strategy
Four considerations guide storage decisions: 
1. Identifying the analytical goal first (all subsequent storage choices depend on this).
2. Determining whether the project requires big data infrastructure or can operate at smaller scale.
3. Avoiding data fatigue (storing data that does not align with the goal wastes resources and introduces noise).
4. Choosing the appropriate management system (SQL for structured relational data, NoSQL for unstructured or flexible-schema data).

### Key Takeaways
![[Pasted image 20260513174039.png]]
1. All data types, regardless of format, are ultimately encoded in binary; understanding their dimensionality and structure is essential for choosing the right tools.
2. Structured, semi-structured, and unstructured data require different storage and querying systems.
3. Storage decisions should always begin with the analytical goal, not with available technology.

**Remember this:** Store data in the format and system that makes it easiest to retrieve and transform for the specific analytical task at hand.

**One analogy to remember it by:** Choosing a data store without a goal is like buying a warehouse before knowing what you will sell: you might end up with the wrong shelving, the wrong loading docks, and no climate control for the things that actually need it.

---

## Part 4: Ethics in Data Science

### What is Data Ethics?
**Data ethics** refers to the moral principles and values that govern the collection, use, and dissemination (phổ biến) of data, including consideration of the impact on individuals, society, and the environment.

In simple terms: just because you can collect and use data does not mean you should, or that you can do so without constraint.

Think of it like: a doctor's obligation to patient confidentiality. Even though a doctor has access to highly sensitive personal information, professional and legal obligations define strict limits on how that information can be shared or used.

Why it matters: as the volume of data collected by organisations grows, the potential for harm from its misuse scales proportionally. Trust, legal compliance, and individual rights all depend on ethical data practices.

### Core Ethical Considerations
- **Privacy** involves protecting personal information from misuse.
- **Consent** requires obtaining informed agreement from individuals before collecting or using their data.
- **Transparency** means being open about how data is collected, used, and shared.
- **Bias** requires identifying and mitigating systematic distortions in data collection and analysis.
- **Security** involves protecting data from unauthorized access.
- **Accountability** means taking responsibility for ethical use and addressing negative impacts when they occur.

### Ethics Approval Process
![[Pasted image 20260514160637.png]]
In research settings, any project involving data from human subjects or user behaviour requires ethics approval before the execution phase can begin. The standard process involves:
1. Familiarising the team with ethical principles (autonomy, beneficence, non-maleficence, and justice).
2. Developing a detailed research proposal that describes methods, risks, and consent procedures.
3. Submitting the proposal to an ethics committee or Institutional Review Board (IRB).
4. Waiting for review (which can take weeks to months)
5. Obtaining informed consent from participants once approved, and then conducting research in accordance with the approved protocol.

Informed consent requires that participants clearly understand the nature of the research, the associated risks and benefits, and their right to withdraw at any time without penalty.

### Key Takeaways
1. Data ethics is not optional: any project touching human or behavioural data requires formal ethics consideration and often institutional approval.
2. The six core dimensions of data ethics are privacy, consent, transparency, bias, security, and accountability.
3. Ethical failure risks reputation damage, financial loss, legal liability, and harm to individuals.

**Remember this:** Ethics approval is not bureaucratic (quan liêu) overhead; it is the mechanism by which the rights and welfare of participants are formally protected.

**One analogy to remember it by:** Ethics approval is a safety checklist before a flight. It feels slower than just taking off, but it is what prevents catastrophic and irreversible harm.

---

## Part 5: Data Security

### What is Data Security?
**Data security** is the protection of digital data from unauthorized access, use, disclosure, modification, or destruction, maintaining three core properties: 
1. **Confidentiality** (only authorised parties can access data).
2. **Integrity** (data is accurate and unaltered).
3. **Availability** (data is accessible when needed).

In simple terms: data security is the set of locks, cameras, and procedures that protect your data the same way a vault protects physical assets.

Think of it like: a bank's multilayered security system. There are locked vaults, access logs, surveillance cameras, ID requirements, and alarm systems. No single measure is sufficient; security requires multiple overlapping layers.

Why it matters: failure to implement adequate data security can result in data breaches, financial loss, regulatory penalties, and lasting damage to an organisation's reputation and the privacy of the individuals whose data it holds.

### Security Measures
- **Access controls** limit access to sensitive data by requiring authentication such as passwords, tokens, or biometric identification. 
- **Encryption** encodes sensitive data using algorithms that render it unreadable to unauthorized parties.
- **Network security** uses firewalls, intrusion detection and prevention systems, and other technologies to secure communications infrastructure.
- **Physical security** protects hardware devices that store or process sensitive data through physical access controls.
- **Data backup and recovery** ensures regular, secure copies are maintained so data can be restored after a breach or system failure.
- **Data retention policies** define how long data is kept and how it is disposed of in line with legal and regulatory requirements.

### Key Takeaways
1. Data security protects confidentiality, integrity, and availability; a breach in any one of these has serious consequences.
2. Effective security is never a single measure; it requires multiple overlapping controls across access, encryption, network, physical, backup, and policy dimensions.
3. The consequences of inadequate security include financial loss, legal liability, and reputational damage.

**Remember this:** Security is a system of overlapping layers, not a single lock; one control failing should not mean total exposure.

**One analogy to remember it by:** Data security is like a medieval castle: there is a moat, a drawbridge, a gatehouse, inner walls, and a keep. You do not rely on any single barrier.

---

## Part 6: Language Models, Tokens, and Embeddings

### What is a Language Model?
A **language model** is a system that predicts the next token in a sequence by estimating the probability of each possible continuation given all preceding tokens. Formally, it computes $P(w_t \mid w_1, w_2, \ldots, w_{t-1})$.

In simple terms: a language model is a very sophisticated "autocomplete" that learns from massive amounts of text which continuations are statistically most likely.

Think of it like: the way humans complete sentences. When someone says "I heard a dog ____", almost everyone answers "bark" without consciously reasoning through it. The model does the same thing, but mathematically, across billions of learned patterns.

Why it matters: language models underpin modern AI assistants, automated content generation, code completion tools, translation systems, and an increasingly wide range of decision-support applications in business and cybersecurity.

### What an LLM Learns
A **Large Language Model (LLM)** builds a structured probability landscape over language that encodes four types of information:
1. **Co-occurrence** (which words tend to appear together).
2. **Dependencies** (which words influence others across long distances in a sentence).
3. **Syntactic patterns** (structural grammatical rules).
4. **Semantic associations** (meaning relationships between words and phrases).

The model does not follow explicit rules; all of this is learned from statistical patterns in the training data.

### The LLM Pipeline
![[Pasted image 20260514180739.png]]
When a model processes text, it follows a fixed pipeline:
Token -> Embedding -> Self-Attention -> Probability Distribution

First, the input is broken into tokens. Each token is converted into a dense numerical vector called an **embedding**. The model then uses **self-attention** to model relationships between tokens across the sequence. Finally, it produces a probability distribution over all possible next tokens, from which a token is selected.

The critical point is that the model does not understand language the way humans do; it estimates probabilities based on learned statistical relationships.

### Key Takeaways
1. A language model is fundamentally a next-token probability estimator, not a reasoning engine in the human sense.
2. LLMs learn co-occurrence, dependency, syntactic, and semantic structure entirely from data, not from explicit rules.
3. The full pipeline is: Token → Embedding → Self-Attention → Probability Distribution → Selected Token.

**Remember this:** LLMs predict; they do not know.

**One analogy to remember it by:** A language model is like a very well-read detective who has read millions of mystery novels and can predict the next plot development with uncanny accuracy, not because they understand crime, but because they have seen every pattern before.

---

## Part 7: Tokens and Tokenization

### What is a Token?
A **token** is the smallest unit of text that a language model processes. A token is not the same as a word: it can be a full word, a subword, a prefix, a suffix, a number, a symbol, or punctuation. The model processes sequences of tokens, not sequences of words.

In simple terms: the model never sees sentences; it sees a stream of pieces.

Think of it like: morphemes (hình vị) in linguistics. Just as the word "unbelievable" can be broken into 3 morphmes "un-" (prefix), "believ-" (root), and "-able" (suffix), tokenisation finds the reusable sub-units that allow the model to handle new words it has not seen before.

Why it matters: the choice of tokenisation scheme directly affects vocabulary size, context window usage, computational cost, and even reasoning behaviour.

### Why Not Just Use Words?
Using whole words as units creates two serious problems. The first is vocabulary explosion: English alone contains hundreds of thousands of words, and new ones (like "COVID-19" or "ChatGPT") appear constantly. A word-level vocabulary would need to be enormous and would still fail on new terms. The second is the morphology problem: words like "play", "playing", "played", and "player" share a root but are treated as completely separate entries in a word-level vocabulary, bloating the vocabulary further and making generalisation harder.

Tokenisation solves both by breaking words into smaller, reusable sub-units. "barking" becomes ["bark", "ing"], allowing the model to learn the meaning of "bark" and "-ing" separately and combine them for any word with that structure.

### Subword Tokenisation Methods
Most modern LLMs use subword tokenisation. The three dominant approaches are:
1. **Byte Pair Encoding (BPE)**, where frequently co-occurring character pairs are iteratively merged into tokens.
2. **WordPiece**, which maximises the likelihood of the training data under the tokeniser vocabulary.
3. **Unigram Language Model**, which starts with a large vocabulary and prunes it probabilistically.

In all three, frequently occurring patterns become single tokens while rare words are broken into smaller pieces.

### Why Tokenisation Matters

|Factor|Effect|
|---|---|
|Vocabulary Size|Controls how many unique tokens the model knows|
|Context Window|Longer token sequences consume more of the available context|
|Computational Cost|More tokens means higher processing expense per inference|
|Reasoning Patterns|Different tokenisers can produce different model behaviour on the same input|

### Key Takeaways
1. Tokens are not words; they are subword units that balance vocabulary size against coverage of rare and novel terms.
2. Subword tokenisation solves both the vocabulary explosion and morphology problems.
3. Tokenisation choices have downstream effects on context window efficiency, cost, and model behaviour.

**Remember this:** The model never reads words; it reads a sequence of fragments it has learned to recognise.

**One analogy to remember it by:** Tokenisation is like how sheet music uses notes rather than full phrases. By breaking music into individual, reusable note-units, you can represent any song ever written or yet to be written with the same finite set of symbols.

---

## Part 8: Embeddings and Semantic Vector Space

### What is a Vector?
![[Pasted image 20260514221400.png]]
A **vector** is a mathematical object with two properties: **magnitude** (its size or length) and **direction** (where it points). You can think of it as an arrow in space. A short arrow pointing northeast and a long arrow pointing northeast have the same direction but different magnitudes. A short arrow pointing northwest has the same magnitude as the first but a completely different direction.

When written out, a vector looks like a list of numbers, like [0.12, -0.45, 0.88, 0.03]. Each number is a coordinate along one dimension of the space. In 2D you have two numbers (x, y). In embedding spaces, models typically use hundreds or thousands of dimensions, so the list is much longer, but the same principles apply.

![[Pasted image 20260514222230.png]]
In practice you cannot directly visualise it because embedding spaces have hundreds or thousands of dimensions, which is impossible for humans to perceive. What researchers do instead is use dimensionality reduction techniques to compress the high-dimensional space down to 2D or 3D while trying to preserve the relative distances between points, so you can plot them on a graph.

The reason direction matters more than magnitude in many applications is captured by **cosine similarity**: it measures the angle between two vectors rather than the distance between their tips. Two vectors pointing in nearly the same direction are considered similar regardless of how long they are. Think of two compasses: if both needles point roughly northeast, the compasses agree on direction even if one needle is twice as long as the other.

### What is an Embedding?
An **embedding** is a vector assigned to a token, where each number captures some aspect of the token's meaning, learned from training data. The full vector acts as the token's position in a high-dimensional meaning-space.

In simple terms: instead of labelling "dog" with an arbitrary ID like 2391, you describe it with a vector where the values reflect what it means and how it relates to other words.

Think of it like: plotting cities on a map using coordinates. Sydney sits at roughly (-33.9, 151.2) and Melbourne at (-37.8, 144.9), so they end up close on the map because they share many properties. An embedding gives tokens their own coordinates, so tokens with similar meanings end up close together in that space.

Why it matters: a raw token ID (2391) tells the model nothing about what "dog" means. An embedding like [0.18, -0.42, 0.91, ...] encodes that "dog" is close to "cat" and far from "car", making similarity measurable and useful.

### Why Token IDs Are Not Enough
![[Pasted image 20260514215532.png]]
A token ID is an arbitrary index in a vocabulary list. For example:
- "dog" has ID 2391
- "cat" has ID 15000
- "car" has ID 841
=> distance between "dog" and "car" = |2391 - 841| = 1550 (This carries no meaning whatsoever)

This does not indicate that "dog" and "car" are related, nor does the larger distance |2391 - 15000| = 12609 (for "cat") indicate that "car" is semantically closer to "dog" than "cat" is. Raw IDs tell the model nothing about animality, sound production, or any other semantic property.

### Meaning as Geometry
Once tokens are embedded, semantic relationships become spatial relationships. This is the central idea: the model does not store facts about words explicitly, it stores them implicitly as positions and distances in space.

Look at these three example vectors:
![[Pasted image 20260514215445.png]]
- dog = [0.18, -0.42, 0.91, ..., 0.03]
- cat = [0.20, -0.39, 0.88, ..., 0.05]
- car = [-0.70, 0.12, -0.55, ..., 0.40]

Notice that "dog" and "cat" have very similar values across most positions in their vectors. "car" looks completely different. This is not a coincidence: the model learned from training data that "dog" and "cat" appear in similar contexts (both are pets, both are animals, both follow words like "my" or "the"), so it pushed their vectors close together. "car" appears in entirely different contexts, so its vector ended up far away.

This is what "semantic relationships become spatial relationships" means: the question "are these two words similar?" becomes the geometric question "are these two points close together?"

Classic analogical relationships also emerge in embedding space. The relationship between "king" and "queen" mirrors the relationship between "man" and "woman", encoded as a consistent directional offset in the high-dimensional space.

The key insight: meaning becomes geometry. Language becomes spatial relationships.

### Key Takeaways
1. Embeddings transform arbitrary token IDs into dense vectors where geometric proximity encodes semantic similarity.
2. Cosine similarity is the standard metric for comparing tokens in embedding space.
3. Semantic structure (synonymy, analogy, category membership) emerges from the geometry of the learned embedding space.

**Remember this:** An embedding is not a label for a token; it is a position in a map of meaning.

**One analogy to remember it by:** Embedding space is like a city map of concepts. Nearby buildings (tokens) are semantically similar neighbours. "Cat" and "dog" are on the same street; "dog" and "democracy" are on opposite sides of the city.

---

## Part 9: Reasoning in Large Language Models

### What is Reasoning in LLMs?
**Reasoning** in the context of LLMs refers to the model's capacity to use existing information to derive new conclusions, going beyond simple next-token prediction to support decision-making, inference, and multi-step problem solving.

In simple terms: reasoning is what separates a model that generates plausible text from one that can actually solve problems.

Think of it like: the difference between a parrot that has memorised responses and a lawyer who can apply rules to new facts they have never seen before. Reasoning requires structure, not just pattern recall.

Why it matters: modern LLM applications in security, finance, medicine, and agentic systems demand more than fluent text generation. They require the model to detect phishing attempts, infer conclusions from structured rules, solve multi-step calculations, and make autonomous decisions under uncertainty.

### Types of Reasoning
![[Pasted image 20260514223033.png]]
- **Deductive reasoning** applies rules with certainty: if A implies B, and A is true, then B must be true. LLMs can apply this in straightforward logical chains, such as: "All employees must complete security training. Alice is an employee. Therefore Alice must complete the training." This is classic deductive inference with no ambiguity.
- **Mathematical reasoning** requires step-by-step calculation. Given "John has 3 apples, buys 5 more, and gives away 2", the model must chain: 3 + 5 = 8, then 8 - 2 = 6. The key challenge is maintaining accurate state across steps.
- **Commonsense reasoning** uses everyday world knowledge. Observing that the ground is wet and people are carrying umbrellas leads to the inference that it is probably raining, even without an explicit rule connecting these observations.
- **Strategic reasoning** involves adversarial or risk-aware inference. In phishing detection, the model identifies red flags such as urgency ("immediately"), threat framing ("account suspended"), and a call to action (click a link). 
	- In social engineering detection, patterns like an authority claim combined with a financial request and artificial urgency signal potential manipulation.
	- In agent (chủ động) (refer to Agentic AI like) decision-making, an instruction to "download and run this file" from an unknown source triggers a risk inference chain: unknown source equals risk; executing unknown files equals potential malware; therefore refuse.

### Techniques to Improve Reasoning
- **Chain-of-Thought (CoT)** prompting breaks the reasoning process into explicit steps, dramatically improving performance on multi-step tasks: Question → Step 1 → Step 2 → Final Answer. 
- **Few-shot reasoning** provides worked examples in the prompt to demonstrate the reasoning pattern the model should follow. 
- **Self-consistency** generates multiple independent reasoning paths and selects the most frequently arrived-at answer through a voting mechanism. 
- **Tool-augmented reasoning** connects the model to external resources such as calculators, search engines, or code executors, addressing limitations where the model itself is unreliable.

### Limitations
Despite progress to improve reasoning, four significant limitations persist:
1. **Hallucination** is the most critical: the model can generate confidently stated but entirely fabricated information. A model asked "Who invented the first email in 2005?" may produce a plausible-sounding but false answer, because it is pattern-matching rather than verifying against ground truth.
2. **Shallow reasoning** occurs when the model matches surface patterns rather than applying genuine logical structure. The classic example is the bat-and-ball problem: "A bat and ball cost $1.10 total. The bat costs $1 more than the ball. How much is the ball?" Pattern matching produces $0.10 (simply subtracting $1.00 from $1.10), while genuine algebra yields $0.05 (solving $x + (x+1) = 1.10$).
3. **Lack of true logic** means LLMs struggle with formal syllogisms. The prompt "All cats are animals. Some animals are robots. Are some cats robots?" often elicits/provoke (gợi ra) "Yes" from LLMs, when formal logic shows no overlap between the sets can be guaranteed, so no explicit conclusion can be made.
	- ![[Pasted image 20260514224916.png]]
4. **Sensitivity to prompt framing** means the same underlying question can produce opposite answers depending on how it is worded. "Is it safe to click links in emails?" yields a negative answer like "No, it can be risky", while "Can clicking links in emails be safe?" yields a positive one like "Yes, if the source is trusted", even though both questions address the same issue.

### Key Takeaways
1. Reasoning enables LLMs to go beyond pattern generation toward structured decision-making, but it remains probabilistic rather than formally guaranteed.
2. The four core reasoning types (logical, mathematical, commonsense, and strategic) each demand different model capabilities.
3. Chain-of-thought, few-shot, self-consistency, and tool augmentation are the primary techniques for improving reasoning quality.
4. Hallucination, shallow reasoning, formal logic failures, and prompt sensitivity are persistent limitations that must be accounted for in any real-world deployment.

**Remember this:** An LLM's reasoning is sophisticated pattern (khuôn mẫu) inference (suy luận), not formal proof (chứng minh hình thức step by step); treat its conclusions as hypotheses to verify (for you to verify 😭), not facts to accept blindly.

**One analogy to remember it by:** LLM reasoning is like a very experienced detective who has seen thousands of cases. They can make excellent inferences from incomplete evidence, but they are not infallible and can be misled by a cleverly framed question or a case that superficially (bề ngoài) resembles a familiar pattern but differs in a critical way like the bat and ball problem.