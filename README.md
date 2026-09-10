# Verifiable Trust Infrastructure (VTI) Specification

**📖 Read the specification: <https://trustoverip.github.io/dtgwg-vti-spec/>**

This repository holds the ToIP **Verifiable Trust Infrastructure**
specification: the infrastructure layer at which Decentralized Trust Graph
(DTG) components are composed into an operating trust system.

The specification covers three bodies of material:

- **the common model** every node shares — trust contexts and the authority
  model built over them, identity and keys, transports and delivery, sessions,
  the client lifecycle, the operation surface, and credential handling;
- **the nodes** — the Verifiable Trust Agent (VTA), the Verifiable Trust
  Community (VTC) and the supporting hosts — including which node owns which
  authority decision;
- **the composition requirements** — properties that must hold across a
  complete interaction and that no single component specification can
  establish.

It does not define identifier methods, credential data models, Trust Task
definitions, transports, or governance frameworks. It references those and
states how they compose.

## Reading the specification

The rendered specification is published at
<https://trustoverip.github.io/dtgwg-vti-spec/> and is rebuilt automatically on
every push to `main` by the *Render and Deploy* workflow, which renders to the
`gh-pages` branch. `docs/` is build output and is never committed to `main`.

The source lives in `spec/`, one file per chapter, rendered in the order given
by `markdown_paths` in `specs.json`.

## Contributing

- [`STRUCTURE.md`](STRUCTURE.md) — the chapter map, the requirement-identifier
  scheme, the ownership tags, the decision record, and the drafting sequence.
  **Read this before proposing a new chapter or moving material between
  chapters.**
- Editorial instructions are enclosed in `{{double curly braces}}` and must be
  removed or replaced before publication. In the current revision most chapter
  bodies are still instructions: they fix the scope and section structure of
  each chapter, and normative text is drafted chapter by chapter.
- Terminology is managed with
  [Spec-Up-T](https://trustoverip.github.io/spec-up-t-website/); a term already
  defined in a referenced glossary is transcluded rather than redefined.

## Building locally

```bash
npm install
npm run render
```
