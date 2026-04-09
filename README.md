# AI project ideas for documentation teams

An open-source collection of AI project ideas for documentation teams that use docs-as-code workflows. Each idea targets a specific pain point, explains why AI is the right tool, and includes an implementation sketch with research backing and named tools. Built for technical writers, docs engineers, developers, and anyone working at the intersection of documentation and AI.

Want to add your own idea? See [CONTRIBUTING.md](CONTRIBUTING.md).

---

This document collects project ideas for documentation teams exploring AI automation. Every idea is grounded in a real pain point: content that drifts out of sync with code, review queues clogged with mechanical edits, documentation gaps that surface through customer complaints instead of proactive detection. Each idea is framed around a central question: what can AI enable documentation teams to do that they couldn't do before?

These ideas emerged from a documentation team managing a large doc set (500+ pages) for a fast-shipping engineering organization. The problems are not unique to that team. If your documentation lives in a Git repository, your review process uses pull requests, and your CI pipeline runs linters or validators, most of these ideas apply directly to your stack.

Every idea includes a problem statement, an explanation of why AI is needed, an implementation sketch, research backing, and recommended open-source tools. The ideas are complete enough to start a proof of concept during a hack week or focused sprint.

## Prerequisites

This resource assumes:

- Your documentation lives in a **Git repository** (docs-as-code workflow)
- Your team uses **pull requests** for review
- You have or can add a **CI/CD pipeline** for automated checks
- You use or are willing to adopt a **docs linter** (Vale, markdownlint, or similar)

Teams using wiki or CMS-based documentation platforms (Confluence, SharePoint, Paligo, MadCap Flare) will find the problem framing useful but the implementation approaches less directly applicable.

## How to use this resource

Each idea stands alone. Browse the table of contents, find ideas that match your pain points, and read only those. Ideas are organized by theme but not sequenced; there is no required reading order. The "Related ideas" section at the end of each idea points to complementary projects if you want to explore a cluster.

The implementation sketches describe architectures and approaches, not step-by-step tutorials. They name specific tools and cite research so you can evaluate feasibility before committing engineering time.

## Table of contents

| Project                                                      | Theme                  | Description                                                  | Related projects                                             |
| ------------------------------------------------------------ | ---------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [Code-to-docs change detection pipeline](#code-to-docs-change-detection-pipeline) | Change detection       | Monitors code PRs and design docs for documentation impact, generates starter drafts | [Blast radius analyzer](#documentation-blast-radius-analyzer), [PR review triage](#pr-review-triage-and-semantic-change-detection) |
| [Documentation blast radius analyzer](#documentation-blast-radius-analyzer) | Change detection       | Maps semantic connections across the doc set to find all pages affected by a change | [Code-to-docs detection](#code-to-docs-change-detection-pipeline), [Terminology consistency](#terminology-and-cross-reference-consistency-engine) |
| [Content staleness detector](#content-staleness-detector)    | Change detection       | Scores pages on staleness risk using metadata age signals and semantic drift analysis | [Code-to-docs detection](#code-to-docs-change-detection-pipeline), [Blast radius analyzer](#documentation-blast-radius-analyzer) |
| [Terminology and cross-reference consistency engine](#terminology-and-cross-reference-consistency-engine) | Quality and coverage   | Detects inconsistent term usage and missing cross-references across multi-product docs | [Semantic consistency checker](#cross-page-semantic-consistency-checker), [Linter auto-fix](#linter-auto-fix-with-llm-rewriting) |
| [Cross-page semantic consistency checker](#cross-page-semantic-consistency-checker) | Quality and coverage   | Detects cross-page contradictions using LLM comparison of related page pairs | [Terminology consistency](#terminology-and-cross-reference-consistency-engine), [Content duplication detection](#content-duplication-detection) |
| [Linter auto-fix with LLM rewriting](#linter-auto-fix-with-llm-rewriting) | Quality and coverage   | Three-layer auto-fix: deterministic formatting, terminology swaps, and LLM sentence rewrites | [Terminology consistency](#terminology-and-cross-reference-consistency-engine), [Authoring-time validation hooks](#authoring-time-structural-validation-hooks) |
| [Content duplication detection](#content-duplication-detection) | Quality and coverage   | Page-level semantic similarity and snippet-level duplicate identification | [Terminology consistency](#terminology-and-cross-reference-consistency-engine), [Documentation coverage map](#documentation-coverage-map) |
| [Documentation coverage map](#documentation-coverage-map)    | Quality and coverage   | Compares codebase user-facing surfaces against docs to produce a prioritized gap report | [Blast radius analyzer](#documentation-blast-radius-analyzer), [Content staleness detector](#content-staleness-detector) |
| [PR review triage and semantic change detection](#pr-review-triage-and-semantic-change-detection) | Review and workflow    | Classifies PR changes as cosmetic, additive, behavioral, or destructive for review routing | [Code-to-docs detection](#code-to-docs-change-detection-pipeline), [Auto-merge for mechanical edits](#auto-merge-for-mechanical-edits) |
| [Auto-merge for mechanical edits](#auto-merge-for-mechanical-edits) | Review and workflow    | Auto-merges provably safe changes (URL swaps, version bumps, linter auto-fixes) without human review | [PR review triage](#pr-review-triage-and-semantic-change-detection), [Linter auto-fix](#linter-auto-fix-with-llm-rewriting) |
| [Authoring-time structural validation hooks](#authoring-time-structural-validation-hooks) | Review and workflow    | Pre-commit hooks for frontmatter, headings, images, code blocks, and links | [Linter auto-fix](#linter-auto-fix-with-llm-rewriting), [Documentation scaffolding CLI](#documentation-scaffolding-cli) |
| [Conversational documentation testing](#conversational-documentation-testing) | Testing and validation | LLM-as-judge comprehension tests and procedure execution tests in CI | [Reader persona simulation](#reader-persona-simulation), [Configuration example validator](#configuration-example-validator) |
| [Reader persona simulation](#reader-persona-simulation)      | Testing and validation | LLM role-plays specific reader personas to surface usability friction and comprehension gaps | [Conversational documentation testing](#conversational-documentation-testing) |
| [Configuration example validator](#configuration-example-validator) | Testing and validation | Extracts config examples from docs and validates against product schemas in CI | [Conversational documentation testing](#conversational-documentation-testing), [Authoring-time validation hooks](#authoring-time-structural-validation-hooks) |
| [Alt text generation for screenshots](#alt-text-generation-for-screenshots) | Content generation     | Vision-model pipeline generating contextual alt text from screenshots and surrounding page context | [Authoring-time validation hooks](#authoring-time-structural-validation-hooks) |
| [Documentation scaffolding CLI](#documentation-scaffolding-cli) | Content generation     | CLI tool generating new doc pages with correct frontmatter, headings, and components | [Authoring-time validation hooks](#authoring-time-structural-validation-hooks), [Engineer-to-docs input pipeline](#engineer-to-docs-structured-input-pipeline) |
| [Engineer-to-docs structured input pipeline](#engineer-to-docs-structured-input-pipeline) | Content generation     | Converts structured engineer answers into on-format doc drafts via templates and AI prose | [Code-to-docs detection](#code-to-docs-change-detection-pipeline), [Documentation scaffolding CLI](#documentation-scaffolding-cli) |
| [Agent-friendly documentation validation](#agent-friendly-documentation-validation) | AI readability         | Validates documentation sites against the Agent-Friendly Documentation Spec using afdocs CLI checks and LLM comprehension testing | [Ghost reader report](#ghost-reader-report), [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle) |
| [Ghost reader report](#ghost-reader-report)                  | AI readability         | Audits AI-generated responses about your product for factual accuracy against current docs | [Agent-friendly documentation validation](#agent-friendly-documentation-validation), [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle) |
| [MCP server for documentation](#mcp-server-for-documentation) | AI readability         | Read-only MCP server exposing docs to Claude Code, Copilot, and Cursor at inference time | [Agent-friendly documentation validation](#agent-friendly-documentation-validation), [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle) |
| [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle) | AI readability         | Build-time llms.txt generation, JSON-LD schema markup, and frontmatter CI enforcement | [Agent-friendly documentation validation](#agent-friendly-documentation-validation), [Ghost reader report](#ghost-reader-report), [MCP server for documentation](#mcp-server-for-documentation) |

---

## Change detection and drift prevention

### Code-to-docs change detection pipeline
A three-level pipeline that automatically monitors engineering artifacts for documentation signals: it scans design docs for upstream planning references, watches GitHub PRs for code changes that affect existing docs, and generates starter drafts for writers when updates are needed. It combines deterministic [AST-anchored detection via Fiberplane Drift](https://fiberplane.com/blog/drift-documentation-linter/) with LLM-based semantic analysis to catch both obvious and indirect documentation impacts, routing everything through human review before publication. The pipeline hooks into GitHub webhook events on your engineering repos, feeds diffs through a semantic comparison against your documentation set, and outputs structured tickets or draft PRs. It never publishes anything automatically.

#### The problem it solves

When code changes land in engineering repos, the docs team has no automated way to know which documentation pages are affected. Writers discover drift days or weeks after a merge, often through customer complaints or during release crunch. The team manually monitors PRs and relies on engineers to flag docs-impacting changes, which they frequently don't. Incidents where multiple pages incorrectly state a feature's support status, requiring cross-functional cleanup, show what happens without this signal. Even shipped features can be listed as unsupported for weeks without detection.

The problem also exists upstream at the planning stage. Documentation needs are buried in engineering artifacts the docs team doesn't monitor: design docs that mention "user documentation" or "migration guide," feature specs that include a "documentation plan" section, and other feature specs with a "user-facing changes" section. The team discovers these needs at shipping time, not planning time. In a high-volume launch cycle where documentation consistently lags behind shipping, the cost of this gap is real.

#### Why AI is needed

Rule-based detection (such as grep for file paths or keywords) only catches the most obvious cases. A change to a CLI flag's default value, a refactored API endpoint, or a shifted authentication flow requires understanding what the code *means*, not what files it touches. AI enables semantic comparison between a code diff and the existing documentation set, identifying conceptual overlap that no regex or keyword match can capture. An LLM can read a diff, understand that it changes how a networking feature is configured, and then find every doc page that discusses that feature, even if none of those pages mention the specific file that changed.

At the planning stage, scanning a design doc for the phrase "user documentation" is trivial keyword matching. But understanding whether "we should document this new behavior" in paragraph 14 of a design doc means "we need a new docs page" vs. "we should update an existing page" vs. "this is aspirational and not actionable yet" requires reading comprehension. An LLM can extract actionable documentation signals from unstructured engineering documents and assess whether they're immediate needs or future considerations.

Beyond detection, translating a code diff into a prose description of what changed for users requires understanding both the code's intent and how to communicate it to a non-engineering audience. Deterministic tools can detect that a change happened but cannot explain what it means. AI adds the generation step: producing a starting draft so the writer's job shifts from blank-page authoring to editing. The ideal architecture is two-tier: [Fiberplane Drift](https://fiberplane.com/blog/drift-documentation-linter/) handles deterministic AST-anchored detection for known doc-to-code bindings; LLM-based semantic analysis handles everything else.

#### How it might work

The pipeline could operate at three levels.

**Level 1** (deterministic + AI): a periodic scan of shared drives and document management systems identifies engineering documents that reference documentation needs. An LLM extracts what documentation is needed, what product area it covers, and what the expected timeline is.

**Level 2** (deterministic + AI): the pipeline hooks into GitHub's webhook events on your engineering repos via existing CI infrastructure. When a PR merges, a GitHub Action sends the diff to an LLM along with an index of all documentation topics in your content directory. The LLM identifies which docs are potentially affected and outputs a structured list. For API-heavy repos, a deterministic pre-pass with [oasdiff](https://github.com/oasdiff/oasdiff) or generated CLI `--help` snapshots can mark obvious contract changes before semantic matching, which reduces both cost and false positives. A likely starting point is adding a documentation impact step to an existing PR workflow rather than building a new one.

**Level 3** (AI): when Level 2 classifies a change as docs-impacting, the diff plus the existing page is fed to an LLM that generates a draft update following your style guide. The draft is explicitly labeled as AI-generated and goes into a ticket, never published automatically.

A two-tier detection architecture is more reliable than either approach alone: [Fiberplane Drift](https://fiberplane.com/blog/drift-documentation-linter/) for deterministic AST-anchored detection, [driftcheck](https://github.com/deichrenner/driftcheck) for LLM-powered semantic contradiction detection on staged diffs. For a focused sprint, a reasonable scope is Level 2 alone on a single product area with a small number of pages and active development.

#### The value it will bring

Estimated time savings of 3 to 5 hours per week in manual PR monitoring across the docs team. Catching documentation needs at the planning stage gives the team weeks of lead time instead of days. Adding draft generation reduces the time from "code shipped" to "docs updated" by shifting the writer's task from authoring to editing.

#### Research backing

[Fiberplane's 2025 blog post on documentation rot](https://fiberplane.com/blog/drift-documentation-linter/) describes the problem precisely: "Agents are prolific code changers but terrible at knowing what else their change affects." Their production data shows AST fingerprinting with [tree-sitter](https://tree-sitter.github.io/) eliminates false positives from whitespace-only changes that naive diff-based detection would flag. [Promptless's YC W25 launch](https://news.ycombinator.com/item?id=43092522) demonstrates commercial viability: customers include a Fortune 500 company where Promptless "makes every technical writer twice as productive." Overcast Blog's [2025 analysis of AI-driven documentation](https://overcast.blog/documenting-your-codebase-in-2025-c40c4deec3f8) recommends instrumenting CI pipelines to flag docs older than a threshold in modules with recent commits, and identifies "days since doc updated relative to last code change" as a leading indicator of drift.

#### Recommended tools

- [Fiberplane Drift](https://github.com/fiberplane/drift) (TypeScript/Go, MIT): anchors markdown and MDX files to specific source code symbols using tree-sitter AST fingerprinting, then runs `drift check` in CI. Supports TypeScript, Python, Rust, Go, Zig, and Java on the code side; language-agnostic on the docs side.
- [driftcheck](https://github.com/deichrenner/driftcheck) (Go, January 2026): LLM-powered pre-push hook that parses staged diffs, uses semantic search to find related docs, and flags factual contradictions. Requires an OpenAI-compatible API key.
- [oasdiff](https://github.com/oasdiff/oasdiff) (Go, Apache 2.0): compares OpenAPI specs, detects breaking and additive changes, and emits changelogs or GitHub-friendly annotations. It is a strong deterministic front-end when part of the documentation surface is API reference content.
- [reviewdog](https://github.com/reviewdog/reviewdog) (Go, MIT): routes findings from drift detectors back into PR comments or GitHub checks so docs-impact signals show up where engineers already work.
- [Promptless](https://promptless.ai/) (commercial, YC W25): full end-to-end commercial pattern with [Vale](https://vale.sh/) integration for style matching. Confirms the architecture is production-proven at scale.

#### Related ideas

Directly enables [Documentation blast radius analyzer](#documentation-blast-radius-analyzer) by producing the change detection signals that blast radius analysis extends. Shares GitHub Actions infrastructure with [PR review triage and semantic change detection](#pr-review-triage-and-semantic-change-detection).

---

### Documentation blast radius analyzer
A semantic search and knowledge graph tool that maps hidden connections across the entire doc set, so writers know instantly which pages need updating when a product change lands. Given a concept, feature name, or code change description, it returns a ranked list of every documentation page that is conceptually related, even when those pages use different terminology. It combines vector embeddings for semantic similarity with entity-relationship extraction and git-history coupling analysis for a multi-layered view of what needs updating.

#### The problem it solves

When a product change is announced, there is no quick way to identify every documentation page that references the affected concept. Writers rely on full-text search and memory, which misses indirect references and semantic connections. Incidents where a claim appeared across multiple pages illustrate the stakes: cross-reference discovery is entirely manual. Content duplication compounds the problem; redundant pages covering the same installation or configuration topic mean a single change may require updates in a dozen places. As documentation grows and new product areas integrate into the same doc set, identifying what connects to what becomes untenable without tooling.

#### Why AI is needed

Full-text search finds exact keyword matches but cannot identify pages that discuss the same concept using different terminology. A change to how your product handles DNS resolution might affect pages about DNS features, split DNS, networking configuration, and integration docs, but those pages may never use the phrase "DNS resolution" directly. Vector embeddings represent every documentation page as a point in semantic space, so a query about "DNS resolution behavior" returns all conceptually related pages regardless of exact wording.

Entity extraction adds a second AI layer: parsing pages to identify product names, CLI commands, config keys, and cross-references, then building a queryable relationship graph. Recognizing that a CLI command mentioned in a troubleshooting guide creates a dependency on the CLI reference page requires reading comprehension, not pattern matching.

#### How it might work

Two complementary layers, one deterministic and one semantic.

**Layer 1** (semantic, AI): embed all documentation topics using [sentence-transformers](https://github.com/huggingface/sentence-transformers) (specifically `all-MiniLM-L6-v2`, which is 5x faster than `all-mpnet-base-v2` with comparable quality) and store vectors in [Qdrant](https://github.com/qdrant/qdrant) for fast semantic similarity queries. If you want a lighter runtime, [FastEmbed](https://github.com/qdrant/fastembed) paired with Qdrant is often enough for a CPU-only proof of concept. When a writer inputs a concept, feature name, or code change description, the tool returns the top-k most semantically relevant documentation pages with similarity scores.

**Layer 2** (deterministic, historical): mine the git history using [code-maat](https://github.com/adamtornhill/code-maat) (`-a coupling`) to compute change coupling: which doc files always change in the same commits. A doc file and a code file that frequently co-occur in commits are blast-radius candidates even when they share no semantic similarity.

The two layers combine into a single ranked output: semantic similarity score + historical coupling frequency = blast radius score. The tool could be exposed as a CLI command (`blast-radius "DNS resolution"`) or a simple web interface. A focused sprint scope: Layer 1 alone on a high-churn documentation directory.

#### The value it will bring

Reduces the risk of incomplete documentation updates from high to near-zero for any given change. Turns a task that currently requires 30 to 60 minutes of manual searching and cross-referencing into a 10-second query. The entity-relationship graph produced as a side effect is also independently valuable as documentation health data.

#### Research backing

[Qdrant's 2025 technical documentation on semantic code search](https://qdrant.tech/documentation/tutorials-search-engineering/code-search/) demonstrates the pattern: embed content, store vectors, query by meaning rather than keywords. [CodeScene's engineering blog on change coupling](https://codescene.com/engineering-blog/change-coupling-visualize-the-cost-of-change) explains the methodology: "change coupling isn't possible to calculate from code alone; this information is mined from developer patterns in Git repositories." The [sentence-transformers `all-MiniLM-L6-v2` model](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) has been fine-tuned on over 1 billion training pairs and runs on CPU; no GPU is required for a 500-document doc set.

#### Recommended tools

- [code-maat](https://github.com/adamtornhill/code-maat) (Clojure CLI): mines VCS logs to compute change coupling via `-a coupling`. The `-g` grouping flag maps individual files to logical components for architectural-level analysis.
- [Qdrant](https://github.com/qdrant/qdrant) (Rust, Apache 2.0, 29K GitHub stars): high-performance vector database with hybrid dense-sparse retrieval, metadata filtering, and a simple REST API.
- [FastEmbed](https://github.com/qdrant/fastembed) (Python, Apache 2.0): lightweight embedding generation using ONNX Runtime. Good fit for CI jobs or local tools where a full transformer stack is heavier than necessary.
- [txtai](https://github.com/neuml/txtai) (Python, Apache 2.0): combines an embeddings database, graph networks, and a relational database in a single library. A potential all-in-one solution for the hybrid semantic-plus-graph query this tool needs.
- [sentence-transformers](https://github.com/huggingface/sentence-transformers) (Python, Apache 2.0): provides the embedding model; `all-MiniLM-L6-v2` (384 dimensions) is the recommended starting model.
- [Neo4j GraphRAG](https://pypi.org/project/neo4j-graphrag/) (Python, Apache 2.0): adds graph-native retrieval on top of a relationship store, which is useful once the blast radius model evolves from simple page similarity into entity traversal.

#### Related ideas

Provides the semantic foundation that [Code-to-docs change detection pipeline](#code-to-docs-change-detection-pipeline) uses for identifying affected pages from a code diff. The entity-relationship graph it produces is directly useful for [Terminology and cross-reference consistency engine](#terminology-and-cross-reference-consistency-engine).

---

### Content staleness detector
A continuous monitoring system that combines metadata-based age signals (a date-based freshness field in frontmatter and last commit date per page) with semantic drift analysis (whether recent code changes have altered the behavior a page describes) to score each documentation page on a staleness risk scale. Pages that are both old and in areas of the codebase with recent activity score highest. High-risk pages are surfaced in weekly CI reports and optional chat digests for systematic burndown, replacing manual audit cycles with an automated, prioritized queue. The metadata layer ships in hours using `git effort` from [git-extras](https://github.com/tj/git-extras); the semantic drift layer is added in a second phase using LLM-based cross-referencing.

#### The problem it solves

Most docs teams have no automated way to identify stale content. In a large documentation set, a significant portion of pages have gone without updates for six or more months while the product changes underneath them. Incidents where documentation states incorrect feature support status illustrate what happens when stale content goes undetected. Discovering outdated content requires manual auditing on a recurring basis, which is time-consuming and never comprehensive. Content duplication compounds the problem: the same information stated in multiple places means staleness multiplies across pages.

#### Why AI is needed

A simple "last modified date" check tells you when a page was last touched but not whether it *needs* to be touched. A freshness field in frontmatter (e.g., `lastValidated`) is a better signal, but it only measures when a human last attested to accuracy, not whether anything has changed since. AI enables the second layer: semantic drift analysis. An LLM can read a documentation page, identify the features and behaviors it describes, cross-reference those against recent commits and release notes, and flag pages where the described behavior may no longer be accurate. This requires language comprehension: a deterministic tool can match file paths and dates, but it cannot read a sentence like "the CLI uses port 41641 by default" and know to flag it when the default port behavior changes in the codebase. The two-layer approach is critical: deterministic git metadata handles the first pass; AI classification handles the semantic cross-referencing that no script can do.

#### How it might work

The system operates in two layers shipped sequentially.

**Layer 1** (deterministic, ships day 1): run `git effort` from [git-extras](https://github.com/tj/git-extras) against your content directory to produce a ranked staleness report by commit count and active days per file. Cross-reference with frontmatter freshness values (if present) to produce a composite staleness score: pages with old freshness dates and low recent commit activity score highest. If you already collect git analytics in SQL, [MergeStat](https://docs.mergestat.com/) can join doc age with nearby code churn by directory or component, which makes the score more useful than page age alone. This layer produces an immediately actionable prioritized list with no AI dependency. A GitHub Actions workflow can run this on a weekly schedule and post results to a ticket.

**Layer 2** (AI, adds semantic drift): when a page scores above the staleness threshold, an LLM compares the page's claims against recent git log entries and release notes from your engineering repos. The LLM outputs a structured flag: `{page, claim, last_validated, recent_change, confidence}`. High-confidence flags go directly into the weekly report as "verify this specific claim." A focused sprint scope: scan a single high-traffic content directory against the corresponding source code directory's recent commit log.

#### The value it will bring

Replaces a manual, periodic, incomplete audit process with a continuous, comprehensive one. Reduces the risk of customers following outdated instructions, particularly important for security-sensitive features and rapidly developing product areas. Provides the team with a prioritized backlog of pages to update, directly reducing triage time. The 2025 [DORA State of AI-Assisted Software Development Report](https://dora.dev/research/2025/dora-report/) found that teams using AI for documentation maintenance report a 7.5% quality improvement; the staleness detector is the mechanism that makes that improvement systematic rather than ad hoc.

#### Research backing

The [IBM Think Insights analysis of watsonx Code Assistant](https://www.ibm.com/think/insights/watsonx-code-assistant-software-development) found a 59% reduction in documentation maintenance time when AI handles routine staleness identification tasks. [Dosu's blog post on AI-maintained documentation](https://dosu.dev/blog/using-ai-to-generate-and-maintain-documentation) describes the "living documentation" model: every time a repository changes, the system checks whether the public documentation still aligns with reality, creates a draft update if needed, and leaves humans to review and merge. [Kiro's spec-driven approach](https://kiro.dev/blog/introducing-kiro/) demonstrates the architectural pattern of anchoring documentation to code artifacts: agent hooks trigger documentation review automatically when specific files change. The [2025 DORA report](https://cloud.google.com/resources/content/2025-dora-ai-assisted-software-development-report) found that 64% of software professionals now use AI for documentation, with quality improvements reported across teams that use AI for continuous maintenance rather than one-time generation.

#### Recommended tools

- [git-extras](https://github.com/tj/git-extras) (shell, `brew install git-extras`): provides `git effort`, which shows commits per file and active days per file across a directory. Produces an immediately actionable staleness ranked list in minutes.
- [mergestat-lite](https://github.com/mergestat/mergestat-lite) (Go/SQLite, MIT): SQL-queryable git history that lets you correlate page age with nearby code churn, ownership, or release activity instead of ranking by age alone.
- [Swimm](https://swimm.io/) (JavaScript/TypeScript, GitHub Marketplace): anchors documentation pages to source code symbols. Its [GitHub app](https://github.com/marketplace/swimm-io) runs a check on every PR when coupled documentation drifts.
- [Dosu](https://dosu.dev/) (SaaS, [GitHub](https://github.com/dosu-ai)): monitors repository changes, checks whether documentation aligns with reality, and creates draft updates for human review. Used by over 16,000 repositories including LangChain and Apache Superset.
- [Frouros](https://github.com/IFCA-Advanced-Computing/frouros) (Python, BSD-3-Clause): embedding-based drift detection algorithms applicable to documentation content embeddings over time.

#### Related ideas

Directly complements [Code-to-docs change detection pipeline](#code-to-docs-change-detection-pipeline); that idea detects changes at the code PR level while this idea detects accumulated drift across the full doc set. Shares the staleness data layer with [Documentation blast radius analyzer](#documentation-blast-radius-analyzer), which needs to know which docs are at risk before it can prioritize blast radius impact.

---

## Quality, consistency, and coverage

### Terminology and cross-reference consistency engine
A doc-set-wide scanning system that maintains a YAML-based terminology registry, detects inconsistent usage of product names and technical terms across all documentation, and surfaces missing cross-references between related pages. The system combines a deterministic term registry with [sentence-transformers](https://sbert.net/) embeddings and LLM-powered semantic analysis to catch context-dependent terminology drift that static dictionary rules cannot detect, particularly across multi-product boundaries where common terms carry different meanings in different product areas.

#### The problem it solves

Terminology drift across a documentation set is a persistent problem that static linter rules cannot fully address. Substitution rules enforce known term replacements, but they cannot detect when the same word is used with different meanings in different product areas. Incidents where multiple pages state incorrect feature support status illustrate semantic drift that no regex rule could catch.

As organizations expand to multiple products, term collision across product boundaries multiplies. Does "policy" mean the access control configuration in one product area and a gateway routing rule in another? Does "device" mean a node on the network or a managed endpoint in an MDM context? A linter can enforce that "VPN" should be replaced with your product's preferred term, but it cannot flag when that term is used inconsistently across different product areas. Manual enforcement requires validating broken links at 30 to 40 minutes per check, and cross-reference gaps between related pages are invisible until a reader notices the missing link.

#### Why AI is needed

Term consistency is context-dependent. A static dictionary can tell you that an abbreviation should always be expanded, but it cannot tell you whether two pages describing "the access policy" refer to the same concept or different ones. That distinction requires reading comprehension across both pages in context.

This project requires two AI capabilities. First, **semantic embeddings** to map every documentation page into vector space, enabling doc-set-wide clustering of pages that discuss related concepts without cross-referencing each other. Second, **LLM comprehension** to analyze clusters of related pages and identify whether the same term is being used with different meanings or whether different terms describe the same concept. The registry itself is entirely deterministic; the AI only operates during the analysis pass that populates it.

#### How it might work

Three stages. **Stage 1** (deterministic): parse all documentation pages, extract all instances of tracked terms from your linter's terminology registry, and optionally run [spaCy's EntityRuler](https://spacy.io/api/entityruler) to capture adjacent product names, commands, and config keys as graph nodes. Build a co-occurrence matrix showing which terms appear together on which pages. Pages that always change together in git history but never cross-reference each other are flagged as missing-link candidates. **Stage 2** (AI): an LLM receives batches of pages with high embedding similarity and classifies whether each shared term is being used consistently or with different meanings. The output is structured: `{term, pages, finding, confidence}`. **Stage 3** (deterministic): the report is diffed against the term registry, and a ticket is filed for each newly detected inconsistency.

For a focused sprint, start with a single product area and one high-density cross-cutting term like "policy," generating the first version of the term graph before scaling to the full doc set.

#### The value it will bring

Eliminates the manual burden of style guide enforcement by automating detection of drift. Provides a doc-set-wide view of terminology usage that scales with multi-product expansion. Each new product's docs are automatically audited against the shared term graph rather than requiring a manual review pass.

#### Research backing

[Acrolinx](https://www.acrolinx.com/) provides enterprise terminology governance commercially, reporting that customers reduced content review cycle time from 3 weeks to 2 days. Their platform uses the same two-layer architecture: a deterministic term registry plus AI-powered context analysis. [Datadog's 2024 case study on Vale adoption](https://www.datadoghq.com/blog/engineering/how-we-use-vale-to-improve-our-documentation-editing-process/) documents where rule-based enforcement reaches its limits (context-dependent meaning), which is what this project addresses. The [WMT25 Terminology Translation Task](https://www2.statmt.org/wmt25/pdf/2025.wmt-1.30.pdf) provides academic validation that sentence-level terminology consistency in technical documentation is a well-understood and tractable NLP problem.

#### Recommended tools

- [sentence-transformers](https://github.com/UKPLab/sentence-transformers) (Python, MIT): provides the embedding backbone. The `all-MiniLM-L6-v2` model is 22MB, runs locally, and processes hundreds of sentences per second.
- [code-maat](https://github.com/adamtornhill/code-maat) (Clojure, Eclipse Public License): temporal coupling analysis from git history for the missing-link signal.
- [spaCy](https://github.com/explosion/spaCy) (Python, MIT): provides rule-based and statistical entity extraction. Its `EntityRuler` is especially useful for bootstrapping a terminology graph from existing product nouns, commands, and configuration keys.
- [Vale](https://vale.sh/) (Go, MIT): the deterministic foundation. This engine adds the semantic analysis layer on top rather than replacing it.
- [Neo4j GraphRAG](https://pypi.org/project/neo4j-graphrag/) (Python, Apache 2.0): useful if the terminology registry grows into a graph-backed system where cross-product term relationships need to be traversed, not just listed.

#### Related ideas

Produces the term graph that [Cross-page semantic consistency checker](#cross-page-semantic-consistency-checker) uses for deeper analysis. Complements [Linter auto-fix with LLM rewriting](#linter-auto-fix-with-llm-rewriting) because this engine detects drift that linter rules cannot catch, while auto-fix handles the mechanical corrections.

---

### Cross-page semantic consistency checker
An LLM-powered scanning tool that compares how key concepts are described across related documentation pages and flags semantic contradictions or inconsistencies. It pairs pages by shared topic tags or cross-links, analyzes descriptions of the same concepts using embeddings and chain-of-thought prompting, and surfaces conflicts as a reviewable report with specific passage citations. Unlike string-matching tools, it can recognize that "the product uses WireGuard tunnels" and "the product creates encrypted connections" describe the same mechanism at different levels of precision, and flag when one page's phrasing contradicts another's without any shared vocabulary.

#### The problem it solves

The same concept gets described differently across pages in ways that erode user trust and occasionally cause real incidents. When multiple pages state incorrect feature support status and a customer makes a decision based on those claims, the result is a credibility-damaging cleanup. With hundreds of documentation pages, no human can hold the full doc set in their head to catch cross-page contradictions before they reach users.

#### Why AI is needed

Detecting that two different descriptions of the same mechanism contradict each other requires semantic understanding no deterministic tool can perform. Grep, diff, and linter terminology rules match strings and patterns, not meaning. All linter rules can be active during an incident and none will catch the contradiction because the incorrect claims are internally consistent on each page. An LLM can reason about whether two passages describe the same concept, whether one is more accurate, and whether the difference is a genuine contradiction or an intentional variation for different audiences. The [2025 arXiv work on contradiction detection in RAG systems](https://arxiv.org/abs/2504.00180) shows that LLMs outperform deterministic approaches on context-context conflict detection. [Global Consistency Checking with Noisy LLM Oracles](https://arxiv.org/html/2601.13600) (2026) introduces a divide-and-conquer algorithm for localizing minimal unsatisfiable subsets within natural-language claim sets, directly applicable to a large doc set.

#### How it might work

Three stages. **Stage 1** (deterministic): parse every doc page and extract tags and related-page frontmatter to build a page-pair graph. Pages linked by related fields or sharing two or more tags are candidate pairs. **Stage 2** (AI): for each pair, an LLM receives both pages and identifies passages describing the same concept, flagging contradictions. Chain-of-thought prompting improves accuracy. Output: `{page_a, page_b, concept, passage_a, passage_b, conflict_type, severity}`. **Stage 3** (deterministic): aggregate into a Markdown report sorted by severity. A writer reviews each flag, since some variation is intentional.

The [KnowledgeBase Guardian pattern](https://github.com/datarootsio/knowledgebase_guardian), which retrieves semantically similar documents first then runs LLM comparison only on high-similarity pairs, reduces LLM calls and makes the scan cost-effective at scale. [Promptfoo](https://github.com/promptfoo/promptfoo) can turn the highest-risk page pairs into a standing regression suite so the same contradiction checks run before every release. For a focused sprint, target a single high-churn content directory.

#### The value it will bring

Catches a class of documentation errors no existing linter can detect. Prevents cross-page contradictions before they reach users. Produces a structured contradiction report that becomes a recurring quality artifact: run it before a major release or after a wave of launches. Also identifies concepts that need a single authoritative page, feeding directly into content deduplication work.

#### Research backing

[Contradiction Detection in RAG Systems](https://arxiv.org/abs/2504.00180) (arXiv, April 2025) demonstrates that LLMs can detect context-context conflicts with meaningful accuracy and that chain-of-thought prompting improves results. [Global Consistency Checking with Noisy LLM Oracles](https://arxiv.org/html/2601.13600) (arXiv, January 2026) proves global consistency requires more than pairwise checks and proposes an adaptive algorithm. [Summary Factual Inconsistency Detection Based on LLMs](https://aclanthology.org/2025.findings-acl.1305.pdf) (ACL 2025) addresses the problem with evaluated benchmarks. [Acrolinx](https://www.acrolinx.com/) solves semantic consistency commercially at scale, confirming market validation.

#### Recommended tools

- [KnowledgeBase Guardian](https://github.com/datarootsio/knowledgebase_guardian) (Python, Dataroots): implements the retrieve-then-compare pattern. Embeds documents, retrieves similar pairs, uses an LLM to check for contradictions.
- [RAGAS](https://github.com/explodinggradients/ragas) (Python): provides faithfulness and factual correctness metrics adaptable to consistency scoring.
- [Promptfoo](https://github.com/promptfoo/promptfoo) (TypeScript, MIT): declarative LLM evaluation harness that can keep a contradiction test suite in CI once you have identified the high-risk page pairs.
- [Vale](https://vale.sh/) (Go): handles the deterministic string-matching layer already in the pipeline.

#### Related ideas

Extends [Content staleness detector](#content-staleness-detector) because stale content is the most common source of cross-page inconsistency. Complements [Content duplication detection](#content-duplication-detection) because redundant pages are the highest-risk pairs for semantic drift. Feeds into [Terminology consistency engine](#terminology-and-cross-reference-consistency-engine).

---

### Linter auto-fix with LLM rewriting
A three-layer auto-fix pipeline that combines deterministic formatting fixes ([markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2)), deterministic terminology swaps ([Vale](https://vale.sh/) substitution rules), and LLM-powered sentence rewrites for nuanced style violations (voice, tone, clarity, hedging, and passive construction) where the meaning must be preserved while the style is corrected. The pipeline runs the linter, sends flagged passages with their surrounding context to an LLM for rewriting, then re-scans iteratively until clean, following and extending the pattern established by Corelight's open-source [LLM-Ninja](https://github.com/corelight/LLM-Ninja).

#### The problem it solves

[Vale](https://vale.sh/) (or your docs linter) detects style violations but cannot fix them. For simple substitution rules, such as replacing "utilize" with "use" or enforcing specific term capitalization, deterministic auto-fix works. But for nuanced rules like passive voice, hedging language, and tone rules, the fix requires rewriting a sentence while preserving its technical meaning. Without auto-fix, each violation requires a writer to manually open the file, understand the context, rewrite the passage, and verify the fix. If your team's goal is to enforce linter rules in the build process once errors reach zero, auto-fix is the fastest credible path to that goal.

#### Why AI is needed

Rewriting "It should be noted that the configuration might need to be updated depending on your setup" into "Update the configuration for your setup," while preserving technical specificity, requires understanding the sentence's meaning, not matching a pattern. Deterministic find-and-replace handles substitution rules: mechanical swaps. But passive voice rules fire on sentences where the passive construction exists for a reason (avoiding "you" in conceptual explanations) versus sentences where it's weak writing. An LLM can distinguish between "Authentication is handled by the coordination server" (appropriate passive; the subject is the server) and "The settings can be configured by clicking the gear icon" (inappropriate passive; the user is the agent). That judgment is what makes flat find-and-replace dangerous and an LLM necessary.

#### How it might work

Three layers, each building on the previous.

**Layer 1** (deterministic, milliseconds): `markdownlint-cli2 --fix` handles formatting violations. No AI required.

**Layer 2** (deterministic, milliseconds): a script applies linter substitution rules as direct string replacements. Handles all mechanical swaps without calling an LLM.

**Layer 3** (AI, seconds per file): for violations that survive Layers 1 and 2, the pipeline runs the linter in JSON mode, extracts violation context (the flagged sentence plus 2 surrounding sentences), and sends batched rewrite requests to an LLM with a prompt specifying the rule violated, the style guide rationale, and an instruction to preserve technical accuracy. The rewritten passage replaces the original. The linter runs again. If new violations appear, the loop repeats, stopping after 3 consecutive iterations without improvement, matching the [LLM-Ninja](https://github.com/corelight/LLM-Ninja) behavior. In CI, [reviewdog](https://github.com/reviewdog/reviewdog) can publish accepted rewrites back to the PR as suggestions instead of pushing directly, which keeps human approval in the loop.

For a focused sprint, target a single high-violation directory as a batch fix, demonstrating end-to-end pipeline without requiring CI integration.

#### The value it will bring

Can reduce a backlog of linter violations to a manageable number in one automated batch pass, unlocking the path to promoting suppressed rules to enforced. Ongoing, it prevents style debt from accumulating by fixing violations as part of the authoring workflow. Cross-team contributors submitting PRs benefit immediately: their PRs pass style checks without requiring manual revision from the docs team.

#### Research backing

Corelight's [LLM-Ninja](https://github.com/corelight/LLM-Ninja), documented in their [September 2025 blog post](https://corelight.com/blog/microsoft-style-guide-llm), demonstrates the Vale + LLM iterative rewrite pattern in production against the Microsoft Style Guide, with support for Claude, ChatGPT, and Gemini. [Grafana's writers' toolkit](https://grafana.com/docs/writers-toolkit/review/lint-prose/) and [Elastic's Vale integration](https://www.elastic.co/docs/contribute-docs/vale-linter) demonstrate that enterprise docs teams have standardized on Vale. [GetStream's 2025 linting guide](https://getstream.io/blog/linting-documentation-with-vale/) documents the ROI of prose linting in production environments.

#### Recommended tools

- [LLM-Ninja](https://github.com/corelight/LLM-Ninja) (Python): the direct architectural predecessor. Runs Vale in JSON mode, extracts violations, generates rewrite prompts, calls an LLM, applies rewrites, and iterates until clean.
- [markdownlint-cli2](https://github.com/DavidAnson/markdownlint-cli2) (Node.js): handles Layer 1 deterministic formatting fixes.
- [Vale](https://vale.sh/) (Go): the linting engine. Run in `--output=JSON` mode for the machine-readable violation list that feeds the AI rewrite layer.
- [reviewdog](https://github.com/reviewdog/reviewdog) (Go, MIT): turns linter and rewrite output into inline PR comments or multi-line suggestions, which makes the auto-fix workflow easier to adopt incrementally.

#### Related ideas

Extends [Authoring-time structural validation hooks](#authoring-time-structural-validation-hooks) by adding an auto-fix pass before the lint check runs. Shares linter infrastructure with [Cross-page semantic consistency checker](#cross-page-semantic-consistency-checker) and [Terminology consistency engine](#terminology-and-cross-reference-consistency-engine).

---

### Content duplication detection
A two-part system that identifies redundant documentation at both the page level and the snippet level. Page-level detection uses semantic similarity scoring with embeddings to flag pages with significant content overlap for consolidation review. Snippet-level awareness deploys as a three-tier pipeline: a deterministic linter rule layer catches known snippet substitutions in CI; an embeddings-based PR comment layer flags paragraphs semantically similar to existing snippets; and a CLI search tool lets authors query the snippet library before writing. The initial audit uses [jscpd](https://github.com/kucherenko/jscpd) to scan all topics and produce the candidate list.

#### The problem it solves

Documentation sets accumulate duplication over time: redundant install guides, overlapping quickstarts, and the same prerequisite block written slightly differently in a dozen places. When a correction is applied to one copy but not others, duplicated content drifts out of sync and causes incidents. Each new contributor doesn't know what pages already exist and may unknowingly create overlap. The current process for finding duplicates is manual content auditing, which doesn't scale.

#### Why AI is needed

Deterministic heuristics (shared headings, overlapping keyword density) catch the most obvious cases. Semantic duplication, where two pages describe the same thing in different words, requires understanding meaning. An LLM can identify that a page about "getting started on macOS" and a page about "installing the client on Mac" cover the same ground even if they share no headings or links. For snippet-level detection, a linter regex rule can match exact text against known snippets, but it misses paraphrased versions. Embeddings-based comparison detects meaning-equivalent content that differs in surface text.

#### How it might work

Two complementary scopes sharing infrastructure.

**Page-level duplication detection:** embed all pages using a lightweight embedding model such as [FastEmbed](https://github.com/qdrant/fastembed) or [sentence-transformers](https://github.com/UKPLab/sentence-transformers) and compute pairwise cosine similarity for pages in the same product area or content type. Page pairs exceeding a configurable threshold (starting at 0.80) are flagged for review. Run as a monthly scheduled CI job and as a check on new pages.

**Snippet awareness pipeline:** three tiers. Tier 1 (deterministic): create a linter rule for each stable snippet. The rule's regex matches inline text the snippet replaces and suggests the reusable component. Tier 2 (AI): embed all snippets. On PR, embed new content at the paragraph level. Post a PR comment when similarity exceeds threshold: "This paragraph is 87% similar to the standard prerequisites snippet. Consider using it instead." Tier 3 (AI): a CLI tool for pre-authoring search against the snippet library.

The initial audit uses [jscpd](https://jscpd.dev/) to scan the full doc set using Rabin-Karp copy/paste detection, producing the candidate list that informs which linter rules to write and which snippets to prioritize.

#### The value it will bring

One-time: identifies the existing duplication backlog and snippet candidates. Ongoing: prevents new duplication at both page and snippet level. Snippet awareness shifts reuse from "authors must remember" to "the system tells them," addressing the root cause of content drift from duplication.

#### Research backing

[NVIDIA NeMo Curator's semantic deduplication documentation](https://docs.nvidia.com/nemo-framework/user-guide/24.09/datacuration/semdedup.html) validates the embeddings + cosine similarity approach, reporting 20-50% dataset size reduction while maintaining quality. [SemHash](https://github.com/MinishLab/semhash) is the most purpose-built Python library for semantic deduplication using embeddings. [Document360's duplicate content detection documentation](https://docs.document360.com/docs/content-duplication-detection) provides a concrete production implementation spec. The [PD3 duplication detection framework (arXiv, 2025)](https://arxiv.org/html/2505.17492) demonstrates that dual feedback (qualitative summaries and quantitative scores) helps experts verify results efficiently.

#### Recommended tools

- [jscpd](https://github.com/kucherenko/jscpd) (JavaScript/TypeScript, MIT): Rabin-Karp copy/paste detection across 170+ formats including MDX. Integrates with GitHub Actions via [MegaLinter](https://github.com/oxsecurity/megalinter).
- [SemHash](https://github.com/MinishLab/semhash) (Python, MIT): fast semantic deduplication using Model2Vec embeddings. Runs without a GPU, scales to millions of records.
- [FastEmbed](https://github.com/qdrant/fastembed) (Python, Apache 2.0): fast CPU-first embeddings for paragraph or page similarity jobs that need to run in CI or on a writer laptop.
- [sentence-transformers](https://github.com/UKPLab/sentence-transformers) (Python, Apache 2.0): paragraph-level embedding models for the snippet awareness pipeline.

#### Related ideas

Pairs with [Documentation coverage map](#documentation-coverage-map) for completeness analysis; duplication detection and coverage mapping are two sides of the same structural audit. Complements [Terminology consistency engine](#terminology-and-cross-reference-consistency-engine) for consistency enforcement.

---

### Documentation coverage map
A pipeline that systematically compares every user-facing surface in the codebase (CLI subcommands and flags, API endpoints, admin console routes, configuration keys, error messages) against the documentation set to produce a prioritized gap report. It uses AI agents for extraction and quality assessment, then deterministic scripts to merge results, track deltas between runs, and optionally create tickets for gaps above a configurable severity threshold. Each run produces a Markdown report and a delta diff showing newly opened and newly closed gaps since the previous run.

#### The problem it solves

Most documentation sets have never been systematically compared against the codebase to identify what's documented and what isn't. Teams discover gaps reactively through user complaints and post-mortems rather than proactively through coverage analysis. Features get listed as unsupported when they've already shipped. CLI docs lag behind the CLI itself. There is no baseline coverage metric, no way to tell whether coverage is improving or degrading sprint over sprint, and no prioritization signal beyond recency of customer complaint.

#### Why AI is needed

Extracting every user-facing surface from a codebase and cross-referencing each against documentation requires semantic matching that deterministic tools cannot perform. A CLI flag named `--advertise-exit-node` might be documented on a page titled "Exit nodes" that never uses the exact flag name in its body text. A health message defined in the codebase might be covered in a troubleshooting page that uses entirely different phrasing. AI performs the fuzzy matching between code-level identifiers and documentation-level descriptions that regex and grep cannot handle. The [CodeWiki research framework](https://arxiv.org/abs/2510.24428) demonstrates that repository-level documentation analysis achieves approximately 73% semantic reliability at scale, validating AI-driven codebase-to-docs matching as a tractable problem.

#### How it might work

Three stages.

**Stage 1**, gap extraction (AI): an agent crawls the codebase to extract every user-facing surface. Use surface-specific extractors before the AI pass: [oasdiff](https://github.com/oasdiff/oasdiff) for OpenAPI specs, generated CLI `--help` snapshots or [tree-sitter](https://tree-sitter.github.io/tree-sitter/) parsers for command and config definitions, and route manifests for UI paths. The agent classifies each extracted surface by audience (user, developer, integrator, operator) and documentation type, then cross-references against the doc set to identify complete gaps (features not documented at all). Confidence scoring flags uncertain classifications.

**Stage 2**, quality assessment (AI): a second agent runs against existing docs in gap-adjacent areas to identify partial gaps: pages that exist but don't adequately cover the feature. This catches the pattern where a page exists but states incorrect information rather than covering the feature completely.

**Stage 3**, merge, rank, and output (deterministic): scripts combine both outputs into a unified coverage report. Gaps are ranked by git commit recency using [git-fame](https://github.com/casperdcl/git-fame) or [mergestat-lite](https://github.com/mergestat/mergestat-lite); recently changed, undocumented code areas are highest priority. Delta tracking stores each run's output in the repo. A diff script surfaces new gaps, closed gaps, and regressions, turning a one-time snapshot into a trackable coverage trend.

A focused sprint scope: extract CLI surfaces from your CLI source directory and match against CLI reference docs. CLI `--help` output is structured and machine-readable, making it the most tractable starting surface.

#### The value it will bring

First-ever complete picture of documentation coverage across all topics and all known codebase surfaces. Prioritized backlog of documentation gaps based on code activity, not guesswork. Baseline coverage metric the team can track over time. Delta tracking enables sprint-over-sprint measurement of coverage improvement.

#### Research backing

[CodeWiki (arXiv 2510.24428)](https://arxiv.org/abs/2510.24428) demonstrates that hierarchical, multi-agent AI processing of large-scale codebases achieves 73.65% semantic reliability in documentation tasks. [Serena MCP](https://smartscope.blog/en/generative-ai/claude/serena-mcp-coding-agent/) provides a precedent for symbol-level semantic understanding across 30+ languages. The OpenAPI/Swagger coverage report pattern demonstrates that API surface-to-docs comparison is a proven pattern; this extends it from API reference to full product documentation coverage.

#### Recommended tools

- [oasdiff](https://github.com/oasdiff/oasdiff) (Go, Apache 2.0): compares OpenAPI specs and produces breaking-change or changelog reports. Ideal for extracting API surfaces before the semantic matching pass.
- [tree-sitter](https://github.com/tree-sitter/tree-sitter) (C/Rust, MIT): incremental parser generator with mature grammars across languages. Useful for extracting CLI definitions, config constants, and route declarations from source instead of scraping text.
- [mergestat-lite](https://github.com/mergestat/mergestat-lite) (Go/SQLite, MIT): SQL-queryable git history for commit-recency ranking and delta tracking.
- [git-fame](https://github.com/casperdcl/git-fame) (Python): blame-based ownership analysis by file extension. Identifies zero-owner or single-owner areas at the highest long-term coverage risk.

#### Related ideas

Shares infrastructure with [Documentation blast radius analyzer](#documentation-blast-radius-analyzer): the feature-to-docs dependency map produced by this pipeline is exactly the input that blast radius analysis needs. Complements [Content staleness detector](#content-staleness-detector); that idea finds what's stale, this idea finds what's missing entirely.

---

## Review and workflow automation

### PR review triage and semantic change detection
A CI-based bot that automatically triages incoming documentation PRs using a two-layer approach: deterministic heuristics classify changes by diff characteristics (diff size, step reordering, warning block removal, imperative verb changes); an AI layer performs semantic analysis to classify each change as cosmetic, additive, behavioral, or destructive. The bot posts a structured review comment to every PR so human reviewers know where to focus.

#### The problem it solves

A small docs team managing incoming PRs from engineers across the organization, with no automated assignment or initial triage, spends triage time before review time even begins. PR reviewers allocate attention uniformly across all changes because there is no signal for which changes matter: a typo fix and a behavioral change to an installation procedure look identical in a diff. Reviewers either scrutinize everything (slow) or skim everything (risky). Incidents where incorrect behavioral claims went through review undetected illustrate what a semantic classification layer would prevent. Linters catch mechanical issues only; they cannot classify whether a changed procedure is correct, whether a behavioral change is accurately described, or whether a removed step is load-bearing.

#### Why AI is needed

Classifying whether a change is cosmetic (whitespace, typo), additive (new information that doesn't invalidate existing content), behavioral (changed procedure, recommendation, or command syntax), or destructive (removed a critical step, deleted a warning, altered a security boundary) requires understanding the *meaning* of the change, not the text diff. A change from `tool up --exit-node=100.x.x.x` to `tool set --exit-node=100.x.x.x` is behavioral (the subcommand changed) but a simple diff classifier would score it as a minor edit. An LLM reading both lines understands the semantic significance. The AI layer classifies; deterministic logic acts on the classification.

#### How it might work

A GitHub Actions workflow triggers on `pull_request` events for changes to your content directory.

**Layer 1** (deterministic heuristics, ships first): classify the diff using measurable characteristics: diff size in lines, step reordering detected by comparing numbered list positions, imperative verb changes, and removal of warning or caution components. This handles the majority of clear-cut cases without any AI call.

**Layer 2** (AI semantic classification): for PRs the heuristic layer scores as ambiguous or medium-risk, the diff is sent to an LLM along with the relevant doc type template and a sample of related pages. The LLM classifies each changed section and produces a structured review comment covering risk level, structural adherence, terminology consistency, and potential factual inconsistencies.

The classification posts as a PR comment: a risk badge followed by a structured breakdown. Behavioral and destructive changes get flagged for mandatory human review; cosmetic changes can be auto-approved or collapsed. [reviewdog](https://github.com/reviewdog/reviewdog) or [Danger JS](https://danger.systems/js/) can publish the resulting badge, summary, and suggested follow-up actions directly into the PR conversation. The [coderabbitai/ai-pr-reviewer](https://github.com/coderabbitai/ai-pr-reviewer) GitHub Action provides a reference implementation for the hunk-level review architecture.

#### The value it will bring

Reduces average time between PR creation and first meaningful feedback. Catches semantic issues earlier, before human review time is spent on them. Standardizes the quality of initial feedback regardless of which team member is reviewing. [Greptile's AI code review benchmarks](https://www.greptile.com/benchmarks) report teams catching up to 3x more bugs while merging 50-80% faster after adding semantic review.

#### Research backing

A [large-scale empirical study of GenAI-based code review actions on GitHub](https://arxiv.org/html/2508.18771v1) analyzed over 22,000 review comments and found that hunk-level review actions most closely resemble human reviews. [CodeRabbit](https://www.coderabbit.ai/), connected to over 2 million repositories, demonstrates the production-grade scalability of AI PR review bots. [Greptile's 2025 benchmark](https://www.greptile.com/benchmarks) provides rigorous public evaluation of AI code review tools.

#### Recommended tools

- [coderabbitai/ai-pr-reviewer](https://github.com/coderabbitai/ai-pr-reviewer) (TypeScript, GitHub Action): open-source reference implementation for AI-based PR summarization and hunk-level review.
- [Doc Detective](https://github.com/doc-detective/doc-detective) (JavaScript/Node.js): adds a verification layer. When the classifier labels a change as "behavioral," Doc Detective can execute the described steps to confirm they work.
- [Vale](https://vale.sh/) (Go): handles the mechanical first layer; the triage bot extends it with semantic classification.
- [reviewdog](https://github.com/reviewdog/reviewdog) (Go, MIT): turns classifier output into GitHub checks or inline comments so the triage signal is visible without building a custom bot UI.
- [Danger JS](https://danger.systems/js/) (JavaScript, MIT): lightweight PR automation framework for posting risk summaries, assigning labels, or blocking merges when the classifier marks a change as destructive.

#### Related ideas

Extends [Code-to-docs change detection pipeline](#code-to-docs-change-detection-pipeline) by adding semantic classification to the PR review layer. Shares classification infrastructure with [Auto-merge for mechanical edits](#auto-merge-for-mechanical-edits), which acts on the lowest-risk classification automatically.

---

### Auto-merge for mechanical edits
A CI-integrated classification and auto-merge system that identifies provably safe, mechanical documentation changes (version bumps matching semver patterns, URL swaps where the old URL 404s and the new URL 200s, linter auto-fix changes matching known-safe rules) and merges them without requiring human review. The boundary between "mechanical" and "semantic" edits is derived from AI analysis of the team's PR history, then enforced by deterministic pattern matching in CI. AI defines the safe-edit class once; GitHub's [merge queue](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) enforces it forever.

#### The problem it solves

The docs team reviews every PR with the same level of scrutiny regardless of change type. A PR that swaps a broken URL for a working one competes for reviewer attention alongside a PR that restructures a tutorial. This creates a bottleneck where predictable, verifiable changes consume reviewer time that should be reserved for judgment-intensive changes. The problem is analogous to dependency management before Dependabot auto-merge: every patch version update required a human reviewer even though the risk was effectively zero, until the pattern was recognized and the category was systematically automated. In one team's experience, a significant portion of the review queue consists of mechanical changes.

#### Why AI is needed

The safe-edit boundary cannot be hardcoded from first principles. Defining it requires analyzing the team's actual PR history to identify which change patterns have never caused a regression, a classification task over hundreds of historical PRs that requires reading comprehension, not file diffing. An LLM can analyze historical PRs, compare changes to review outcomes (merged without revision, merged after revision, closed), and identify the structural characteristics of consistently safe changes. This produces a formal specification of the safe-edit class: a set of deterministic predicates that any future PR either satisfies or doesn't. Once defined, the enforcement layer is entirely deterministic: pattern matching, HTTP status checks, and linter rule allow-lists. No AI at runtime.

#### How it might work

Two phases.

**Phase 1** (AI, one-time): analyze the last 12 months of merged PRs using the GitHub API. Extract diffs, files changed, and whether each PR merged without revision. Pass to an LLM: "Identify structural characteristics of PRs that merged without revision requests. Which patterns are both consistent and low-risk?" Output: a formal spec of the safe-edit class as deterministic predicates: (a) only doc files changed, (b) changes match semver version bump patterns, (c) changes match a URL swap where old URL returns 404 and new URL returns 200, (d) diff matches known-safe linter auto-fix patterns.

**Phase 2** (deterministic, CI): implement predicates as a GitHub Actions workflow. If all changes satisfy safe-edit predicates AND all existing CI checks pass, the workflow calls the [GitHub auto-merge API](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request). Any change that fails any predicate routes to normal human review unchanged. A merge queue or [Mergify](https://docs.mergify.com/) rule set can enforce the same branch protections on auto-merged docs PRs as on human-reviewed ones. Start with the two highest-confidence predicates (URL swaps and version bumps).

#### The value it will bring

Faster time-to-merge for mechanical changes. URL swaps and version bumps that sit in the review queue for hours merge within minutes of passing CI. More reviewer attention available for judgment-intensive PRs. Establishes a trust framework with a well-defined audit trail: every auto-merged PR logs which predicates it satisfied.

#### Research backing

[GitHub's auto-merge documentation](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/automatically-merging-a-pull-request) and [merge queue documentation](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/configuring-pull-request-merges/managing-a-merge-queue) establish the infrastructure. [Dependabot's auto-merge pattern](https://docs.github.com/en/code-security/dependabot/working-with-dependabot/automating-dependabot-with-github-actions) is the exact risk calculus applied to documentation. [Renovate's automerge configuration](https://docs.renovatebot.com/key-concepts/automerge/) provides the most mature example of configurable auto-merge policies with audit trails.

#### Recommended tools

- [peter-evans/enable-pull-request-automerge](https://github.com/peter-evans/enable-pull-request-automerge) (JavaScript, MIT): GitHub Action that enables auto-merge programmatically.
- [Mergify](https://docs.mergify.com/) (commercial): rules engine and merge queue for GitHub pull requests. Useful if you want stronger policy management, auditability, and queueing than a single custom Action provides.
- [Renovate](https://github.com/renovatebot/renovate) (TypeScript, AGPL-3.0): the most mature example of rule-based auto-merge with audit trails.

#### Related ideas

Complements [PR review triage](#pr-review-triage-and-semantic-change-detection), which classifies all PRs by risk level; this idea auto-merges the lowest-risk category. Shares classification infrastructure with [Linter auto-fix](#linter-auto-fix-with-llm-rewriting), since linter auto-fix outputs are one defined safe-edit category. Builds on [Authoring-time validation hooks](#authoring-time-structural-validation-hooks), since pre-commit validation is one signal that a PR's changes are within the safe-edit boundary.

---

### Authoring-time structural validation hooks
A set of four to six deterministic pre-commit hooks that catch structural documentation errors at authoring time, before they enter the review queue. The hooks validate frontmatter field completeness against a schema, heading hierarchy, image reference integrity, code block language tag presence, internal link reference format compliance, and redirect completeness. Deployed through the [pre-commit framework](https://pre-commit.com/) and configured to start in warning mode so existing violations can be cleared without blocking contributors. Each hook permanently eliminates one category of structural error that currently slips through review and accumulates as technical debt.

#### The problem it solves

Many docs-as-code repositories lack automated checks for common structural errors at authoring time. Incomplete frontmatter fields (missing date, summary, or content type on new pages), broken heading hierarchies, orphaned image references, missing code block language tags, and incorrectly formatted internal links all slip through review and accumulate as technical debt. Cross-team contributors, engineers and PMs who submit PRs but don't know the content authoring guide, are most prone to structural errors. A CI linter workflow catches style errors on PR, but there is no local authoring-time equivalent that catches structural problems before a PR is even opened.

#### Why AI is needed

AI writes the hooks. Each hook is a deterministic script, but generating correct, edge-case-handling validators for MDX frontmatter schemas, heading hierarchies with component interruptions, and image reference patterns benefits from AI-assisted development. A human developer might spend a full day per hook getting edge cases right: import statements that look like heading levels, component blocks that interrupt heading sequences, frontmatter fields conditional on content type value. AI generates all hooks in a single session, handles edge cases from the start, and produces validators that match actual patterns in your repo rather than generic Markdown validators. This is meta-tooling: AI creates the deterministic tools that run forever without further AI involvement.

#### How it might work

A focused sprint could generate four to six new hooks using AI, then deploy them to your pre-commit configuration in warning mode. Plausible hook set:

1. **Frontmatter completeness validator**: validates that required fields (`title`, `summary`, freshness date) are present and that conditional fields use allowed values from the schema
1. **Heading hierarchy checker**: validates heading levels don't skip (H1 to H3 without H2) and that the first heading matches the frontmatter title, accounting for component blocks between headings
1. **Image reference validator**: checks that every image import resolves to an actual file and that no unreferenced images exist in the directory
1. **Code block language tag validator**: flags code blocks without a language identifier
1. **Internal link reference format**: validates that internal links use the correct format and prefixes per your conventions
1. **Redirect completeness check**: validates that pages being renamed or moved have corresponding redirect entries

Tools reduce the custom hook count. [rumdl](https://github.com/rvben/rumdl) (Rust, installable via pip/brew/npm) implements 71 lint rules with MDX flavor auto-detection, covering heading hierarchy and code block language tag validation. It is [5x faster than markdownlint-cli2](https://github.com/rvben/rumdl-vscode) and has a [pre-commit hook repo](https://github.com/rvben/rumdl-pre-commit). [remark-lint-frontmatter-schema](https://github.com/JulianCataldo/remark-lint-frontmatter-schema) validates frontmatter against a JSON schema. [lychee](https://github.com/lycheeverse/lychee) covers link integrity, and [check-jsonschema](https://check-jsonschema.readthedocs.io/en/latest/precommit_usage.html) covers redirect manifests or frontmatter files expressed as YAML or JSON.

#### The value it will bring

Each hook permanently eliminates one category of structural error from entering the review queue. Cross-team contributors get immediate feedback on structural problems rather than learning about them in PR review comments. The review bandwidth spent catching missing frontmatter fields and broken heading hierarchies gets redirected to content review.

#### Research backing

[Gatlen Culp's 2025 pre-commit hooks guide](https://gatlenculp.medium.com/effortless-code-quality-the-ultimate-pre-commit-hooks-guide-for-2025-57ca501d9835) quantifies the compounding effect: catching errors at commit time costs ~1 second; catching them in CI costs minutes; catching them in PR review costs hours. Google's [2024 DORA report](https://www.digitalapi.ai/blogs/api-documentation-generator-how-to-auto-generate-docs-in-2026) found that AI-assisted documentation workflows need automated validation to maintain accuracy; pre-commit hooks are that validation layer at the authoring stage.

#### Recommended tools

- [rumdl](https://github.com/rvben/rumdl) (Rust): 71 MDX-aware lint rules, auto-detects `.mdx` flavor, with an official [pre-commit hook integration](https://github.com/rvben/rumdl-pre-commit).
- [remark-lint-frontmatter-schema](https://github.com/JulianCataldo/remark-lint-frontmatter-schema) (JavaScript/remark): validates frontmatter against a JSON schema.
- [check-jsonschema](https://check-jsonschema.readthedocs.io/en/latest/precommit_usage.html) (Python, MIT): validates YAML or JSON files against JSON Schema inside `pre-commit`. Useful for frontmatter sidecars, redirects manifests, and other structured docs metadata.
- [lychee](https://github.com/lycheeverse/lychee) (Rust, Apache 2.0 or MIT): fast link checker for Markdown, HTML, and plain text. Good fit for catching broken external references before a PR is opened.
- [pre-commit-hooks](https://github.com/pre-commit/pre-commit-hooks) (Python): the official pre-commit hooks collection including `check-yaml`, `check-json`, `check-symlinks`, and `trailing-whitespace`.

#### Related ideas

Shares quality-automation goals with [Linter auto-fix with LLM rewriting](#linter-auto-fix-with-llm-rewriting), which enforces style rules; this idea catches structural errors before they enter the pipeline. Complements [Documentation scaffolding CLI](#documentation-scaffolding-cli), where scaffolding ensures new pages start with the correct structure and the hooks enforce that structure was not broken during editing.

---

## Testing and validation

### Conversational documentation testing
An automated testing system that treats documentation like code by running comprehension and procedure tests against published pages in CI. It uses LLM-as-judge to verify that pages contain complete answers to the questions users bring to them, and uses [Doc Detective](https://docs.doc-detective.com/) to execute documented procedures and confirm they produce expected results. Tests are defined once (either by an LLM generating specs from existing documentation or by writers authoring them inline) then run deterministically in every PR, catching regressions before users encounter them.

#### The problem it solves

There is no way to test whether the docs actually answer the questions users bring to them. The team publishes content and hopes it works. The only feedback signals are indirect: support tickets, thumbs-down ratings, and occasional user complaints. This is a testing gap that doesn't exist for code: codebases have unit tests, integration tests, and CI. Documentation has none. When a writer rewrites a page, no automated check confirms the rewrite preserved the critical steps. When a product change breaks a documented workflow, no test catches it before a user reports it.

#### Why AI is needed

Testing whether a document answers a question like "How do I set up feature X on Linux?" requires reading comprehension and reasoning about whether the answer is complete, correct, and findable within the page. No deterministic test can do this. An LLM evaluating a page can reason about completeness, identify missing steps, and flag when an answer is technically present but buried. [DeepEval](https://github.com/confident-ai/deepeval)'s faithfulness and answer-relevancy metrics quantify this judgment as a score that can block or warn in CI. The procedure-testing half uses an LLM to generate executable test specs from documentation; the tests then run deterministically. AI generates the spec; deterministic execution validates.

#### How it might work

Two testing approaches.

**Comprehension testing:** for high-traffic pages, define a set of questions per page and store them in a YAML spec file alongside the documentation. In CI, the content and question list are fed to an LLM with a system prompt asking it to answer each question and score its confidence that the answer is complete and findable. [RAGAS faithfulness and answer-relevancy metrics](https://docs.ragas.io/en/stable/concepts/metrics/available_metrics/) provide the scoring framework. [Promptfoo](https://github.com/promptfoo/promptfoo) is a practical fit if you want model-vs-model comparisons or a simple YAML-based eval harness before adopting a Python test framework. A score below threshold generates a warning annotation on the PR.

**Procedure testing:** [Doc Detective](https://docs.doc-detective.com/)'s AI-assisted test generation reads documentation pages and identifies step-by-step procedures. It generates test specs (structured YAML defining link checks, UI interactions, and shell commands) that execute against the actual product. Doc Detective's `runShell` action can validate CLI outputs.

For a focused sprint: three pages with well-defined procedures, question sets authored by the team, and procedure specs generated by Doc Detective. Adding [textstat](https://pypi.org/project/textstat/) for readability scoring alongside comprehension testing creates a third quality dimension at minimal cost.

#### The value it will bring

Automated regression detection for documentation. When a page is rewritten, comprehension tests surface whether the rewrite preserved the information users need. Over time, the question sets become a living definition of what each page is supposed to answer: a machine-readable contract between writers and readers. [DeepEval's CI/CD integration guide](https://www.confident-ai.com/blog/how-to-evaluate-rag-applications-in-ci-cd-pipelines-with-deepeval) estimates a 20-question evaluation suite costs $0.50 to $3.00 per CI run.

#### Research backing

[Doc Detective's AI-assisted test generation documentation](https://www.docsastests.com/ai-assisted-test-generation) describes the exact pattern: an LLM converts procedures into executable test specifications that run deterministically. [LLM Testing in 2026](https://www.confident-ai.com/blog/llm-testing-in-2024-top-methods-and-strategies) (Confident AI) identifies traceability as the critical CI integration capability. [Generating High-Level Test Cases from Requirements using LLM](https://arxiv.org/html/2510.03641v1) (arXiv, 2025) validates that LLMs can generate meaningful test cases from requirements at industry scale.

#### Recommended tools

- [Doc Detective](https://github.com/doc-detective/doc-detective) (JavaScript/Node.js, Apache 2.0): the primary execution framework. Parses doc files, executes UI and CLI actions, reports PASS/FAIL. Includes [agent tools](https://github.com/doc-detective/agent-tools) for AI-assisted spec generation.
- [RAGAS](https://github.com/explodinggradients/ragas) (Python, Apache 2.0): faithfulness, answer-relevancy, and context-recall metrics for documentation completeness scoring.
- [DeepEval](https://github.com/confident-ai/deepeval) (Python, Apache 2.0): wraps RAGAS metrics in a PyTest-compatible framework with CI/CD integration.
- [Promptfoo](https://github.com/promptfoo/promptfoo) (TypeScript, MIT): declarative LLM evaluation harness with CI integrations. Useful for keeping a question bank and grading rubric in version control without writing custom test runners first.
- [textstat](https://pypi.org/project/textstat/) (Python, MIT): readability scoring across all major formulas.

#### Related ideas

Complements [Reader persona simulation](#reader-persona-simulation) because persona simulation reveals what questions users bring to a page, which defines what comprehension tests should ask. Shares CI infrastructure with [Authoring-time validation hooks](#authoring-time-structural-validation-hooks).

---

### Reader persona simulation
A usability testing tool that uses LLMs to role-play specific reader personas (new users, experienced admins, evaluators, desperate debuggers, developers) as they walk through a documentation page. Each simulated reader reports where they get confused, what background knowledge the page assumes, where they would abandon the page, and what questions the page left unanswered. It provides scalable, repeatable usability feedback without the cost or scheduling burden of real user studies.

#### The problem it solves

Documentation is written and reviewed by people who already understand the product. The author's curse of knowledge makes it nearly impossible to see a page the way a new user sees it. There is no scalable way to test whether a new user, an evaluator comparing products, or an admin troubleshooting under pressure can follow the docs to accomplish their goal. Usability testing with real users is expensive, slow, and impossible to run for every page update. The team writes for multiple audiences but has no mechanism to validate that each audience can use the docs successfully. The "desperate debugger" persona is particularly high-value: troubleshooting content that works when calm may be unusable under stress.

#### Why AI is needed

Simulating how a specific reader type would experience a document requires modeling human cognition, assumptions, prior knowledge, and failure modes. Deterministic tools can check style, count syllables, and measure reading grade level, but they cannot reason about whether a new user would understand a product-specific term without clicking away, or whether a troubleshooting page's resolution steps are findable under stress. The [UXAgent research](https://arxiv.org/html/2504.09407v2) (arXiv, April 2025) demonstrates that LLM agents can generate usability data at scale that correlates meaningfully with real user behavior.

#### How it might work

Define a small set of standard personas in YAML files (`persona-new-user.yaml`, `persona-experienced-admin.yaml`, `persona-evaluator.yaml`, `persona-developer.yaml`, `persona-desperate-debugger.yaml`), each specifying the persona's knowledge level, goals, constraints, and known failure modes. Feed a documentation page plus a persona file to an LLM. The LLM plays the persona reading the page, then produces a structured report: passages where the persona would get confused, assumptions the page makes that the persona wouldn't share, steps the persona would fail on, and the point where the persona would abandon the page. The report is formatted as inline comments with severity ratings.

Three tools exist for this: [Impersonaid](https://github.com/theletterf/impersonaid) (Python CLI) is an open-source CLI with YAML-defined personas that runs against any text file. [Promptfoo](https://github.com/promptfoo/promptfoo) can parameterize the same page against multiple persona files and produce diffable reports in CI. The [reader-simulator pattern](https://www.cherryleaf.com/2025/11/testing-documentation-with-ai-the-reader-simulator/) (CT Smith, Payabli) uses the Claude API and has been validated against documentation usability benchmarks. [Megapersonas](https://instrktiv.com/en/blog/tools-efficiency/user-testing-manuals-with-megapersonas/) simulates hundreds of realistic users simultaneously.

For a focused sprint: five high-traffic pages evaluated against all five personas, producing a structured report the team can use to prioritize content improvements.

#### The value it will bring

Surfaces navigation friction, missing context, and unclear procedures from perspectives the team cannot adopt naturally. Functions as scalable usability testing without the cost and lead time of real user studies. The [CHI 2025 Persona-L research](https://dl.acm.org/doi/10.1145/3706598.3713445) validates that LLM-generated personas can represent audiences with complex needs more accurately than traditional persona creation methods.

#### Research backing

[UXAgent: A System for Simulating Usability Testing with LLM Agents](https://arxiv.org/html/2504.09407v2) (arXiv, April 2025) validates the approach for web-based content at scale. [Persona-L: Leveraging LLMs and Ability-based Framework for Personas of People with Complex Needs](https://dl.acm.org/doi/10.1145/3706598.3713445) (CHI 2025) demonstrates methodological rigor. [PersonaGym](https://aclanthology.org/2025.findings-emnlp.pdf) (EMNLP 2025) provides a benchmark for evaluating how faithfully LLMs maintain persona characteristics.

#### Recommended tools

- [Impersonaid](https://github.com/theletterf/impersonaid) (Python, open-source): CLI tool for documentation testing with YAML-defined personas. Accepts any text file as input.
- [Promptfoo](https://github.com/promptfoo/promptfoo) (TypeScript, MIT): runs persona-specific prompts and assertions from YAML, which makes it a good harness for recurring usability simulations in CI.
- [textstat](https://pypi.org/project/textstat/) (Python, MIT): deterministic readability scoring that complements persona simulation.

#### Related ideas

Complements [Conversational documentation testing](#conversational-documentation-testing) because persona simulation reveals what users expect, which defines what comprehension tests should ask. The two share a testing philosophy but at different layers: persona simulation is qualitative and discovery-oriented; comprehension testing is quantitative and regression-oriented.

---

### Configuration example validator
A CI pipeline that extracts configuration examples (JSON, YAML, HCL, and other formats) from documentation using [doccmd](https://github.com/adamtheturtle/doccmd), then validates each example against product schemas using language-native validators like [`jq`](https://jqlang.org/), [`yamllint`](https://yamllint.readthedocs.io/), and format-specific tools. AI classifies which code blocks are validatable configuration examples versus illustrative snippets or pseudocode, enabling the deterministic validators to run correctly without manual tagging of every code block.

#### The problem it solves

Configuration examples are the most-copied content in technical documentation. Users don't read them; they copy-paste them. When they're wrong, the failure mode is silent: the configuration loads but behaves incorrectly, the user blames themselves, and a support ticket eventually surfaces what should have been a docs bug. One documented friction example involved a missing key in a configuration example that blocked a customer for days. No automated check exists to ensure configuration examples in the docs are valid against the schema they claim to implement. Errors are caught only when a user reports them.

#### Why AI is needed

The validators themselves are deterministic: `jq --exit-status`, `yamllint`, and format-specific validators can each verify a configuration example once they receive it. The hard part comes before validation. Across hundreds of topics, code blocks range from genuine configuration examples to illustrative snippets, pseudocode, command-line output, and partial excerpts that are intentionally incomplete. A regex matching all JSON code blocks would submit pseudocode to the validator, generating hundreds of false failures. AI classifies each code block: is this a complete, copy-pasteable configuration example? If yes, which schema does it target? This classification runs once (or on new pages) and produces a manifest that deterministic validators consume on every PR. AI creates the routing; validators run the checks.

#### How it might work

Three stages.

**Stage 1** (AI, one-time per page): an LLM scans each doc file and produces a classification manifest: `{file, block_line, type, validator, skip_reason}`. Blocks tagged as illustrative or partial receive a skip reason and are excluded from validation. The manifest is committed to the repo and updated incrementally.

**Stage 2** (deterministic, extraction): [doccmd](https://github.com/adamtheturtle/doccmd) reads the manifest and extracts only classified-valid blocks, creating temporary files with accurate line numbers.

**Stage 3** (deterministic, validation): validators run per schema type. Use [check-jsonschema](https://check-jsonschema.readthedocs.io/en/latest/precommit_usage.html) or [Ajv](https://ajv.js.org/json-schema) for JSON and YAML schemas, [CUE](https://cuelang.org/) when the product already expresses config contracts in CUE, and format-specific validators for HCL, TOML, or Kubernetes manifests. Failures report the doc file path and line number, linking the error directly to the source. The full pipeline runs as a GitHub Action alongside existing linter workflows. Start non-blocking to establish a baseline, then promote to blocking once violations are cleared.

#### The value it will bring

Eliminates an entire class of documentation errors. Every classified configuration example becomes guaranteed-valid at merge time. The validator runs in CI forever with zero ongoing maintenance after setup. The classification manifest is also a valuable standalone artifact: a machine-readable map of every configuration example in the docs.

#### Research backing

[doccmd](https://adamtheturtle.github.io/doccmd/) is purpose-built for running validators against code blocks in documentation with accurate line-number reporting. [Manny Silva's "Docs as Tests" framework](https://www.docsastests.com/docs-as-tests/concept/2024/01/09/intro-docs-as-tests.html) articulates the core principle: "each doc is a test suite, each procedure a test case, each step an assertion." [Docsie's 2025 analysis of documentation drift](https://www.docsie.io/blog/glossary/documentation-drift/) notes that configuration examples are particularly vulnerable because they are copied from real configurations at a point in time and never updated as schemas evolve.

#### Recommended tools

- [doccmd](https://github.com/adamtheturtle/doccmd) (Python): extracts code blocks from documentation and runs arbitrary commands against them, with line-number-accurate error reporting.
- [check-jsonschema](https://check-jsonschema.readthedocs.io/en/latest/precommit_usage.html) (Python, MIT): validates YAML and JSON files against JSON Schema. Useful when docs examples map cleanly to schemas already published by the product team.
- [Ajv](https://ajv.js.org/json-schema) (JavaScript, MIT): high-performance JSON Schema validator for Node-based docs pipelines or GitHub Actions.
- [CUE](https://cuelang.org/) (Go, Apache 2.0): validates configuration examples against a unified data schema language and can export to JSON or YAML when teams already use CUE in product configuration flows.
- [Doc Detective](https://github.com/doc-detective/doc-detective) (Node.js): validates configuration examples by executing them. Its `runShell` action can pipe JSON through `jq --exit-status`.

#### Related ideas

Shares a testing philosophy with [Conversational documentation testing](#conversational-documentation-testing); both apply the "docs as tests" principle at different layers. [Authoring-time validation hooks](#authoring-time-structural-validation-hooks) can run the same validators locally before a PR is opened.

---

## Content generation and authoring

### Alt text generation for screenshots
A vision-model pipeline that scans all documentation screenshots and generates contextual alt text by analyzing image content alongside surrounding page context: headings, adjacent paragraphs, and frontmatter fields. It operates in two modes: a batch blitz to backfill missing alt text across the full doc set as section-organized PRs, and an ongoing per-PR integration that generates draft alt text for new screenshots at submission time so writers review and confirm rather than write from scratch.

#### The problem it solves

Many screenshots across documentation sites lack alt text. Writing accurate, contextual alt text manually is slow (several minutes per image for complex UI surfaces), and the volume means it doesn't get done, leaving the docs inaccessible to screen reader users and non-compliant with [Section 508](https://www.section508.gov/) and [WCAG 2.1](https://www.w3.org/TR/WCAG21/). Enterprise procurement that evaluates accessibility compliance is a potential deal risk. Admin consoles and rapidly changing product UIs are high-churn surfaces: screenshots go stale quickly, and every screenshot update is an opportunity to add or verify alt text, which does not happen without tooling.

#### Why AI is needed

Describing a screenshot's visual content from raw pixels requires a vision model. No deterministic tool can look at a screenshot of an admin console and produce a meaningful description. OCR can extract visible text, but it cannot describe layout, hierarchy, visual relationships, or what the screenshot illustrates in context. The [FigurA11y](https://dl.acm.org/doi/10.1145/3640543.3645212) research showed that feeding surrounding text context alongside the image produces descriptions specific to the product and use case rather than generic. A screenshot of a settings panel described in isolation becomes "A settings panel with toggle switches"; described with page context it becomes "The DNS settings panel showing the feature enabled and the domain configured."

#### How it might work

**Phase 1** (blitz): a Python script scans all doc files for image imports, extracts surrounding context (page title, summary, nearest headings, adjacent paragraph), and assembles a prompt pairing the image bytes with context. Each image is sent to a vision API and the returned description is inserted as the alt attribute. Images where the model returns low confidence are flagged for review. Output is organized into section-based PRs so writers review batches.

**Phase 2** (ongoing): the CI workflow is extended with a check detecting new image imports without alt text, calling the same vision pipeline and posting draft descriptions as PR comments for writer approval. For privacy-sensitive screenshots or air-gapped teams, local vision models such as [LLaVA through Ollama](https://ollama.com/blog/vision-models) are good enough for first-draft captions with a human review gate.

For a focused sprint, scope to a single product area with a small number of pages to prove the pipeline end-to-end.

#### The value it will bring

Reduces per-image effort from several minutes to seconds of review. Makes full accessibility compliance achievable. The [Twilio case study](https://www.twilio.com/en-us/blog/introducing-accessible-ai-alt-text-images) processed 2,734 images with a 99.96% acceptance rate, saving approximately 900 hours of manual writing. The [AltGen 2025 research](https://dl.acm.org/doi/10.1145/3730436.3730449) achieved a 97.5% reduction in accessibility errors.

#### Research backing

[FigurA11y](https://dl.acm.org/doi/10.1145/3640543.3645212) (Allen AI, IUI 2024) demonstrates that surrounding text context produces significantly more accurate alt text. [AltGen](https://dl.acm.org/doi/10.1145/3730436.3730449) (ACM ICAI 2025) achieved 97.5% accessibility error reduction using a five-stage AI pipeline. An [independent 2026 build-time pipeline](https://sarthakmishra.com/blog/automating-image-alt-text) using Gemini Flash demonstrates the exact pattern: scan for images at build time, hash to detect changes, send to vision API, inject descriptions. [Microsoft Azure AI Vision's alt text overview](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/use-case-alt-text) provides confidence-threshold guidance.

#### Recommended tools

- [FigurA11y](https://github.com/allenai/figura11y) (Python/TypeScript, Allen AI): reference implementation for context-aware alt text generation. Research-grade, but the context-injection pattern is directly applicable.
- [Azure Image Analysis](https://learn.microsoft.com/en-us/azure/ai-services/computer-vision/use-case-alt-text) (managed API): enterprise-friendly captioning and OCR service with confidence scoring and deployment controls.
- [Ollama vision models](https://ollama.com/blog/vision-models) (local runtime): runs LLaVA locally for privacy-sensitive screenshots or offline first-draft captioning.
- [Claude Vision](https://docs.anthropic.com/en/docs/build-with-claude/vision) (managed API): strong general-purpose multimodal model when the screenshot needs layout understanding as well as text extraction.

#### Related ideas

Shares CI workflow extension patterns with [Authoring-time validation hooks](#authoring-time-structural-validation-hooks) and [Code-to-docs change detection](#code-to-docs-change-detection-pipeline).

---

### Documentation scaffolding CLI
A command-line tool that generates new documentation pages pre-populated with the correct frontmatter, heading structure, placeholder sections, and required components for a given content type. Run as `docs new --type how-to --topic "configure subnet routers"` to get a correctly structured file instantly. AI analyzes the content framework, existing page patterns, and frontmatter requirements to derive the optimal prompts and field defaults; the CLI itself is fully deterministic at runtime with no AI call per invocation.

#### The problem it solves

When a writer creates a new documentation page, they either start from scratch or copy an existing page and modify it. Both paths require knowing which template to use, which fields are required for that content type, which heading structure applies, and which components belong in the page. This knowledge is documented in a content authoring guide but requires reading a guide before writing a first sentence. Cross-team contributors (engineers submitting docs PRs for features they shipped) are most exposed: they don't know the difference between content types, which release status values are valid, or which components are required. The result is structural errors that require review comments and revision cycles.

#### Why AI is needed

AI designs the templates and analyzes the content framework. Scanning hundreds of documentation topics to identify which frontmatter field combinations actually appear in which content types, which components are required vs. optional per type, and what the typical heading hierarchy looks like for each content type is an AI-assisted pattern extraction task. The CLI itself is deterministic at runtime: `docs new --type how-to --topic "configure subnet routers"` generates from a template with no AI call. This is meta-tooling: AI creates the templates and CLI logic; the CLI uses them without further AI involvement.

#### How it might work

Two stages. **Stage 1** (AI, one-time): AI analyzes existing templates, content patterns across all topics, the frontmatter schema, and the component library. It produces a configuration file mapping each content type to its template, required fields with validation rules, optional fields with defaults, components with placeholder values, and heading structure. **Stage 2** (deterministic, the shipped artifact): a CLI tool built with [Hygen](https://github.com/jondot/hygen) (EJS templates, zero configuration), [Plop.js](https://plopjs.com/) (Handlebars templates, integrates as an npm script), or [Copier](https://github.com/copier-org/copier) when you want templates that can be updated over time. Hygen is the lower-friction choice: templates co-locate with the project, the syntax is simple enough for non-engineers to maintain, and it has no runtime dependencies beyond Node.js.

Example invocation:

```
$ docs new --type how-to --topic "configure subnet routers"
> Created: [your-content-dir]/how-to/configure-subnet-routers.mdx
```

The generated file has all required frontmatter, the standard heading structure with placeholder sections, and component placeholders.

#### The value it will bring

Eliminates structural errors at authoring time for all new pages. Saves an estimated 15 to 20 minutes per new page for contributors unfamiliar with the content framework. Templates and CLI become the canonical reference for the content framework; improvements to templates automatically benefit all future pages.

#### Research backing

The scaffolding CLI pattern is well-established: [Rails generators](https://guides.rubyonrails.org/generators.html), [Angular CLI `ng generate`](https://angular.io/cli/generate), and [Hugo archetypes](https://gohugo.io/content-management/archetypes/) all apply the same model. [Hugo archetypes](https://gohugo.io/content-management/archetypes/) are the most direct precedent: template-based documentation scaffolding with frontmatter defaults and content structure per content type. [Hygen's CSS-Tricks overview](https://css-tricks.com/file-scaffolding-with-hygen/) confirms language-agnostic file scaffolding is a mature pattern.

#### Recommended tools

- [Hygen](https://github.com/jondot/hygen) (JavaScript/EJS, npm): lightweight scaffolding with frontmatter-style template metadata, zero configuration, language-agnostic output.
- [Plop.js](https://github.com/plopjs/plop) (JavaScript/Handlebars, npm): micro-generator framework, integrates as an npm script.
- [Copier](https://github.com/copier-org/copier) (Python): template renderer with update support, which is useful if the documentation team expects scaffolds to evolve and wants older generated pages to inherit template fixes.
- [Cookiecutter](https://cookiecutter.readthedocs.io/) (Python, BSD): mature template generator with a large ecosystem of reusable scaffolds and simple prompt-driven input collection.

#### Related ideas

Complements [Authoring-time validation hooks](#authoring-time-structural-validation-hooks): scaffolding ensures new pages start correct; hooks enforce the structure was not broken during editing. Complements [Engineer-to-docs input pipeline](#engineer-to-docs-structured-input-pipeline), where scaffolding provides the template and the input pipeline provides the content.

---

### Engineer-to-docs structured input pipeline
A system that converts structured engineer input into documentation drafts by having engineers answer three to five targeted questions about their code change and running those answers through a deterministic template engine plus an AI prose layer. The docs team reviews and edits AI-assembled output rather than authoring from scratch, shifting their work from blank-page prose writing to editing structured, on-format first drafts. The reframe: engineers aren't bad at writing docs; docs are in the wrong format for engineers. Structured data in, prose out.

#### The problem it solves

Engineers who ship code changes are expected to contribute documentation, but the format mismatch between engineering work and documentation output creates two failure modes. First, no documentation: the engineer ships the feature and the docs team discovers the gap reactively. Second, documentation that doesn't match standards: the engineer writes prose in their PR, and the docs team rewrites it entirely. Both failure modes are common. In a high-volume launch cycle where documentation consistently lags behind shipping, the cost of both failure modes is real.

#### Why AI is needed

AI transforms structured answers into polished prose that matches the docs team's voice, content framework, and component conventions. An engineer fills out structured questions ("What changed?" "What's the user-facing effect?" "What CLI flags or config keys changed?" "What's the release stage?" "Are there known limitations?") and a language model assembles the answers into a documentation update formatted as the appropriate content type with correct frontmatter, components, and prose that reads like a doc page rather than an engineer's PR description. Without AI, the structured input produces a bare template that a writer must still rewrite entirely; the labor savings evaporate.

#### How it might work

Two stages with an optional third.

**Stage 1**, structured input collection: engineers answer three to five questions at the point of highest context: when their PR is being reviewed or merged. Option A: a [GitHub PR template](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/creating-a-pull-request-template-for-your-repository) addition with a machine-readable comment block. Option B: [GitHub issue forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository) or a lightweight web form engineers access after their PR merges. Option A is lower friction; engineers fill it out in their PR flow. Option B is higher quality; engineers fill it out after the feature ships.

**Stage 2**, draft assembly: a webhook or scheduled job reads the structured input and routes it to an AI assembly step. The AI receives: the structured answers, the inferred content type, the existing docs page most likely to need updating, and the style guide. It produces a complete draft with correct frontmatter, appropriate components, and docs-quality prose. The draft is opened as a PR with the engineer as contributor.

**Stage 3** (optional), confidence-based routing: high-confidence, low-risk drafts auto-merge (e.g., adding a new CLI flag to the reference page); everything else routes to the docs team review queue.

Structured input questions:

1. "What changed for users?" (free text, becomes the lead paragraph)
1. "What content type does this need?" (select: how-to / reference update / feature guide / changelog)
1. "What CLI flags, API fields, or settings changed?" (structured: name, type, description)
1. "What's the release stage?" (select: alpha / beta / GA)
1. "What plan tier does this require?" (select: all / starter / premium / enterprise)

#### The value it will bring

Lowers the barrier for engineering contributions to five structured fields rather than a blank page. Produces higher-quality first drafts than unstructured prose. Shifts the docs team's work for engineer-originated changes from authoring (hours) to editing (minutes). [Promptless (YC W25)](https://promptless.ai/) reports that enterprise teams with full-time writers use their pipeline to assemble context across Jira, Confluence, and Slack, confirming the pattern works.

#### Research backing

[Promptless](https://promptless.ai/) (YC W25) is the direct commercial validation: it drafts documentation updates from GitHub PRs and supports custom style guides. The [Promptless HN launch thread](https://news.ycombinator.com/item?id=43092522) includes customer quotes confirming the pattern. [ClickHelp's 2026 documentation forecast](https://clickhelp.com/clickhelp-technical-writing-blog/documentation-2026-from-human-centric-to-ai-first/) identifies the shift from human-centric to AI-first documentation as a defining trend. Google's [2024 DORA report](https://cloud.google.com/devops/state-of-devops/) finding that AI-assisted workflows require automated validation reinforces the structured-input approach.

#### Recommended tools

- [Promptless](https://promptless.ai/) (commercial, YC W25): the closest existing solution. Evaluate for direct adoption before building custom.
- [GitHub issue forms](https://docs.github.com/en/communities/using-templates-to-encourage-useful-issues-and-pull-requests/configuring-issue-templates-for-your-repository) (GitHub-native): structured YAML-defined forms with required fields. Useful when the team wants machine-readable input without building a separate UI.
- [Probot](https://probot.github.io/) (Node.js, ISC): GitHub app framework for collecting structured PR metadata, opening follow-up issues, and creating draft docs PRs from repository events.
- [Hygen](https://github.com/jondot/hygen) (JavaScript/EJS, npm): deterministic template-to-output mapping layer once the structured answers have been collected.

#### Related ideas

Complements [Code-to-docs change detection](#code-to-docs-change-detection-pipeline), which detects that a code change requires a docs update; this idea provides the mechanism for engineers to supply the content. Complements [Documentation scaffolding CLI](#documentation-scaffolding-cli), which provides the template structure this pipeline fills with content.

---

## AI readability and discoverability

### Agent-friendly documentation validation
A two-stage validation pipeline that evaluates documentation sites against the [Agent-Friendly Documentation Spec](https://agentdocsspec.com/), scoring how well each site and page serves coding agents, RAG pipelines, and AI support systems. Stage 1 runs the spec's 22 automated checks via the [`afdocs`](https://afdocs.dev/) CLI covering content discoverability, markdown availability, page size, content structure, URL stability, observability, and authentication. Stage 2 adds LLM-based comprehension testing: an AI reads individual sections in isolation and evaluates whether it can extract accurate, complete answers without surrounding context. The pipeline runs as a scheduled CI job, producing a scored report with category breakdowns and actionable fix suggestions.

#### The problem it solves

Documentation sites are increasingly consumed by coding agents (Claude Code, Cursor, GitHub Copilot) rather than human readers, but most sites were not built for this access pattern. Agents hit truncation limits, get walls of CSS instead of content, cannot follow cross-host redirects, and do not know about discovery mechanisms like `llms.txt`. Documentation that relies on client-side rendering, tabbed UI components, or navigation structure to convey meaning fails when agents fetch it. These problems are invisible to site owners: agents silently work with partial information, fall back on training data, or recommend competing products whose docs they can parse. The [Agent-Friendly Documentation Spec](https://agentdocsspec.com/) codifies [22 checks across 7 categories](https://agentdocsspec.com/spec/) that evaluate how well a documentation site serves agent consumers, grounded in empirical observation of real agent workflows.

Beyond coding agents, documentation also feeds RAG pipelines and AI support systems. A section that begins "As mentioned above, the policy file controls access..." is comprehensible to a human scrolling the page but broken when retrieved in isolation by a RAG system. The [2025 Stack Overflow Developer Survey](https://survey.stackoverflow.co/2025/) found that 84% of developers now use or plan to use AI tools, yet 46% say they don't trust AI output accuracy.

#### Why AI is needed

The spec's 22 checks cover structural and infrastructure problems that deterministic and heuristic tools can detect: missing `llms.txt`, client-side rendering, page size violations, broken redirects, tabbed content serialization blowup, cache header issues. The [`afdocs`](https://afdocs.dev/) CLI automates all of these. But evaluating whether documentation *content* is comprehensible to an AI system requires testing it against an AI system. No deterministic check can assess whether an LLM reading a section in isolation would miss that the prerequisites are listed two sections above, whether heading content is specific enough for a RAG system to identify scope without surrounding context, whether code examples are self-contained, or whether cross-references provide enough semantic information to be useful when followed in isolation. The AI evaluation layer tests what structural checks cannot: content-level comprehensibility for non-human consumers.

#### How it might work


Two stages. **Stage 1** (deterministic, ships day 1): run [`afdocs`](https://afdocs.dev/) against your documentation site. This executes the spec's 22 checks across content discoverability (llms.txt existence, validity, size, link resolution, markdown links, page directives), markdown availability (URL support, content negotiation), page size and truncation risk (rendering strategy, markdown size, HTML size, content start position), content structure (tabbed content serialization, section header quality, code fence validity), URL stability (HTTP status codes, redirect behavior), observability (llms.txt freshness, markdown-HTML content parity, cache header hygiene), and authentication (auth gate detection, alternative access paths). The output is a scored report with category breakdowns, per-check pass/warn/fail results, and specific fix suggestions. Integrate into CI with the built-in vitest helper for regression detection.

```bash
npx afdocs check https://docs.example.com --format scorecard
```

**Stage 2** (AI, adds comprehension testing): for prioritized pages (highest-traffic, worst Stage 1 scores, or pages flagged by the spec's content structure checks), an LLM evaluates each section in isolation by answering a test question against just that section's text, then scoring whether the answer is coherent and complete. [RAGAS](https://docs.ragas.io/) provides faithfulness and context-relevance metrics for teams that also serve RAG pipelines. This layer catches content-level problems invisible to structural checks: implicit context dependencies, ambiguous headings, and sections that assume knowledge from elsewhere on the page.

For a focused sprint: run Stage 1 against your full site for an immediate scorecard with actionable fixes. Apply Stage 2 to the 20 highest-traffic pages. Output: per-site scorecard (Stage 1) plus per-page comprehension report (Stage 2) with concrete recommendations. Most Stage 1 fixes are configuration changes, not content rewrites: adding an `llms.txt`, enabling server-side rendering, or serving `.md` URLs can move a site from an F to a B in a single sprint.

#### The value it will bring

Stage 1 delivers immediate, quantified visibility into agent accessibility with zero AI cost. The spec's interaction effects analysis surfaces systemic problems beyond individual check failures: undiscoverable markdown (site serves `.md` but agents cannot find it), truncated indexes (`llms.txt` exceeds agent context limits), client-rendered pages (agents see empty shells), and authenticated docs without alternatives (agents fall back on training data). Stage 2 provides a concrete queue of content-level improvements with measurable impact on AI-mediated answer quality. Documentation that works better for AI consumers also works better for human readers; the fixes compound. The [Contextual AI document parser study](https://contextual.ai/blog/document-parser-for-rag) demonstrated that including document hierarchy metadata in chunks increased end-to-end RAG accuracy from 69.2% to 84.0%.

#### Research backing

The [Agent-Friendly Documentation Spec](https://agentdocsspec.com/) (v0.3.0, 2026) defines the 22 checks implemented by [`afdocs`](https://afdocs.dev/), grounded in findings from [Agent-Friendly Docs](https://dacharycarey.com/2026/02/18/agent-friendly-docs/) (observations from 10+ hours of validating 578 coding patterns with Claude, covering URL failure modes, `llms.txt` discovery, markdown benefits, and page truncation) and [Agent Web Fetch Spelunking](https://dacharycarey.com/2026/02/19/agent-web-fetch-spelunking/) (deep dive into how Claude Code's web fetch pipeline processes HTML and markdown, including the summarization model, truncation limits, and why inline CSS can make a 97-line HTML page invisible to agents). The spec's [Appendix A](https://agentdocsspec.com/spec/#appendix-a-known-platform-truncation-limits) documents known truncation limits across Claude Code, Cursor, GitHub Copilot, Gemini, and OpenAI. A [Vectara study at NAACL 2025](https://langcopilot.com/posts/2025-10-11-document-chunking-for-rag-practical-guide) found that chunking configuration had as much influence on retrieval quality as the choice of embedding model. [Mintlify's analysis on structuring docs for AI](https://www.mintlify.com/blog/structure-documentation-AI-human-readers) identifies the same structural patterns as essential for both human and AI audiences.

#### Recommended tools

- [Docling](https://github.com/docling-project/docling) (Python, MIT): structure-preserving document parser that keeps headings, tables, and layout metadata intact before chunking or evaluation.
- [Promptfoo](https://github.com/promptfoo/promptfoo) (TypeScript, MIT): useful for keeping a stable section-isolation eval suite in CI and comparing how different models score the same page slices.
- [LLMTEXT](https://parallel.ai/blog/LLMTEXT-for-llmstxt) (open source): check tool for validating site-level AI-readability standards.
- [`afdocs`](https://afdocs.dev/) ([npm](https://www.npmjs.com/package/afdocs), [GitHub](https://github.com/agent-ecosystem/afdocs)): reference implementation of the Agent-Friendly Documentation Spec. CLI tool and Node.js library running all 22 checks with scorecard output and CI integration via vitest helper.
- [Agent-Friendly Documentation Spec](https://agentdocsspec.com/) ([GitHub](https://github.com/agent-ecosystem/agent-docs-spec)): the open spec defining the 22 checks, pass/warn/fail criteria, interaction effects, and known platform truncation limits.
- [RAGAS](https://docs.ragas.io/) (Python, Apache 2.0): RAG evaluation framework with faithfulness, answer relevancy, context precision, and context recall metrics. Powers Stage 2 comprehension testing for teams that also serve RAG pipelines.
- [remark-lint-frontmatter-schema](https://github.com/JulianCataldo/remark-lint-frontmatter-schema): validates frontmatter against a JSON schema for metadata completeness. Complements the spec's content structure checks.

#### Related ideas

Shares infrastructure with [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle), which addresses AI discoverability at the site level while this validates overall agent accessibility. The spec's `llms.txt` checks (existence, validity, size, link resolution, freshness) overlap with that idea's llms.txt generation; use together for build-time generation plus runtime validation. Produces agent accessibility scores that could feed into [Content staleness detector](#content-staleness-detector) as an additional quality signal.

---

### Ghost reader report
An automated audit pipeline that queries major AI platforms (ChatGPT, Perplexity, Google AI Overviews) with a curated set of common product questions and compares the AI-generated responses against known-correct answers sourced from the docs. The system flags factual errors, outdated information, and missing context, outputting a ranked report giving the docs team its first-ever visibility into the AI-mediated reading experience. AI platforms now serve as an invisible intermediary between your documentation and a large share of readers; this report identifies where documented truth and AI-synthesized truth have diverged.

#### The problem it solves

The docs team has no visibility into what AI systems say about your product when users ask them questions. A 2024 Stanford HAI study found that 23% of brand-related queries to major LLMs contained at least one factual error; for brands with ambiguous names, that climbs to 41%. For products where configuration errors mean broken connectivity or misconfigured access control, AI misinformation generates support tickets and erodes user trust. Traditional monitoring (social listening, support ticket analysis) is blind to what AI says about your product in real time.

#### Why AI is needed

This is one of the few ideas where AI is both the subject being audited and a necessary component of the evaluation system. Comparing AI-generated responses against documentation requires language understanding: AI platforms don't produce structured outputs a regex can check. Evaluating whether a paragraph asserting feature support is factually consistent with current docs requires reading comprehension and semantic comparison. An LLM grader with access to the ground-truth docs can identify specific factual divergences and classify their severity (wrong claim, outdated reference, unsupported feature described as supported). Only the evaluation step requires AI; query dispatch and scheduling are deterministic.

#### How it might work

Three stages. **Stage 1** (deterministic): define a question bank of 25-40 common user questions. For each, pull the ground-truth answer from the relevant documentation page. **Stage 2** (AI, query execution): dispatch each question to ChatGPT (via OpenAI API), Perplexity (via Perplexity API), and optionally Google AI Overviews (via headless browser with [Playwright](https://github.com/microsoft/playwright)). Collect raw responses. **Stage 3** (AI, evaluation): pass each (question, ground-truth, AI-response) triple to a grader LLM with a structured prompt. [Promptfoo](https://github.com/promptfoo/promptfoo) is a good fit for the provider matrix and grading harness because it stores providers, prompts, and assertions in versioned config files. Output a structured JSON report, then aggregate into a ranked report sorted by severity and frequency.

For a focused sprint: 15 questions covering your highest-traffic topics, with ChatGPT as the sole audit target. Schedule as a weekly GitHub Action posting a Markdown report.

#### The value it will bring

First-ever visibility into how AI systems represent your product. Identifies specific documentation improvements that would correct AI-mediated misinformation. Establishes a repeatable audit baseline. The [Princeton GEO paper (KDD 2024)](https://arxiv.org/abs/2311.09735) found that optimized content receives 30-40% higher AI citation rates.

#### Research backing

The foundational [GEO paper from Princeton, Georgia Tech, and IIT Delhi](https://arxiv.org/abs/2311.09735) (KDD 2024) quantifies that optimized content receives 30-40% higher AI citation rates. [AI Labs Audit's 2026 analysis](https://ailabsaudit.com/blog/en/ai-hallucinations-brand-reputation-protect-image-chatgpt-claude-gemini) reports 35% of brands report reputation damage from inaccurate AI responses. A [Metrics Rule study on LLM brand hallucinations](https://www.metricsrule.com/research/brand-dilution-crisis-llm-hallucinations-seo-fix/) documented a real case where an AI platform told prospects a product cost $79/month when the actual price was $49. [Gartner predicts a 25% drop in traditional search volume by 2026](https://www.semrush.com/blog/llm-monitoring-tools/).

#### Recommended tools

- [Playwright](https://github.com/microsoft/playwright) (TypeScript/Python, Apache 2.0): headless browser queries to Google AI Overviews where no API exists.
- [Promptfoo](https://github.com/promptfoo/promptfoo) (TypeScript, MIT): provider-agnostic eval runner for storing the question bank, dispatching prompts, and grading responses against expected answers.
- [Otterly AI](https://otterly.ai/) (SaaS): commercial tool tracking brand share of voice across AI platforms. Useful for commercial validation of the approach.

#### Related ideas

Pairs with [Agent-friendly documentation validation](#agent-friendly-documentation-validation), which evaluates whether docs are structurally optimized for agents and AI systems; this tests what AI actually says. Complements [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle), which controls what AI crawlers access.

---

### MCP server for documentation
A read-only [Model Context Protocol (MCP)](https://modelcontextprotocol.io) server that exposes documentation as a queryable API for AI coding assistants. Built with an off-the-shelf MCP SDK, it indexes the rendered docs build output and provides search and retrieval tools so [Claude Code](https://claude.ai/code), [GitHub Copilot](https://github.com/features/copilot), and [Cursor](https://cursor.com/) can access authoritative, up-to-date documentation at inference time instead of relying on stale training data.

#### The problem it solves

AI coding assistants rely on training-time snapshots when answering product questions. When a developer asks how to configure access control, the answer may reflect outdated syntax or features that changed since training cutoff. No mechanism exists to provide AI assistants with current, authoritative documentation at query time. Competitors and adjacent platforms have made this a default feature: [GitBook automatically exposes an MCP server](https://gitbook.com/docs/developers/gitbook-api/api-reference/docs-sites/site-mcp-servers) for every published space, and [Mintlify auto-generates an MCP server](https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs) for every hosted docs site. Custom docs sites built on frameworks like Next.js, Hugo, or Docusaurus don't get MCP for free.

#### Why AI is needed

[MCP](https://modelcontextprotocol.io) is an AI-native protocol. The server exposes documentation as a queryable API for AI consumption; the tools it exposes (search, page retrieval) are designed to be called by LLMs during response generation, not by humans. The capability being unlocked is retrieval-augmented generation at the docs-site level: AI assistants query the live documentation index during generation rather than relying on memorized training data.

#### How it might work

A lightweight read-only MCP server built with the [TypeScript MCP SDK](https://github.com/modelcontextprotocol/typescript-sdk) or the [Python MCP SDK](https://github.com/modelcontextprotocol/python-sdk) exposing two tools:

- `search_docs`: full-text and semantic search across the rendered docs, returning snippets with titles, paths, and relevance scores
- `get_page`: retrieves the full Markdown content of a specific page by path

The server indexes content from your content directory at startup. The server configuration JSON can be published in the repo so any developer can add it to their Claude Code, Cursor, or Copilot setup. [FastMCP](https://gofastmcp.com/) is useful if you want a production-ready Python wrapper around the protocol, while documentation-specific projects such as [mcpdoc](https://github.com/langchain-ai/mcpdoc) show the value of exposing `llms.txt` and docs retrieval behind a standard MCP interface.

A focused sprint scope: build the server against a single product area (e.g., 6-10 pages) as a proof of concept. For deployment beyond the sprint, the server could be hosted internally or the MCP config and indexed JSON bundle could be generated at docs build time and shipped alongside the static site, requiring no server infrastructure.

#### The value it will bring

Every AI coding assistant with MCP support gets access to current documentation. Developers receive answers grounded in current docs rather than training-time snapshots. Reduces incorrect AI-generated configuration advice. The [MCP SDK has 97 million monthly downloads](https://www.mintlify.com/blog/what-is-mcp-and-how-to-get-started); the ecosystem is at critical mass.

#### Research backing

[Mintlify's analysis of MCP vs. web search](https://www.mintlify.com/blog/generate-mcp-servers-for-your-docs) notes that MCP searches current indexed documentation directly while web search depends on what search engines have indexed. [GitBook's LLM-ready docs announcement](https://gitbook.com/docs/publishing-documentation/llm-ready-docs) confirms that MCP, `llms.txt`, and `llms-full.txt` generation are now table stakes for developer documentation platforms.

#### Recommended tools

- [modelcontextprotocol/typescript-sdk](https://github.com/modelcontextprotocol/typescript-sdk) (TypeScript): official MCP SDK for building servers. Natural fit for Next.js-based docs sites.
- [modelcontextprotocol/python-sdk](https://github.com/modelcontextprotocol/python-sdk) (Python): official MCP SDK, alternative for non-Node stacks.
- [FastMCP](https://gofastmcp.com/) (Python, Apache 2.0): production-oriented framework for building MCP servers with less boilerplate than the raw SDK.
- [mcpdoc](https://github.com/langchain-ai/mcpdoc) (Python): documentation-focused MCP server that exposes `llms.txt` sources to IDEs and coding assistants. Good reference if you want a docs-first server rather than a generic repository integration.

#### Related ideas

Complements [Agent-friendly documentation validation](#agent-friendly-documentation-validation), which validates doc structure and content for agent and AI consumption; this provides the transport layer for AI to query that documentation. Complements [llms.txt and structured data bundle](#llmstxt-and-structured-data-bundle): llms.txt is a passive index for AI crawlers; the MCP server is an active API for AI systems that query at inference time.

---

### llms.txt and structured data bundle
A build-time addition to the docs pipeline that generates an [llms.txt](https://llmstxt.org/) file indexing the most important documentation pages for AI retrieval systems, adds [JSON-LD structured data](https://developers.google.com/search/docs/appearance/structured-data/intro-structured-data) (HowTo, FAQPage, Article schemas from [Schema.org](https://schema.org/)) to each docs page for search engine rich results and AI grounding, and includes a CI check enforcing required frontmatter fields that feed the structured data generation. The entire bundle ships as a single build pipeline addition.

#### The problem it solves

AI retrieval systems and search engines lack a machine-readable index of your documentation. There is no `/llms.txt` file listing the most important pages for LLMs. There is no structured data marking up how-to guides as [HowTo schemas](https://schema.org/HowTo), FAQs as [FAQPage schemas](https://developers.google.com/search/docs/appearance/structured-data/faqpage), or feature guides as Article schemas. Without this markup, docs are less likely to appear in Google AI Overviews and LLM-generated answers. Over [844,000 websites have implemented llms.txt](https://www.bluehost.com/blog/what-is-llms-txt/), including Anthropic, Cloudflare, and Stripe. [Princeton GEO research found content with clear structural signals saw up to 40% higher visibility in AI-generated responses](https://www.benallymarketing.com/post/the-comparative-effectiveness-of-llm-txt-and-json-ld-in-large-language-model-optimization).

#### Why AI is needed

AI selects and ranks the top pages for `llms.txt` inclusion based on traffic signals, topical coverage analysis, and information density assessment. A script can generate `llms.txt` syntax mechanically, but deciding which pages best represent authoritative entry points for AI systems requires understanding which pages are conceptually foundational vs. peripheral. AI also classifies each topic to determine the appropriate [Schema.org type](https://schema.org/): a page with a numbered procedure is a HowTo, a page with question-answer pairs is a FAQPage, a feature overview is an Article. The build scripts that generate the output from frontmatter are deterministic; the initial classification requires AI judgment.

#### How it might work

Three independently shippable components.

**Component 1**, `llms.txt` generation: AI produces a ranked list of top pages with one-line descriptions, following the [llms.txt specification](https://llmstxt.org/). A build script generates the output as a Markdown file hosted at `/llms.txt`. If the site already emits static HTML, [llms-txt-action](https://github.com/demodrive-ai/llms-txt-action) can crawl the built output and generate `llms.txt` and `llms-full.txt` with minimal custom code.

**Component 2**, JSON-LD structured data: a build-time script reads each doc file's frontmatter (content type, title, summary, freshness date) and generates the appropriate Schema.org type. Pages with how-to content get HowTo schema with steps extracted from numbered headings. FAQ pages get FAQPage schema. All pages get Article schema as a baseline. The JSON-LD is injected into each page's `<head>` at build time.

**Component 3**, CI frontmatter enforcement: a CI step validates that required frontmatter fields are present on every doc file. This is a blocker for Component 2: JSON-LD generation requires accurate frontmatter. Simpler than pre-commit hooks; a single Python script that fails CI if required fields are absent.

For a focused sprint, Components 1 and 3 could ship in a single day. Component 2 adds a day of build pipeline work.

#### The value it will bring

Immediate, permanent improvement in how AI systems consume and represent your documentation. Pages with valid structured data are 2.3x more likely to appear in Google AI Overviews. A [combined llms.txt + JSON-LD approach began producing measurable results within 24 hours of indexing](https://www.dev5310.com/en/lab/llms-txt-is-powering-ai-answers) in documented cases.

#### Research backing

The [llms.txt specification](https://llmstxt.org/) was created by Jeremy Howard at Answer.AI and has 844,000+ implementations. [Mintlify's llms.txt analysis](https://www.mintlify.com/blog/what-is-llms-txt) confirms that Anthropic requested llms.txt for their documentation. The [comparative effectiveness analysis of llms.txt vs. JSON-LD](https://www.benallymarketing.com/post/the-comparative-effectiveness-of-llm-txt-and-json-ld-in-large-language-model-optimization) finds a combined approach outperforms either alone. Google's [FAQPage structured data documentation](https://developers.google.com/search/docs/appearance/structured-data/faqpage) confirms FAQ schema produces rich search results and feeds AI Overviews.

#### Recommended tools

- [llms-txt-action](https://github.com/demodrive-ai/llms-txt-action) (GitHub Action): generates `llms.txt` and `llms-full.txt` from a built docs site, which shortens the path from proof of concept to a scheduled production job.
- [schema-dts](https://npm.io/package/schema-dts) (TypeScript, Apache 2.0): typed Schema.org definitions for generating valid JSON-LD at build time without hand-assembling raw objects.
- [Schema.org HowTo](https://schema.org/HowTo) and [FAQPage](https://schema.org/FAQPage): authoritative vocabulary definitions for the structured data layer.
- [Next.js Metadata API](https://nextjs.org/docs/app/building-your-application/optimizing/metadata): `generateMetadata` for injecting JSON-LD at build time on Next.js sites.
- [Google Rich Results Test](https://search.google.com/test/rich-results) (web tool): validates the generated JSON-LD before rollout so the build step does not emit markup that looks correct but fails eligibility checks.

#### Related ideas

Part of the AI readability cluster with [Agent-friendly documentation validation](#agent-friendly-documentation-validation), [Ghost reader report](#ghost-reader-report), and [MCP server for documentation](#mcp-server-for-documentation). llms.txt provides a passive index; the MCP server provides an active API. Both are needed; different AI systems use different consumption patterns.

---

## Contributing

Have an idea that belongs here? Contributions are welcome. See [CONTRIBUTING.md](CONTRIBUTING.md) for the idea template, quality criteria, and submission process.
