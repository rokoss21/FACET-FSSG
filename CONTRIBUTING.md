# Contributing to FSSG

Thank you for your interest in improving **FSSG (FACET Static Site Generator)**. This project is part of the broader [FACET ecosystem](https://github.com/rokoss21) and follows the same principles of determinism, safety, and rigorous engineering. The guidelines below describe how to report issues, propose enhancements, and submit high-quality pull requests.

## Table of Contents
1. [Code of Conduct](#code-of-conduct)
2. [Project Philosophy](#project-philosophy)
3. [Before You Start](#before-you-start)
4. [Development Workflow](#development-workflow)
5. [Testing](#testing)
6. [Coding Guidelines](#coding-guidelines)
7. [Commit & PR Standards](#commit--pr-standards)
8. [Release Process](#release-process)
9. [Need Help?](#need-help)

---

## Code of Conduct
By participating in this project you agree to uphold the standards found in the [Contributor Covenant](https://www.contributor-covenant.org/). Always be respectful, constructive, and collaborative.

---

## Project Philosophy
- **Determinism is non‑negotiable.** Same inputs must yield identical outputs. Avoid adding features that introduce randomness or rely on mutable global state.
- **Contracts over heuristics.** Align contributions with the FACET specification and error catalogue. Every new behaviour should be observable and verifiable.
- **Auditability by design.** Logs, diagnostics, and documentation should help operators understand what happened and why.
- **Minimal surface area.** FSSG focuses on publishing; executing workflows belongs in the FACET MCP runtime and orchestration layers.

---

## Before You Start
1. **Understand the stack.** Skim the documentation for:
   - [FACET Language](https://github.com/rokoss21/FACET)
   - [FACET Agents](https://github.com/rokoss21/FACET-AGENTS)
   - [FACET MCP Server](https://github.com/rokoss21/FACET_mcp)
   - [RMCP Protocol](https://github.com/rokoss21/rmcp-protocol)
2. **Create an issue first.** For new features or significant refactors, open an issue to discuss scope and approach.
3. **Fork or create a feature branch.** Keep changes isolated and rebased on top of the latest `main` branch.

---

## Development Workflow
1. **Clone and set up environment**
   ```bash
   git clone https://github.com/rokoss21/FACET-FSSG.git
   cd FACET-FSSG
   python3 -m venv .venv
   source .venv/bin/activate
   pip install -e fssg
   ```
2. **Optional: install the FACET reference parser** for full test coverage.
   ```bash
   pip install -e FACET/facet-lang  # local checkout only; ignored by Git
   ```
3. **Make changes** in a dedicated branch (`feature/my-improvement`). Keep commits focused and logically separated.
4. **Run formatting & static checks** (add tools as we adopt them). For now ensure two-space indentation in FACET files and conventional linting for Python/TypeScript code.

---

## Testing
- Unit tests live under `FACET/facet-lang/tests` and `fssg/tests` (if present). Add coverage alongside new features or bug fixes.
- Run the relevant suites before opening a PR:
  ```bash
  PYTHONPATH=FACET/facet-lang pytest FACET/facet-lang/tests
  pytest fssg/tests  # if applicable
  ```
- For renderer changes, build the sample site and inspect `dist/`:
  ```bash
  fssg build -c fssg.config.json
  ```
- Determinism checks: rebuild twice; outputs should be identical (byte-for-byte).

---

## Coding Guidelines
- **FACET artifacts**: Respect spec rules—two-space indentation, no tabs, no attribute interpolation (`F304`).
- **Python code**: Follow PEP 8 and add docstrings for modules, classes, and complex functions. Prefer type hints.
- **Error handling**: Reuse existing error codes (`F###`, `H###`, etc.) or propose new ones in the issue/PR description.
- **Documentation**: Update `README.md`, `FSSG.md`, or inline comments when behaviour changes or new features are added.

---

## Commit & PR Standards
- Use clear, imperative commit messages (e.g., `Add deterministic anchor validation`).
- Keep PRs focused. Large changes should be split into logical pieces.
- Include:
  - Summary of changes
  - Reason/motivation
  - Testing performed (commands + results)
  - Any follow-up work required
- PR titles should follow Conventional Commits when possible (`feat:`, `fix:`, `docs:`, etc.).

---

## Release Process
1. Ensure `main` is green (tests passing, documentation updated).
2. Update version strings (`fssg/__init__.py`, `pyproject.toml`, etc.).
3. Create a changelog entry (even if brief) in the release notes or documentation.
4. Tag the release (`git tag vX.Y.Z`) and push tags.
5. Publish updated packages to PyPI/npm as required (see project-specific instructions).

---

## Need Help?
- Open a [GitHub issue](https://github.com/rokoss21/FACET-FSSG/issues).
- Start a discussion in the relevant FACET ecosystem repository.
- Reach out directly via the maintainer profile: [@rokoss21](https://github.com/rokoss21).

Thank you for helping keep the FACET ecosystem deterministic, auditable, and delightful to work with. 🚀
