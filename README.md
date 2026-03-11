# Human-Centered Skills

A collection of skills for AI agents that analyze software through a **human-centered systems lens**.

Modern AI tools are increasingly capable of generating code, reviewing code, and building software systems. But one important dimension of software design is still frequently overlooked:

**humans are diverse.**

Languages, writing systems, naming conventions, cultural expectations, accessibility needs, and identity structures vary widely across the world. Many software failures are not caused by logic errors, but by **assumptions about users** that were never questioned.

This repository contains structured **skills for AI agents** that help identify and reason about those assumptions.

The focus is especially on:

- localization and internationalization
- global-ready software design
- Unicode-safe text processing
- human-centered UX constraints
- cross-cultural data modeling

---

## Why This Exists

Localization problems rarely start with translation.

They usually begin much earlier, during system design.

Examples include:

- database schemas that assume Western naming structures
- UI layouts that break when text expands
- validation rules that reject non-ASCII characters
- workflows that assume a single language or region

These decisions often appear reasonable during development but can create significant friction for global users.

The goal of this repository is to help AI systems **surface those assumptions early**, so developers can design more inclusive and resilient software systems.

---

## Skill Library

Skills are located in the `/skills` directory.

Current and planned skills include:

- Detect Localization Assumptions
- RTL-Safe UI Design
- Unicode-Safe Text Processing
- Text Expansion-Aware Layouts
- Internationalization Data Models
- Global User Identity Models

Each skill focuses on a specific class of human-centered design challenges.

---

## Repository Structure

```
human-centered-skills/
   skills/
   Individual skill definitions for AI agents

   examples/
   Example analyses using these skills

   docs/
   Background concepts and philosophy
```

---

## Intended Use

These skills are designed to support AI systems that analyze:

- product specifications
- user interface designs
- backend schemas
- APIs
- validation rules
- application logic

The goal is **not translation**, but rather detecting and explaining human-centered design risks in software systems.

---

## Philosophy

Localization is often treated as a translation problem.

In practice, it is frequently a **systems design problem**.

Software systems embed assumptions about people. Localization simply exposes those assumptions faster than most other disciplines.

More background is available in:

`docs/philosophy.md`

---

## Contributing

Contributions and suggestions are welcome.

Possible areas for contributions include:

- new human-centered analysis skills
- examples of real-world localization failures
- improvements to existing skills
- documentation and educational material

---

## Maintainer

Maintained by **Moriel Schottlender**

Principal Engineer and systems architect with a focus on **human-centered localization and global software design**.

Website: https://moriel.tech