# Micro-Frontend Canvas

A practical, one-page tool for defining and validating micro-frontend boundaries before implementation begins, or for diagnosing existing architectures that need restructuring.

[![License: CC BY-NC 4.0](https://img.shields.io/badge/License-CC%20BY--NC%204.0-lightgrey.svg)](https://creativecommons.org/licenses/by-nc/4.0/)

---

## What Is This?

The Micro-Frontend Canvas is a structured A4 document that captures the key decisions for a single micro-frontend: business purpose, boundaries, dependencies, communication contracts, governance, quality guardrails, and risks.

It works in two modes:

**Planning Mode**: Use it before you write code. The canvas forces your team to answer critical questions about boundaries, ownership, and constraints upfront, preventing costly rework later.

**Assessment Mode**: Use it to audit existing micro-frontends. Fill the canvas based on reality (not intention) to surface where boundaries are leaking, where ownership is unclear, and where standards aren't being enforced.

The fundamental principle: **one micro-frontend, one canvas.**

---

## What's in This Repo

```
├── micro-frontend-canvas.pdf        # Full guide with examples and blank template
├── MFE_CANVAS.md                    # Markdown template to embed in your MFE repo
└── examples/
    ├── shopnow-checkout.md          # E-commerce checkout flow (from the PDF guide)
    ├── learnhub-learning.md         # E-learning video player and lesson notes
    └── ...                          # Community-contributed examples
```

See the examples for complete filled canvases:
- [examples/shopnow-checkout.md](examples/shopnow-checkout.md) — E-commerce checkout flow (vertical split, payment processing, PCI compliance)
- [examples/learnhub-learning.md](examples/learnhub-learning.md) — E-learning platform (video player + lesson notes kept together, accessibility requirements, offline capability)

---

## Getting Started

### Option 1: Workshop (Recommended for New MFEs)

1. Run a boundary identification workshop with your team (event storming, domain storytelling) with 15-20 participants maximum
2. Once boundaries are established, print the blank canvas from the PDF
3. Work through each section as a team in a half-day session
4. Store the completed canvas in your micro-frontend's repository

### Option 2: Assessment (For Existing MFEs)

1. Copy `MFE_CANVAS.md` into your micro-frontend's repository
2. Fill each section based on what's actually happening today, not what you planned
3. Identify gaps: which sections are hard to answer? That's where your problems are
4. Use the gaps to prioritise what needs fixing

### Option 3: Async (Lightweight Approach)

1. Have your tech lead or architect fill the canvas to 90%
2. Bring the team together for a focused discussion on each section
3. Align on the remaining 10% collaboratively
4. Store the result in your repository alongside your README

---

## Shared Baselines

If your micro-frontends share identical technical constraints, dependencies, or patterns, create a shared baseline document and reference it from individual canvases rather than repeating the same information. Each canvas should focus on what's unique: its specific business capability, boundaries, risks, and communication contracts.

---

## Canvas and README

The canvas and your README serve different purposes:

- **Canvas**: Architectural decisions and boundary validation. Why this MFE exists, what it owns, how it communicates, what constraints apply. Changes infrequently.
- **README**: Living implementation details. Setup, migrations, conventions, environment config. Changes frequently.

The canvas answers "what are we building and why?" while the README answers "how do I work on this today?" Store both in your repository.

---

## Using the Canvas with AI Code Assistants

The canvas is designed to work as a context artifact for AI-assisted development. A completed canvas provides structured, explicit context that dramatically improves code generation quality.

Instead of prompting a code assistant with vague instructions like "build a checkout flow," a canvas gives it everything it needs: business purpose, inputs, outputs, contracts, constraints, dependencies, quality targets, and known risks.

### Pairing with MFE Skills

For even better results, combine the canvas with [mfe-skills](https://github.com/lucamezzalira/mfe-skills), a set of AI code assistant skills specifically designed for micro-frontend architecture governance.

**The canvas defines *what* you're building. MFE Skills ensure *how* you build it follows micro-frontend best practices.**

Together they provide:
- **Canvas**: Business context, boundaries, contracts, constraints, quality guardrails
- **MFE Skills**: Architecture patterns, governance rules, code-level enforcement

This combination works across Claude Code, Cursor, Windsurf, and GitHub Copilot.

### Workflow

```
1. Complete the canvas (planning or assessment mode)
2. Store MFE_CANVAS.md in your repository
3. Install mfe-skills in your project
4. Your code assistant now has both architectural context and governance rules
5. Generate specs, scaffold implementations, validate boundaries
```

---

## How Much Detail?

Each section should contain enough information to make the decision clear, but not so much that the canvas becomes a specification document. A good rule: if you can't summarise a section in 3-5 bullet points, your micro-frontend's scope may be too broad, or you're capturing implementation details that belong in your README.

The canvas should be scannable in under 5 minutes. Resist the temptation to shrink fonts and cram more in. If a section feels too small, that's a signal your boundary needs rethinking.

---

## Community Showcase

We encourage teams to share their completed canvases (anonymised if needed) so others can learn from real-world examples across different industries and scales.

To contribute an example:
1. Fork this repo
2. Add your canvas to `examples/` (use `MFE_CANVAS.md` as template)
3. Open a PR with a brief description of your context (industry, team size, MFE count)

---

## Contributing

Your feedback makes the canvas better for everyone.

- **Found a problem?** Open an issue
- **Have a suggestion?** Open an issue or PR
- **Used it with your team?** Share your experience in Discussions
- **Adapted it for your context?** We'd love to see how

---

## License

This work is licensed under [Creative Commons Attribution-NonCommercial 4.0 International (CC BY-NC 4.0)](https://creativecommons.org/licenses/by-nc/4.0/).

You are free to use, share, and adapt this material for non-commercial purposes, provided appropriate credit is given.

**Attribution**: © Luca Mezzalira. Originally published as part of the Micro-Frontend Canvas.

---

## Resources

- [Building Micro-Frontends (O'Reilly)](https://buildingmicrofrontends.com) - Comprehensive guide to micro-frontend architecture
- [MFE Skills](https://github.com/lucamezzalira/mfe-skills) - AI code assistant skills for micro-frontend governance
- [buildingmicrofrontends.com](https://buildingmicrofrontends.com) - Newsletter, articles, case studies, and community resources

---

## Author

**Luca Mezzalira** - Solutions architect specialising in frontend architectures, distributed systems, and cloud infrastructure. Author of Building Micro-Frontends (O'Reilly). Over 20 years in the industry and a decade helping teams worldwide adopting micro-frontends architecture.

- [LinkedIn](https://linkedin.com/in/lucamezzalira)
- [buildingmicrofrontends.com](https://buildingmicrofrontends.com) — newsletter, articles, and resources

---

## Support

If you find the Micro-Frontend Canvas useful, you can [buy me a coffee](https://www.buymeacoffee.com/mezzalab).

<a href="https://www.buymeacoffee.com/mezzalab"><img src="https://cdn.buymeacoffee.com/buttons/v2/default-yellow.png" alt="Buy me a coffee" width="200"></a>
