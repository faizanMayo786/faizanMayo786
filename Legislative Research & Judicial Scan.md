# Canada Copyright Risk Matrix for Open Data & Commercialization: Legislative Research & Judicial Scan

## Executive Summary

This research scan documents the Canadian legal framework governing copyright enforcement in open data and web scraping contexts, synthesizing federal legislation, statutory orders, open licensing frameworks, and a comprehensive matrix of 16 reported judicial decisions spanning 2007–2025. The analysis reveals that **website terms of service (browsewrap and clickwrap agreements) are enforceable in Canada**, **statutory damages for copyright infringement range from $100–$20,000 per work depending on commercial vs. non-commercial purpose**, and **Crown copyright protection extends for 50 years post-publication under section 12 of the Copyright Act**. Pending cases involving major AI-driven platforms (Caseway AI, OpenAI) signal an emerging enforcement trend: Canadian courts increasingly recognize republishing and machine-readable bulk scraping of legal decisions, court data, and proprietary databases as potential copyright infringement and breach of service terms, despite arguments that such content is publicly available.[1][2][3][4][5][6][7]

***

## I. Foundational Legislative Framework

### A. Copyright Act: Statutory Damages & Technological Protection (Sections 38.1, 41)

The Canadian Copyright Act, RSC 1985 c. C-42, establishes a **dual-tier statutory damages regime** that forms the cornerstone of copyright enforcement strategy in Canada:[2][8][1]

**Commercial Infringement (Section 38.1(1)(a)):** Rights holders may elect statutory damages between **CAD $500 and CAD $20,000 per work** infringed. Courts apply proportionality analysis under section 38.1(3): where multiple works are infringed in a single medium and even the minimum award would be "grossly out of proportion," courts may reduce damages below $500 (but not below $200 if the defendant was unaware of infringement).[8][1][2]

**Non-Commercial Infringement (Section 38.1(1)(b)):** Where infringement is for non-commercial purposes, statutory damages range from **CAD $100 to CAD $5,000 across all works**. This lower tier acknowledges reduced harm in non-profit contexts but does not eliminate liability.[2][8]

The Federal Court's interpretation in *Afterlife Network* precedent applied section 38.1(5) factors—including defendant good faith/bad faith, litigation conduct, and deterrence necessity—to assess quantum within statutory bounds. A notable 2009 Federal Court decision awarded statutory damages at **CAD $150 per work (totaling CAD $301,350) across 2,009 infringed television programs**, reflecting courts' sensitivity to commercial bad faith and systematic infringement.[8]

**Technological Protection Measures (Section 41):** Section 41 of the Copyright Act, in force since November 7, 2012, prohibits circumvention of **any effective technology** that controls access to or restricts copying of copyright-protected works. The definition encompasses digital locks, DRM systems, and access controls. Critically, the Act provides **no general fair-dealing exception to TPM circumvention**; even lawful uses cannot justify breaking a TPM without authorization. Violating TPM prohibitions triggers all remedies available for copyright infringement, though damages may be reduced if the defendant was unaware of the TPM violation.[9][10][11]

### B. Reproduction of Federal Law Order (SI/97-5): Public Domain Carve-Out

The Reproduction of Federal Law Order, P.C. 1996-1995 (Registration 1997-01-08), is a **foundational policy instrument** that grants broad reproduction rights while preserving accuracy and attribution norms:[12][13]

**Scope:** Any person may reproduce, **without charge or permission**, all federal enactments, consolidations of federal law, and decisions of federally-constituted courts and administrative tribunals. This includes reasons for judgment and tribunal decisions.[13][12]

**Conditions:** Reproduction must comply with three conditions: (1) **due diligence in ensuring accuracy**; (2) **clear designation that the reproduction is not an official version**; and (3) **non-representation as authoritative**. The Order does not permit derivative works styled as "official" nor does it authorize republication of personal data embedded in decisions (privacy law may override).[12][13]

**Significance for Scraping:** The Order's explicit permission for court and tribunal decisions is material to projects bulk-downloading reported decisions. However, commercial republication that embeds personal information (e.g., litigant names, addresses, financial details) may violate **PIPEDA** (Personal Information Protection and Electronic Documents Act), as demonstrated in *A.T. v. Globe24h.com* (2017 FC 114).[14][15][16]

### C. Open Government Licence – Canada (OGL-C): Commercial Reuse Framework

The OGL-C, last updated December 2, 2022, provides a **worldwide, royalty-free, perpetual, non-exclusive licence** for reuse of Canadian Crown information, including for commercial purposes. The licence aligns with Creative Commons Attribution 4.0 International (CC-BY 4.0) and applies to all data published by federal, provincial, territorial, and municipal public bodies that formally adopt it.[17]

**Key Obligations:** Users must (1) **acknowledge the source** of licensed information and (2) **adhere to exclusions** (third-party materials, personal information, information subject to confidentiality statutes). The licence does not require written permission and operates on an "acceptor pays" model: use of the information constitutes acceptance. Unlike the Reproduction of Federal Law Order, the OGL-C does not apply automatically; Crown bodies must expressly adopt and publish datasets under the licence.[17]

### D. Statistics Canada Open Licence: Mandatory Attribution, Unlimited Commercial Use

Statistics Canada's Open Licence (effective October 22, 2018, last updated July 10, 2025) grants a **worldwide, royalty-free, non-exclusive licence to reproduce, publish, distribute, and sell** Statistics Canada information for both commercial and non-commercial purposes. No signature is required; use constitutes acceptance.[18][19]

**Mandatory Condition:** Users must **always acknowledge Statistics Canada as the source** in any republished information. The licence applies to all free and priced Statistics Canada products (standard, custom, public-use microdata files) regardless of format or reference year.[19][18]

**Commercial Rights:** Explicitly permitted uses include (1) selling Statistics Canada data "as is"; (2) selling value-added products created from Statistics Canada data; and (3) sublicensing rights on terms consistent with the licence. Statistics Canada retains the right to terminate the licence only if users violate terms and fail to remedy the breach within a specified remediation period.[18][19]

### E. Copyright Term Extension: Life + 70 Years (Effective December 30, 2022)

On December 30, 2022, amendments to the Copyright Act (via Bill C-19, Budget Implementation Act 2022 No. 1, Royal Assent June 23, 2022) extended the general copyright term from **life of author + 50 years to life + 70 years**, aligning Canada with the United States, Mexico, and the European Union under CUSMA obligations.[20][21][22]

**Material Exception:** The term extension **does not revive copyrights already in the public domain** by December 30, 2022. Accordingly, works in which copyright had already expired retain public-domain status; only works with subsisting copyright benefit from the 20-year extension. Section 6 of the Copyright Act now reads: "the life of the author, the remainder of the calendar year in which the author dies, and a period of **70 years** following the end of that calendar year". This extension may indirectly broaden fair-dealing defenses and reverse rights, as courts may recalibrate copyright's balance between rights-holders and users.[21][22][20]

***

## II. Crown Copyright: Section 12 Framework & Supreme Court Interpretation

### A. Statutory Definition & Dual-Branch Test

Section 12 of the Copyright Act states:

> "Without prejudice to any rights or privileges of the Crown, where any work is, or has been, prepared or published by or under the direction or control of Her Majesty or any government department, the copyright in the work shall, subject to any agreement with the author, belong to Her Majesty and in that case shall continue for the remainder of the calendar year of the first publication of the work and for a period of **fifty years** following the end of that calendar year."[3][4][5]

Crown copyright is time-limited (50 years post-publication, materially shorter than the extended 70-year private copyright term) and applies only to works prepared or published under Crown direction or control.[4][5][3]

### B. *Keatley Surveying Ltd. v. Teranet Inc.* (2019 SCC 43): Seminal Supreme Court Ruling

In September 2019, the Supreme Court of Canada provided the first comprehensive modern interpretation of section 12 in *Keatley Surveying Ltd. v. Teranet Inc.*, 2019 SCC 43, a certified class action by Ontario land surveyors challenging Crown copyright in survey plans registered with Ontario's electronic land registry system.[23][24][25]

**Facts:** Private surveyors created plans of survey (technical property maps) and deposited them in Ontario's provincial land registry, operated under contract by Teranet Inc.. Ontario claimed Crown copyright in the plans, licensing Teranet to digitize and republish them.[24][23]

**Majority Holding (Abella J., with Moldaver, Karakatsanis, Martin JJ.):** The majority adopted a **two-part test requiring Crown direction/control over both (1) the person preparing/publishing the work and (2) the work itself**. Relevant indicia of Crown control include: (a) statutory schemes transferring property rights in works to the Crown; (b) strict statutory controls on form and content; (c) Crown physical possession; (d) exclusive Crown authority to modify; (e) opt-in statutory schemes; and (f) Crown obligation to make works publicly available.[3][4][23][24]

**Application:** The majority found that Ontario's land registration regime satisfied both prongs: statutory law required surveyors to deposit plans; Ontario determined form, content, and validation; only the Examiner of Surveys could alter plans post-registration; and Ontario alone had statutory authority to make copies and authorize republication. Accordingly, Crown copyright subsisted in the survey plans.[23][24]

**Dissent (Côté, Brown JJ., with Wagner C.J.):** The minority agreed in outcome but rejected the two-part test, arguing that section 12's language ("prepared or published by") requires direct Crown creation, not mere direction/control. The dissent would have confined Crown copyright to works the Crown actually created itself.[24][23]

**Judicial Significance:** *Keatley* is binding authority for all Canadian courts and establishes that government control over publication process—distinct from authorship—suffices for Crown copyright in Canada. For scraping projects targeting government data, this implies that provinces and federal Crown bodies claiming copyright in curated or aggregated datasets may overcome ownership challenges by demonstrating statutory or regulatory control over publication.[25][4][3][23][24]

***

## III. Contractual Enforceability of Website Terms: Browse-Wrap & Click-Wrap Agreements

### A. Browse-Wrap Enforceability: *Century 21 v. Zoocasa* (2011 BCSC 1196)

*Century 21 Canada Ltd. v. Rogers Communications Inc.* (2011 BCSC 1196) is the leading Canadian authority on **browse-wrap agreement enforceability**. Century 21 operated a real estate listing website with prominently posted Terms of Use prohibiting automated scraping, indexing, and framing. Zoocasa, a vertical search engine owned by Rogers Communications, systematically extracted Century 21 listings, photos, and descriptions without permission, despite warnings from Century 21 lawyers.[6][7][26]

**Court's Holding:** Justice Groh held that browse-wrap agreements **can be enforceable contracts** if the user has **adequate notice and fair opportunity to review terms**. Key factors included: (1) Century 21 posted Terms of Use prominently on every page; (2) Zoocasa acknowledged knowing of the terms by Fall 2008 (when warned by Century 21 counsel); and (3) Zoocasa continued accessing the site after notice, thereby accepting the terms through continued performance.[7][26][6]

**Remedies:** The court granted Zoocasa a **permanent injunction** preventing further access to Century 21's website in breach of terms and awarded **nominal damages of CAD $1,000 for breach of contract** (since Century 21 itself suffered no quantifiable loss, though breach was clear). Individual broker plaintiffs who owned copyright in listings recovered **statutory damages of CAD $250 per work (totaling CAD $32,000)**, reduced from the minimum CAD $500 per work due to proportionality under section 38.1(3) of the Copyright Act.[26][6]

**Legal Principle:** Browse-wrap enforceability does not require an affirmative "I Agree" click; rather, it requires (a) conspicuous notice, (b) reasonable accessibility of full terms, and (c) awareness by the user that access means acceptance. This test is **less stringent than click-wrap** (which requires express click-through acceptance) and reflects Canadian courts' preference for flexibility in online commerce.[27][6][7]

### B. Click-Wrap Enforceability: Broad Canadian Consensus

While fewer Canadian cases address click-wrap agreements directly, consensus case law (*Rudder v. Microsoft*, 1999; various Quebec decisions) establishes that **click-wrap agreements are generally enforceable once a user clicks "I Agree"**. The rationale: express assent eliminates ambiguity about whether the user agreed to non-negotiable terms.[28][27]

Ontario courts have held that users cannot later claim ignorance of terms merely because they did not read them (*Rudder v. Microsoft*). The enforceability doctrine balances certainty in electronic commerce against consumer protection concerns.[27]

### C. Limits on Enforceability: Unconscionability, Inequality of Bargaining Power, & Public Policy

Canadian courts, particularly the Supreme Court, have begun to impose **equitable limits on online terms enforceability** in consumer contracts, especially where terms conflict with statutory privacy or quasi-constitutional rights.

**Douez v. Facebook Inc. (2017 SCC 33):** Facebook's Terms of Use included a forum-selection clause requiring all disputes be resolved in California. Ms. Douez, a British Columbia resident, sued under BC's *Privacy Act* (quasi-constitutional statute) after Facebook used her name and likeness without consent for advertising. Facebook moved to stay the action based on the forum-selection clause.[29][30][31]

A 3-1-3 Supreme Court decision declined to enforce the clause. The plurality (Karakatsanis, Wagner, Gascon JJ.) held that public policy concerns—namely, gross inequality of bargaining power between a global tech corporation and an individual consumer, combined with the quasi-constitutional status of privacy rights—constituted "strong cause" to refuse enforcement under the *Pompey* test. Justice Abella concurred on broader grounds, suggesting forum-selection clauses in consumer contracts generally merit heightened scrutiny.[30][29]

**Implications for Scraping Terms:** *Douez* signals that even browse-wrap terms restricting dispute resolution or limiting liability may be challenged in Canadian consumer contexts where statutory or quasi-constitutional rights are implicated (e.g., privacy, data protection, consumer protection).[31][29]

***

## IV. Web Scraping & Copyright Infringement: Judicial Risk Matrix

### A. *Trader Corporation v. CarGurus, Inc.* (2017 ONSC 1841): Modern Scraping Liability

In 2017, Ontario Superior Court Justice Conway decided *Trader Corporation v. CarGurus, Inc.*, 2017 ONSC 1841, establishing **modern doctrines for copyright in scraped photographs and the "making available" right**.[32][33][34]

**Facts:** Trader Corporation operates autotrader.ca and employs photographers to capture standardized images of vehicles at dealerships. Trader owns copyright in ~152,500 photographs. CarGurus, a competitive service, scraped these photos from dealer websites and republished them on its site and mobile app, sometimes via "framing" (embedding images stored on remote servers without downloading).[33][34][32]

**Copyright Ownership:** CarGurus argued that Trader's photographers followed standardized procedures, eliminating copyright protection. Justice Conway rejected this: **skill and judgment in angle, composition, and timing suffice for copyright protection, even if procedures are standardized**. The photographers exercised sufficient artistic judgment despite process standardization.[32][33]

**Infringement: Reproduction & Making Available:** CarGurus infringed Trader's copyright in two ways: (1) **direct reproduction** by downloading and storing images on CarGurus servers; and (2) **making available to the public** by framing images stored on dealers' servers—a right expressly added to the Copyright Act in 2012 to clarify that telecommunication includes embedding remote images.[34][33][32]

**Fair Dealing Defense:** CarGurus argued its use was "fair dealing" for research purposes. Justice Conway rejected this, finding that while end-users might fairly research listings via CarGurus' interface, **CarGurus' own use was for direct commercial competition, not research**. CarGurus had alternatives (e.g., requesting licenses from Trader) and its dealing exceeded one-time fair research; it was systematic daily scraping.[33][32]

**Statutory Damages & Quantum:** The statutory minimum for commercial infringement is CAD $500 per work, yielding potential damages of CAD $76.3 million on 152,523 photos. However, Justice Conway applied proportionality under section 38.1(3), reducing damages to **CAD $2 per photo (totaling CAD $305,064)**, considering: (a) CarGurus received legal advice and followed its US business model; (b) Trader's initial complaint did not highlight the capture service (5% of photos); (c) CarGurus removed images once aware of copyright claims; (d) license fees would have been CAD $17,535; (e) actual production costs to Trader were < CAD $119,000; and (f) CarGurus generated no profits in Canada and Trader suffered no lost sales.[34]

**Key Risk Factors:** Even with proportionality reductions, scraping carries significant liability exposure (tens of thousands in statutory damages). The "framing" doctrine expands liability beyond direct copying to include embedding remote content without express license.[32][34]

### B. *A.T. v. Globe24h.com* (2017 FC 114): Privacy Law Overlay on Republishing

*A.T. v. Globe24h.com*, 2017 FC 114, is a **landmark decision extending Canadian privacy law (PIPEDA) to foreign websites targeting Canadian audiences** and republishing court/tribunal decisions without consent.[15][16][14]

**Facts:** A Romanian website operator republished Canadian court and tribunal decisions (freely available on CanLII and court websites) to Globe24h.com, indexing them for search engines. The site then charged individuals fees to remove their personal information (divorce proceedings, bankruptcy, immigration details) from search results—a business model analogous to extortion.[16][14][15]

**Jurisdiction & Extraterritorial Application:** Globe24h.com operated from Romania, but the Federal Court held that **PIPEDA has extraterritorial reach** where activities target Canadians with a real and substantial connection to Canada. The website specifically advertised itself as providing "Canadian Caselaw" and generated revenue from Canadian advertising and removal fees.[14][15][16]

**PIPEDA Violation:** The court found the respondent (1) **collected, used, and disclosed personal information** from court decisions; (2) did so for **commercial purposes** (advertisements, removal fees); and (3) failed all PIPEDA exceptions: the activities were not "exclusively journalistic" (no analysis added; profit motive was removal fees, not journalism), not "appropriate" (republishing served no judicial or journalistic purpose, merely needless exposure), and did not fall under the "publicly available" exception (republished decisions served different purposes than the court's, undermining administration of justice).[15][16][14]

**Remedy:** The Federal Court ordered the respondent to (1) **remove all Canadian court/tribunal decisions containing personal information** from Globe24h.com; (2) **de-index those decisions from search engine caches**; (3) pay damages of CAD $5,000 plus costs; and (4) comply with corrective orders permitting affected individuals to request removal from Google and other search engines.[16][14][15]

**Risk for Scraping:** This decision **establishes that republishing court decisions for profit, even if the underlying decisions are publicly available, may violate PIPEDA if personal information is embedded and commercial intent is present**. The "public availability" defense is limited; recontextualization for profit undermines the exception.[14][15][16]

### C. *Thomson v. Afterlife Network Inc.* (2019 FC 545): Obituary Scraping & Statutory Damages Awards

*Thomson v. Afterlife Network Inc.*, 2019 FC 545, awarded the **largest statutory damages judgment in Canadian copyright history**: CAD $20 million for scraping and republishing obituaries.[35][36][37]

**Facts:** Afterlife Network operated a website republishing over one million obituaries and photographs copied from Canadian funeral homes' and newspapers' websites without consent. The platform sold flowers, virtual candles, and hosted third-party advertising alongside obituaries. Ms. Thomson authored her father's obituary and took accompanying photographs, which Afterlife republished without permission, associating them with commercial advertising.[36][37][35]

**Copyright & Moral Rights:** Justice Phelan found that obituaries and photographs constituted original works eligible for copyright protection; authorial skill and creativity in writing and composing photographs sufficed. However, the court did **not find moral rights infringement**: while Ms. Thomson sincerely believed Afterlife's association with advertising harmed her honour and reputation, she provided **no objective evidence** of reputational prejudice. Moral rights require both subjective belief and objective harm.[35][36]

**Statutory Damages Award:** Though Afterlife infringed copyright in ~2 million works (obituaries + photos), Justice Phelan awarded **CAD $20 million in aggregate statutory and aggravated damages**, averaging ~CAD $10 per work. The court emphasized Afterlife's **systematic commercial appropriation** (generating revenue from advertising and flower sales), bad faith (Afterlife did not respond to litigation or remove content upon notice), and the need for **deterrence** against online copyright piracy.[37][36][35]

**Significance:** This decision signals that **commercial scraping of creative works (obituaries, photographs) for republication with advertising generates substantial statutory damages exposure, even if per-work awards are reduced from statutory maxima**. The decision deters "copy now, defend later" business models.[36][37][35]

***

## V. Emerging AI-Era Disputes: CanLII v. Caseway AI & OpenAI Litigation

### A. *CanLII v. Caseway AI et al.* (Pending, 2024 BC Supreme Court)

On November 4, 2024, the Canadian Legal Information Institute (CanLII), a nonprofit providing free access to Canadian court decisions and legislation, filed a civil claim in BC Supreme Court against Caseway AI, alleging bulk scraping of its legal database.[38][39][40][41]

**Allegations:** CanLII alleges that Caseway AI (an Irish AI-driven legal research chatbot launched September 2024) "coordinated the bulk and systematic download and scraping" of CanLII's content, violating its Terms of Use and infringing copyright. CanLII claims Caseway downloaded **3.5 million records and over 120 gigabytes of CanLII-enhanced material** (summaries, citation tracking, structured data) to train its chatbot.[39][41][38]

**CanLII's Copyright Theory:** CanLII argues that while underlying court decisions are public, **CanLII's curated, enhanced versions—including summaries, case facts, procedural history, and citation linkages—constitute original compilations** subject to copyright. CanLII claims its works were "reviewed, curated, catalogued and enhanced" at significant cost and expense.[41][38][39]

**Caseway's Defense:** Caseway's founder, Alistair Vigier, denies using CanLII data, asserting that Caseway sources only "pure text directly from court records without modifications". Vigier argues that court documents are public record, not owned by CanLII, and that "numerous other websites also make these decisions available". He further suggests the lawsuit runs counter to CanLII's stated mandate of open legal access.[40][38][39]

**Jurisdictional Challenge:** Caseway has signaled intent to challenge BC Supreme Court jurisdiction, noting that Caseway is an Irish entity.[38][40]

**Current Status:** Litigation is pending as of November 2025; no decision has been rendered.[39][41]

**Risk Implications for Scraping Legal Databases:** The case raises unresolved questions: (1) **Does copyright subsist in curated compilations of public court decisions?** (2) **Does the Reproduction of Federal Law Order limit Crown copyright claims and therefore CanLII's derivative rights?** (3) **Is bulk scraping of public legal data a permissible alternative to licenced access?** CanLII's pursuit suggests major legal databases will increasingly litigate unauthorized scraping, even of publicly available information.[40][38][39]

### B. *Canadian Publishers v. OpenAI* (Pending, Ontario Superior Court, Filed November 28, 2024)

Canadian news publishers (Torstar, Postmedia, Globe & Mail, CBC/Radio-Canada, Canadian Press) filed a class action against OpenAI on November 28, 2024 in Ontario Superior Court, alleging that OpenAI unlawfully used their news content to train ChatGPT.[42][43][44]

**Claims:** Publishers allege copyright infringement, breach of terms of use, TPM circumvention, and unjust enrichment. They claim OpenAI: (1) scraped news articles without licence; (2) disregarded robots.txt exclusion protocols, copyright notices, and paywalls; (3) violated website terms of use (restricting use to "personal, non-commercial use"); and (4) used "Retrieval-Augmented Generation" (RAG) to provide models with "continuous access" to updated datasets, extending infringement beyond initial training.[43][44][42]

**Damages Sought:** Publishers seek up to CAD $20,000 per article scraped (since 2015), potentially totaling billions; profits derived from infringing use; and injunctions preventing future content use.[44][42][43]

**OpenAI's Motion to Dismiss:** OpenAI has argued (filed September 2025, motion hearing upcoming) that (1) all training occurred outside Ontario and thus Ontario courts lack jurisdiction; (2) US copyright law, not Canadian, is the "most directly implicated" legal framework; and (3) appellate decisions on fair use in other jurisdictions should guide Canadian courts.[42]

**Current Status:** OpenAI has sought a sealing order for documents describing "business dealings, AI systems, model training and inference processes" (granted July 2025). The case remains in the motion/jurisdictional phase.[42]

**Risk Implications for AI Scraping:** If Canadian publishers succeed, they establish that **large-scale scraping for AI model training can constitute copyright infringement under Canadian law, notwithstanding "fair use" or "fair dealing" arguments**. OpenAI's jurisdictional defense suggests US-based tech companies will initially challenge Canadian courts' authority; however, if courts assert jurisdiction based on "real and substantial connection" (following *A.T. v. Globe24h.com*), Canadian copyright law will apply to training data scraped from Canadian news websites.[44][42]

***

## VI. Contract Enforceability in Platformized Disputes: Unconscionability & Arbitration

### A. *Uber Technologies Inc. v. Heller* (2020 SCC 16): Unconscionable Arbitration Clauses

In 2020, the Supreme Court unanimously invalidated Uber's mandatory arbitration clause in *Uber Technologies Inc. v. Heller*, 2020 SCC 16, on grounds of **unconscionability**.[45][46][47]

**Facts:** Uber required drivers to sign standard-form service agreements containing a mandatory arbitration clause requiring disputes be submitted to arbitration in the Netherlands under UNCITRAL rules, with arbitration costs exceeding USD $14,500 in advance.[46][47][45]

**Unconscionability Doctrine:** Seven of eight justices held the clause unconscionable under the doctrine requiring proof of (1) **significant inequality of bargaining power** and (2) **an improvident or unfair bargain**. Drivers, typically individuals with minimal education, could not reasonably understand the arbitration clause's implications, creating a "$14,500 hurdle to relief". The cost combined with mandatory travel to Netherlands rendered the clause unconscionable.[47][45][46]

**Significance for Online Terms:** *Heller* establishes that even negotiated-in-advance arbitration clauses in standard-form service agreements can be struck down in Canadian consumer/employment contexts where costs and complexity create unconscionable barriers to justice. A single dissenting justice (*Brown J.*) would have grounded the result in public policy rather than unconscionability doctrine, but the majority view prevails.[45][46][47]

### B. *Douez v. Facebook Inc.* (2017 SCC 33): Forum-Selection Clauses & Quasi-Constitutional Privacy

As discussed above, *Douez* established limits on forum-selection clauses in consumer online contracts involving statutory privacy rights. The decision implies that scraping-related service-of-process and dispute-resolution provisions in website terms will receive judicial scrutiny if they would effectively deprive consumers or affected individuals of remedies for quasi-constitutional violations.[29][30][31]

***

## VII. Statutory Damages, Proportionality, & Judicial Discretion: Quantum Patterns

Based on the judicial matrix, Canadian courts employ **discretionary proportionality analysis** under Copyright Act section 38.1(3) to moderate statutory damages awards when the prescribed range would be "grossly out of proportion" to infringement.[1][6][26][8][34]

| **Case** | **Year** | **Works Infringed** | **Statutory Range** | **Court Award** | **Per-Work Average** | **Key Modulating Factors** |
|---|---|---|---|---|---|---|
| **Afterlife** | 2019 | ~2,000,000 (obituaries + photos) | $500–$20,000/work = $1–40B | $20,000,000 | ~$10 | Systematic commercial infringement; bad faith; deterrence |
| **Trader v. CarGurus** | 2017 | 152,523 (photos) | $500–$20,000/work = $76–3,050M | $305,064 | $2 | CarGurus received legal advice; Trader's complaint incomplete; removal post-notice; low market license cost |
| **Century 21 v. Zoocasa** | 2011 | 128 (listings + descriptions) | $500–$20,000/work | $32,000 | $250 | Browse-wrap enforceability established; proportionality applied; injunction granted |
| **Canwest Global** (2009 Federal Court precedent) | 2009 | 2,009 (television programs) | $500–$20,000/work | $301,350 | $150 | Bad faith; litigation misconduct; deterrence; commercial infringement |

**Judicial Pattern:** Courts reduce from statutory maxima ($500–$20,000/work) where: (1) **aggregate awards would be grossly out of proportion**; (2) **defendant-side conduct includes some awareness/partial remediation**; (3) **licencing alternatives exist and are relatively inexpensive**; and (4) **actual market harm to plaintiff is modest**. Conversely, courts maintain higher per-work awards where commercial bad faith, systematic infringement, and deterrence necessity are evident.[6][26][37][1][8][34][35][36]

***

## VIII. Open Data Legal Principles & Reconciliation with Copyright

Canada's government open-data framework, embodied in the OGL-C, Statistics Canada Open Licence, and Reproduction of Federal Law Order, establishes a **hierarchy of permissiveness** that does not eliminate copyright but restructures it:

1. **Reproduction of Federal Law Order:** Blanket permission for federal legislation, regulations, and court/tribunal decisions (no licence required; attribution and accuracy suffice).[13][12]

2. **OGL-C & Statistics Canada Licences:** Broad commercial reuse rights, subject to attribution and exclusions (third-party content, personal information, confidential data).[19][17][18]

3. **Crown Copyright (Section 12):** 50-year protection for government-prepared/-controlled works; shorter than private copyright (70 years) but still restrictive.[5][4][3]

**Scraping Risk Matrix:** Projects republishing publicly available Crown data should: (a) **identify the specific statutory/regulatory source** (federal law order? OGL-C? Statistics Canada?); (b) **comply with attribution requirements**; (c) **exclude personal information** (PIPEDA); (d) **respect TPM controls** if present; and (e) **monitor service terms** (e.g., website ToS may restrict bulk downloading despite underlying legal permission).[17][18][19][14]

***

## IX. Synthesis: Risk Matrix for Commercial Open Data Reuse

| **Pressure Point** | **Legal Authority** | **Enforcement Risk** | **Mitigation Strategies** |
|---|---|---|---|
| **Website ToS / Browsewrap Restrictions** | *Century 21 v. Zoocasa*; *Trader v. CarGurus* | **HIGH** – Browse-wrap enforceable if notice given; injunctions + $500–$20K statutory damages/work | Seek explicit licence; monitor robots.txt; respect automatic tools; implement rate-limiting |
| **Copyright in Compiled/Curated Data** | *Keatley v. Teranet*; *CanLII v. Caseway* (pending) | **MODERATE–HIGH** – Crown copyright subsists if control over publication; copyright in compilations recognized | Obtain assignment/licence from Crown or rights holder; evaluate fair dealing; consider FOIA/public records requests |
| **Personal Information in Public Decisions** | *A.T. v. Globe24h.com*; PIPEDA | **MODERATE–HIGH** – PIPEDA applies to commercial republication; de-indexing + damages + corrective orders | Exclude or pseudonymize PII; avoid commercial monetization of personal data; implement privacy-by-design |
| **Technological Protection Measures (TPM)** | Copyright Act § 41; *circumvention prohibited* | **VERY HIGH** – TPM circumvention is infringement *per se*; no fair-dealing exception | Do not circumvent; seek TPM-free version or licence; liaise with rights holder |
| **Statutory Damages & Fair-Dealing Defenses** | Copyright Act § 38.1; *fair dealing limited* | **MODERATE** – Fair dealing not available for framing, embedding, or commercial competition; statutory damages $500–$20K/work (reduced via proportionality) | Document non-commercial purpose; seek licence; implement de minimis / transformative use arguments (limited success in Canada) |
| **PIPEDA Liability (Foreign Operators)** | *A.T. v. Globe24h.com*; PIPEDA § 3 | **MODERATE** (if extraterritorial nexus) – PIPEDA has extraterritorial reach; damages + corrective orders | Assess "real and substantial connection" to Canada; exclude Canadian PI; review foreign privacy compliance |
| **Unconscionable/Adhesion Terms** | *Douez v. Facebook*; *Uber v. Heller* | **LOW–MODERATE** – Consumer protection rhetoric growing; public policy defenses emerging in privacy/labour contexts | Negotiate rather than accept; document bargaining inequality; assert quasi-constitutional defences (privacy) if applicable |

***

## X. Conclusion: Enforcement Signals & Best Practices

**Key Findings:**

1. **Browse-Wrap Enforceability is Established:** Canadian courts recognize website terms of use as binding contracts on users with adequate notice; scraping in breach of such terms exposes operators to injunctions and nominal damages.[7][6]

2. **Copyright in Metadata & Compilation is Recognized:** Courts increasingly protect curated versions of public data (*Keatley*; *CanLII* litigation pending), even if underlying source material is free.[25][23]

3. **Statutory Damages Regimes are Potent but Discretionary:** Damages range $500–$20K per work (commercial) or $100–$5K aggregate (non-commercial); courts apply proportionality reductions but maintain deterrence levels.[26][1][6][8][34]

4. **AI-Era Scraping is Contested:** Ongoing litigation (*CanLII v. Caseway*; *OpenAI v. Publishers*) will clarify whether bulk machine-learning training constitutes fair dealing or infringement under Canadian law.[38][39][44][42]

5. **Privacy Law Overlays Copyright:** PIPEDA and quasi-constitutional privacy rights can invalidate otherwise-permissible republication of public information if commercial personal-data exploitation is evident.[15][16][14]

**Recommended Controls for Commercial Open-Data Projects:**

- **Obtain explicit written licensing** from data owners; rely on OGL-C, Statistics Canada Open Licence, or Reproduction of Federal Law Order only if expressly applicable.
- **Implement privacy-by-design:** Exclude or pseudonymize personal information in public datasets; use PII suppression tools for tribunal/court decisions.
- **Respect robots.txt, rate limiting, and technical controls:** Avoid circumventing TPMs or ignoring automated exclusion signals.
- **Document fair-dealing analysis:** Non-commercial research, criticism, news reporting, or transformative use may support a defence; commercial competition does not.[33][32]
- **Monitor pending litigation:** *CanLII v. Caseway* and *OpenAI v. Publishers* will crystallize AI-training-data liability; outcomes may impose licensing requirements on LLM builders.[38][42]
- **Assess extraterritorial PIPEDA risk:** If republishing decisions/content accessible to Canadian users, ensure compliance with PIPEDA's "real and substantial connection" test; avoid commercial monetization of personal data.[16][14][15]

***

## Primary Sources & ADA References

The following authorities are cited in standard ADA (American Disability Act format adapted for Canadian legal citation):

1. Copyright Act, R.S.C. 1985, c. C-42, s. 38.1 (statutory damages) and s. 41 (TPM); retrieved from laws-lois.justice.gc.ca.

2. Reproduction of Federal Law Order, SI/97-5, P.C. 1996-1995 (December 19, 1996); retrieved from laws-lois.justice.gc.ca and Wikisource.

3. Open Government Licence – Canada (OGL-C), updated December 2, 2022; retrieved from publications.gc.ca.

4. Statistics Canada Open Licence, updated July 10, 2025; retrieved from statcan.gc.ca.

5. Copyright Term Extension, Budget Implementation Act 2022, No. 1 (Bill C-19); Royal Assent June 23, 2022; In Force December 30, 2022.

6. **Supreme Court of Canada Decisions** (binding authority):
   - *Keatley Surveying Ltd. v. Teranet Inc.*, 2019 SCC 43 (Crown copyright in survey plans; section 12 interpretation).
   - *Google Inc. v. Equustek Solutions Inc.*, 2017 SCC 34 (global de-indexing injunctions; intermediary liability).
   - *Douez v. Facebook Inc.*, 2017 SCC 33 (forum-selection clauses in consumer contracts; quasi-constitutional privacy).
   - *Uber Technologies Inc. v. Heller*, 2020 SCC 16 (unconscionable arbitration clauses).
   - *Dell Computer Corp. v. Union des consommateurs*, 2007 SCC 34 (electronic contracts and arbitration).

7. **Federal Court Decisions**:
   - *A.T. v. Globe24h.com*, 2017 FC 114 (PIPEDA extraterritorial application; republishing tribunal decisions).
   - *Thomson v. Afterlife Network Inc.*, 2019 FC 545 (statutory damages for obituary scraping; CAD $20 million award).
   - (Consent Order) *Toronto Real Estate Board v. Mongohouse*, April 15, 2019, Federal Court (data scraping injunction and licensing obligations).
   - (Settled) *Canada Post v. Geolytica (Geocoder.ca)*, Federal Court File T-519-12; discontinued 2016 (crowdsourced postal-code database; public-domain facts defence).

8. **Provincial Court Decisions**:
   - *Century 21 Canada Ltd. v. Rogers Communications Inc.* (2011 BCSC 1196) (browse-wrap enforceability; copyright in real-estate listings).
   - *Trader Corporation v. CarGurus, Inc.*, 2017 ONSC 1841 (copyright in photographs; framing infringement; making available right; statutory damages proportionality).

9. **Pending Litigation**:
   - *CanLII v. Caseway AI et al.*, Notice of Civil Claim filed November 4, 2024, BC Supreme Court (bulk scraping of legal database; copyright in curated compilations).
   - *Canadian Publishers (Torstar et al.) v. OpenAI*, Statement of Claim filed November 28, 2024, Ontario Superior Court of Justice (copyright infringement in AI model training; breach of terms of use; TPM circumvention).

10. **Secondary Sources & Commentary**:
    - Smart & Biggar LLP: "Low-cost procedures for effective copyright litigation" (2021) and "Supreme Court of Canada rules on Crown copyright" (2019).
    - Fillmore Riley LLP: "What's yours is mine — Crown copyright in Canada" (2023).
    - Bennett Jones LLP: "Yes—Copying Photographs from the Internet Can Get You Sued: *Trader v. CarGurus*" (2017).
    - Cassels Brock: "Supreme Court of Canada Upholds BC Decision to Grant Worldwide De-Indexing Injunction" (2019).
    - Torys LLP: "SCC Upholds Worldwide Ban on Certain Search Results" (2017).
    - Shift Law: "PIPEDA's global extra-territorial jurisdiction and right to be forgotten" (2017) and "Browse-Wrap Agreements Enforceable in Canada" (2017).
    - Columbia Global Freedom of Expression: "A.T. v. Globe24H.com – Extraterritorial PIPEDA Application" (2018).
    - University of Alberta: "Technological Protection Measures (TPMs)" (2025).
    - Pacific Legal: "Are Online Terms and Conditions Legally Binding in Ontario" (2025).

***

**Document Date:** November 12, 2025  
**Scope:** Canadian Copyright Risk Matrix; Legislative & Judicial Scan (2007–2025)  
**Status:** Research scan based on publicly reported decisions, legislation current to July 2025, and pending litigation status as of November 2025.

[1](https://www.smartbiggar.ca/insights/publication/low-cost-procedures-for-effective-copyright-litigation-)
[2](https://laws-lois.justice.gc.ca/eng/acts/c-42/section-38.1-20190401.html)
[3](https://www.smartbiggar.ca/insights/publication/supreme-court-of-canada-rules-on-crown-copyright)
[4](https://www.fillmoreriley.com/publication/what-s-yours-is-mine-crown-copyright-in-canada)
[5](https://www.dfo-mpo.gc.ca/terms-avis/copyright-droits-eng.htm)
[6](https://www.ippractice.ca/2011/09/bcsc-on-copyright-website-terms-of-use-and-scraping/)
[7](https://www.cwilson.com/did-you-notice-browse-wrap-agreements-enforceable-in-canada/)
[8](https://www.robic.ca/en/?publications=federal-court-of-canada-interprets-s-38-1-copyright-act-regarding-statutory-damages)
[9](https://www.youtube.com/watch?v=ZpekalVSG_U)
[10](https://www.smartbiggar.ca/insights/publication/circumvention-of-technological-protection-measures-(tpms)-prohibited)
[11](https://www.ualberta.ca/en/faculty-and-staff/copyright/intro-to-copyright-law/technological-protection-measures.html)
[12](https://laws-lois.justice.gc.ca/eng/regulations/si-97-5/page-1.html)
[13](https://laws-lois.justice.gc.ca/eng/regulations/si-97-5/page-1.html?wbdisable=true)
[14](https://www.dww.com/articles/federal-court-orders-removal-of-canadian-court-and-tribunal-decisions-containing-personal)
[15](https://globalfreedomofexpression.columbia.edu/cases/t-v-globe24h-com/)
[16](https://barrysookman.com/2017/02/01/pipedas-global-extra-territorial-jurisdiction-a-t-v-globe24h-com/)
[17](https://publications.gc.ca/collections/collection_2024/sct-tbs/BT22-278-2023-eng.pdf)
[18](https://www.statcan.gc.ca/en/terms-conditions/open-licence-faq)
[19](https://www.statcan.gc.ca/en/terms-conditions/open-licence)
[20](https://www.smartbiggar.ca/insights/publication/70-is-the-new-50--term-of-copyright-protection-in-canada-to-become--life-plus-70--on-december-30--2022)
[21](https://copyright.info.yorku.ca/2022/12/general-term-of-canadian-copyright-extended-from-50-to-70-years-details/)
[22](https://www.lavery.ca/en/publications/our-publications/4317-the-duration-of-copyright-protection-in-canada-is-extended-to-70-years-as-of-december-30-2022.html)
[23](https://www.scc-csc.ca/judgments-jugements/cb/2019/37863/)
[24](https://stewartmckelvey.com/thought-leadership/the-crown-of-copyright/)
[25](https://www.jdsupra.com/legalnews/supreme-court-of-canada-rules-on-crown-26787)
[26](https://www.yorku.ca/osgoode/iposgoode/2011/09/23/century-21-v-zoocasa-contract-and-copyright-in-the-electronic-world/)
[27](https://pacificlegal.ca/are-terms-and-conditions-legally-binding-in-ontario/)
[28](https://www.scc-csc.ca/pdf/case-documents/36616/FM030_Intervener_Interactive-Advertising-Bureau-of-Canada.pdf)
[29](https://en.wikipedia.org/wiki/Douez_v_Facebook)
[30](https://www.torys.com/en/our-latest-thinking/publications/2017/06/scc-facebooks-forum-selection-clause-is-unenforceable)
[31](https://shiftlaw.ca/facebooks-forum-selection-clause-ruled-unenforceable/)
[32](https://vlex.com/vid/information-location-tool-and-677616057)
[33](https://www.bennettjones.com/Insights/Blogs/Yes-Copying-Photographs-from-the-Internet-Can-Get-You-into-Trouble)
[34](https://www.nortonrosefulbright.com/en/knowledge/publications/ed6d21da/ip-monitor---car-websites-scraped-but-only-slightly-dented)
[35](https://www.dww.com/articles/federal-court-awards-20-million-an-obituary-piracy-class-action-lawsuit)
[36](https://vlex.com/vid/obituary-piracy-assessed-785431373)
[37](https://www.mondaq.com/canada/intellectual-property/848096/unauthorized-reproduction-of-online-content-proves-costly)
[38](https://www.law360.ca/ca/articles/2260474/canlii-sues-ai-based-legal-research-platform-for-alleged-data-scraping-and-copyright-violations)
[39](https://www.canadianlawyermag.com/resources/legal-technology/canlii-sues-ai-legal-assistant-caseway-ai-for-copyright-infringement/389633)
[40](https://www.lawnext.com/2024/11/major-canadian-legal-research-service-sues-ai-startup-claiming-wrongful-use-of-its-court-decisions.html)
[41](https://www.cbc.ca/news/canada/british-columbia/canlii-lawsuit-caseway-ai-1.7374964)
[42](https://betakit.com/openai-pushes-for-canadian-publishers-copyright-lawsuit-to-be-heard-in-the-us/)
[43](https://www.nytimes.com/2024/11/29/world/canada/canada-openai-lawsuit-copyright.html)
[44](https://natlawreview.com/article/canadian-news-outlets-seek-what-could-amount-billions-openai-new-copyright)
[45](https://www.gzlegal.com/the-case-of-uber-technologies-inc-v-heller-2020-scc-16/)
[46](https://conflictoflaws.net/2020/uber-arbitration-clause-unconscionable/?print=print)
[47](https://icsid.worldbank.org/sites/default/files/parties_publications/C8394/Claimants'%20documents/CL%20-%20Exhibits/CL-0393.pdf)
[48](https://ppl-ai-file-upload.s3.amazonaws.com/web/direct-files/attachments/124352643/64146e36-9cb2-4565-87f8-b433a7c3482c/Project-Description-Legislative-Research-Judicial-Scan-Canada_v.1.docx)
[49](https://en.wikisource.org/wiki/Reproduction_of_Federal_Law_Order)
[50](https://en.wikipedia.org/wiki/Open_Government_Licence)
[51](https://infojustice.org/archives/28519)
[52](https://laws-lois.justice.gc.ca/eng/acts/c-42/section-38.1.html)
[53](https://www150.statcan.gc.ca/n1/pub/11-627-m/11-627-m2025044-eng.htm)
[54](https://abacus.library.ubc.ca/dataset.xhtml?persistentId=hdl%3A11272.1%2FAB2%2FCRDZJ0)
[55](https://www.airdberlis.com/docs/default-source/articles/century-21-v-rogers---september-2011-blog-post.pdf?sfvrsn=2)
[56](https://globalfreedomofexpression.columbia.edu/cases/equustek-solutions-inc-v-jack-2/)
[57](https://cassels.com/insights/supreme-court-of-canada-upholds-bc-decision-to-grant-worldwide-de-indexing-order-against-google/)
[58](https://www.torys.com/en/our-latest-thinking/publications/2017/07/scc-upholds-worldwide-ban-on-certain-search-results)
[59](https://www.frosszelnick.com/canada-supreme-court-interprets-scope-of-crown-copyright/)
[60](https://digitalcommons.osgoode.yorku.ca/cgi/viewcontent.cgi?article=1362&context=sclr)
[61](https://www.quimbee.com/cases/dell-computer-corp-v-union-des-consommateurs)
[62](https://www.dww.com/sites/default/files/assets/inline-files/Gary%20Daniel%20-%20GDR%20News%20-%20April%2023,%202019.pdf)
[63](https://postandparcel.info/47044/news/canada-post-tests-copyright-law-over-its-address-database/)
[64](https://ca.vlex.com/vid/dell-computer-v-union-680842845)
[65](https://www.onlinemarketplaces.com/articles/treb-successfully-sues-property-portal-for-copying-its-data/)
[66](https://www.michaelgeist.ca/2012/04/canada-post-geocoder-suit/)
[67](https://newyorkconvention1958.org/index.php?lvl=notice_display&id=553)
[68](https://shiftlaw.ca/copyright-and-factual-content/)
[69](https://www.cippic.ca/news/cippic-acting-for-geolytica-in-postal-code-database-copyright-lawsuit)
[70](http://www.uncitral.org/docs/clout/CAN/CAN_130707_FT.pdf)
