# Contributing

This project collects AI project ideas for documentation teams. If you have an idea that fits, add it.

## How to contribute

1. Fork this repository.
2. Create a branch from `main`.
3. Add your idea to `README.md` in the relevant theme section (see [Theme categories](#theme-categories)). Place it at the end of the section, before the `---` separator.
4. Add a row to the [table of contents](#updating-the-table-of-contents) in the same file.
5. Open a pull request.

## Idea template

Copy the template below and fill in every section. Each idea lives under its theme's `##` heading in `README.md` as a `###`-level entry.

```markdown
### Your idea title

[1-2 paragraphs: what this tool or system does, how it works at a high level, and what makes it distinct from existing approaches. Be specific about the architecture. Name the techniques (embeddings, AST parsing, LLM classification) rather than describing them generically.]

#### The problem it solves

[What pain point does this address? Ground it in real workflows. Describe what happens today without this tool and why that's costly. Avoid hypotheticals; describe problems that documentation teams actually encounter.]

#### Why AI is needed

[Why can't this be solved with deterministic tooling alone? Identify the specific AI capability required (semantic understanding, generation, classification, embedding similarity) and explain what it enables that rules, regex, or scripts cannot.]

#### How it might work

[Describe the architecture. Break it into layers, stages, or levels. Label each as deterministic, AI, or both. Name specific techniques and where they fit. Include a suggested scope for a focused sprint or proof of concept.]

#### The value it will bring

[What changes for the team? Be specific: time saved, risk reduced, coverage gained. Quantify where possible. Avoid vague claims like "improves efficiency."]

#### Research backing

[Cite sources that validate the approach: blog posts, academic papers, production case studies, tool documentation. Link to each source. Describe what each source contributes to the argument (not just that it exists).]

#### Recommended tools

[List open-source tools, libraries, or commercial products relevant to implementation. For each tool, include:]

- [Tool name](URL) (language, license): one-sentence description of what it does and why it's relevant to this idea.

#### Related ideas

[Which other ideas in this collection complement or depend on this one? Use anchor links: `[Idea name](#idea-anchor)`. Explain the relationship, not just the link.]
```

## Field-by-field guidance

**Title.** Use a descriptive noun phrase. The title should tell a reader what the tool *does*, not what problem it addresses. Compare: "Content staleness detector" (what it does) vs. "Solving stale documentation" (what problem it addresses).

**Intro paragraphs.** Lead with the mechanism, not the motivation. The first sentence should describe the system, not the problem. Name specific technologies. See the [Code-to-docs change detection pipeline](#code-to-docs-change-detection-pipeline) intro for an example that names AST-anchored detection, semantic analysis, and webhook events in the first paragraph.

**The problem it solves.** Describe real workflows, not abstract pain. "Writers discover drift days or weeks after a merge, often through customer complaints" is specific. "Documentation becomes outdated" is not.

**Why AI is needed.** This section justifies the AI component. Every idea in this collection has a deterministic layer; the AI layer handles what rules cannot. Explain the boundary. If your idea could be fully solved with grep, a linter, or a script, it does not need AI and does not belong here.

**How it might work.** Describe architecture, not a tutorial. Label layers as deterministic, AI, or both. Include a "focused sprint scope" at the end suggesting a minimal viable starting point. The implementation sketch should help a reader evaluate feasibility, not follow step-by-step instructions.

**The value it will bring.** Quantify where you can: hours saved per week, risk reduction, coverage percentages. If you cannot quantify, describe the before and after concretely.

**Research backing.** Cite at least two sources. For each, describe what it contributes to the argument. "The 2025 DORA report found that 64% of software professionals use AI for documentation" is useful. A bare URL is not.

**Recommended tools.** List 2-4 tools. Include the language, license, and a one-sentence description for each. Prefer open-source tools with active repositories. Commercial tools are fine when they validate the architecture or fill a gap.

**Related ideas.** Link to at least one other idea in the collection using anchor links. Explain the relationship: what data, infrastructure, or output they share.

## Theme categories

Add your idea to the theme section that best fits. The current themes are:

1. **Change detection and drift prevention** -- tools that detect when documentation falls out of sync with code, design docs, or product behavior.
2. **Quality, consistency, and coverage** -- tools that find inconsistencies, duplicates, terminology drift, and coverage gaps across the doc set.
3. **Review and workflow automation** -- tools that reduce review burden through triage, auto-merge of safe changes, and pre-commit validation.
4. **Testing and validation** -- tools that test documentation quality and accuracy using LLM-based comprehension checks, persona simulations, and schema validation.
5. **Content generation and authoring** -- tools that generate drafts, scaffolding, alt text, or structured input to accelerate the writing process.
6. **AI readability and discoverability** -- tools that make documentation more consumable by AI systems, RAG pipelines, and coding assistants.

If your idea does not fit an existing theme, propose a new one in your pull request and explain why the existing themes do not cover it.

## Updating the table of contents

The table of contents is a Markdown table near the top of `README.md`. Add a new row in the position that matches where your idea appears in the document.

Each row has four columns:

```markdown
| [Your idea title](#your-idea-title) | Theme name | Short description (one sentence) | [Related idea 1](#anchor), [Related idea 2](#anchor) |
```

- **Project**: the idea title as an anchor link. The anchor is the heading text in lowercase with spaces replaced by hyphens.
- **Theme**: use the short theme name from the existing rows (e.g., "Change detection", "Quality and coverage", "Review and workflow"). Match the convention in the table, not the full `##` heading text.
- **Description**: one sentence summarizing what the tool does.
- **Related projects**: anchor links to related ideas, matching the "Related ideas" section of your entry.

## Quality bar

Every idea in this collection meets these standards:

- **Specific problem framing.** The problem is grounded in real documentation workflows, not abstract concerns.
- **Clear AI justification.** The idea explains what AI enables that deterministic tooling cannot.
- **Architectural sketch.** The implementation section describes layers, components, and techniques, not vague aspirations.
- **Research backing.** At least two cited sources that validate the approach or components.
- **Named tools.** At least two recommended tools with links, languages, and licenses.
- **Related ideas.** At least one connection to another idea in the collection.

If your idea does not meet these standards yet, submit it anyway and note what is missing. Feedback during review can help strengthen it.

## Notes

- **Google Docs exports.** If you draft your idea in Google Docs and export to Markdown, strips smart quotes, non-breaking spaces, zero-width characters, and heading attribute syntax that Google Docs exports introduce.
- **Anchor links.** GitHub auto-generates heading anchors from heading text: lowercase, spaces become hyphens, special characters are removed. You do not need to add manual anchor IDs.
- **License.** Contributions are licensed under the [MIT License](LICENSE).
