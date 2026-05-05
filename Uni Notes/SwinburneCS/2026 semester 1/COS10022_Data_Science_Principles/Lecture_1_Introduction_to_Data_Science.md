### 1. Whats is a Data Scientist

A **Data Scientist** is someone capable of analysing and interpreting complex digital data to assist a business in its decision-making.
:DD

### 2. Key Principles & Core Theory
**The Four Vs of Big Data**
![[image 1 7.png|image 1 7.png]]
Big Data are data sets that exceeds the processing capacity of conventional database systems.

| V            | Meaning    | Description                                            |
| ------------ | ---------- | ------------------------------------------------------ |
| **Volume**   | Scale      | Data at TB/PB levels - too large for traditional DBs   |
| **Velocity** | Timeliness | Data moves fast - batch, real-time, streams, near-time |
| **Variety**  | Diversity  | Structured, unstructured, semi-structured data         |
| **Veracity** | Accuracy   | Uncertainty and quality of the data                    |

**Data Structures (least to most complex)**
![[image 2 5.png|image 2 5.png]]
- **Structured** - Relational DBMS (tables, SQL)
- **Semi-Structured** - XML, JSON, CSV, RDF
- **Quasi-Structured** - Web clickstreams, emails, log files, social media
- **Unstructured** - Text documents, PDFs, images, video

**Data Science vs Business Intelligence**
![[b38c6e88-0b32-469b-9839-4023e90670a5.png]]

### 3. Examples & Applications
**Data Devices - Video Game:** A player on PC, PlayStation, Xbox, or smartphone generates player movement logs, session data, purchase history, and device telemetry. For every GB of game data, roughly a petabyte of metadata is generated about that interaction.

**Data Collectors - Streaming Provider:** A provider like Netflix tracks which shows a user watches, what they will or won't pay for on-demand, and their price sensitivity. This feeds recommendation algorithms and pricing models.

**Data Aggregators - Falcon:** Compiles social media data across Instagram, Twitter, and Facebook, calculates sentiment and benchmarks, and packages it as a product for corporate customers.

### 4. The Big Data Ecosystem
![[image 3 3.png|image 3 3.png]]

![[image 4 2.png|image 4 2.png]]

Data flows through four layers:
- **Data Devices** - Generate data passively (phones, GPS, ATMs, credit card readers, IoT sensors, medical imaging).
- **Data Collectors** - Gather and store data from devices and users (government bodies, analytic services, telecom companies).
- **Data Aggregators** - Compile, make sense of, and package data as a product (User Brokers, Catalog Co-ops, Information Brokers).
- **Data Users and Buyers** - Direct benefactors of aggregated data (corporate customers, advertising agencies, credit bureaus, banks, media archives).

**Types of Analytics**
- **Descriptive** - What happened? (BI territory)
- **Exploratory** - What patterns exist?
- **Predictive** - What will happen? (Data Science territory)
- **Prescriptive** - What should we do?

### 5. Comparisons
**Data Science Roles Skill Comparison**

|Role|Programming|Machine Learning|Data Wrangling|Statistics|Software Eng.|
|---|---|---|---|---|---|
|Data Analyst|Somewhat|Low|Somewhat|Somewhat|Low|
|ML Engineer|High|High|Somewhat|High|High|
|Data Engineer|High|Low|High|Somewhat|High|
|Data Scientist|High|High|High|High|Somewhat|

**Enterprise Data Warehouse (EDW) vs Analytic Sandbox**
An **Enterprise Data Warehouse (EDW)** is a centralised repository designed to store, manage, and facilitate access to large volumes of structured data from across an entire organisation. It integrates data from transactional systems, databases, and external sources to provide a unified view of the enterprise's data assets.

![[image 5 2.png|image 5 2.png]]

An **Analytic Sandbox** is a flexible, analyst-controlled workspace that resolves the conflict between what data scientists need and what a tightly governed EDW allows. It pulls data assets from multiple sources and technologies into a non-production environment where analysts can experiment freely without risking production systems.

|Feature|EDW|Analytic Sandbox|
|---|---|---|
|**Ownership**|DBA owned - changes and access require IT approval|Analyst owned - the data scientist controls their own workspace|
|**Environment**|Production - changes affect live systems and reporting|Non-production - experiments don't impact operational data|
|**Data Access**|Formal and governed - structured queries, strict permissions|Flexible and multi-source - pull from databases, files, APIs, streams|
|**Data Types**|Primarily structured, cleaned, and standardised data|Structured and unstructured - raw, messy, or experimental datasets|
|**Purpose**|BI and operational reporting - answering known business questions|Exploratory data analysis - discovering patterns and testing hypotheses|
|**Speed of Access**|Slower to provision - requires formal requests and processes|Fast to set up - analysts can spin up and iterate quickly|
|**Risk**|Low risk to data integrity due to strict controls|Reduces shadow file system risks while still allowing freedom|
|**Scalability**|High - designed for enterprise-wide, long-term data storage|Variable - scaled to the needs of a specific analysis or project|
|**Governance**|Strong data governance, security policies, and audit trails|Lighter governance - suited for exploration before formalising results|
|**Typical Users**|DBAs, BI developers, business analysts running standard reports|Data scientists and analysts running experimental or ad hoc analyses|

**When to use which:**
- Use the EDW when you need reliable, governed, production-quality data for business reporting and dashboards.
- Use the Analytic Sandbox when you are exploring new data, prototyping models, or working with data sources that haven't been integrated into the EDW yet.

### 6. Real-World Applications
- **Finance:** Banks detect fraud in real time by analysing transaction velocity, geolocation mismatches, and behavioural patterns. Predictive models flag anomalies before a fraud is confirmed.
- **Healthcare:** Medical imaging and gene sequencing combined with machine learning enables early disease detection. Smart wearables continuously feed health monitoring platforms.
- **Retail:** Clickstreams, purchase history, and search queries feed personalisation engines that optimise product recommendations, pricing, and inventory.
- **Transport:** Myki tap data, GPS feeds, and IoT sensors let governments model commuter behaviour, predict demand spikes, and optimise transit scheduling.
- **Expert Insight:** The Data Science Venn Diagram places the ideal data scientist at the intersection of Computer Science, Machine Learning, and Math/Statistics - but also requires Subject Matter Expertise. Without domain knowledge, even technically perfect models can mislead.