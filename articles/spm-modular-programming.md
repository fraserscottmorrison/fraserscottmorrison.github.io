---
title: Swift Package Manager: Modular Programming
permalink: /SPM-Modular-Programming/

---

<p align="center">
<img width="200" height="200" alt="SPM Modular Programming"
src="https://github.com/user-attachments/assets/ab698fd0-b1bb-45b3-8903-80160e096d7b" />
</p>

# Swift Package Manager: Modular Programming

Let me frame this article with a couple of not-so-bold predictions for software development.
First, agentic coding will be responsible for the majority of code written. This is already achievable today with an optimised development harness and spec-driven development.

As a consequence, I expect feature development to accelerate dramatically resulting in codebases that continue to grow in both size and complexity. This is a particular concern for enterprise applications, which are already operating at significant scale.

To prepare for this new era of code growth, architecture must prioritise **modularity, isolation, and clear dependency boundaries**.

This iOS architecture demonstrates a scalable approach that uses **Swift Package Manager (SPM)** as the foundation for modular application design.

---

# Core Concept

At the heart of this architecture is a simple idea:

> The Xcode project should be almost empty — everything lives in Swift Packages.

### Key principles:

- The Xcode project is intentionally **extremely thin**; ideally only 10-15 lines of code
- All features, shared logic, and UI components are implemented as **Swift Packages**
- Packages are fully self-contained:
  - Unit tests
  - Mocks
  - Even UI tests (where applicable)
- Dependency management is handled entirely through `Package.swift`

---

## Dependency Management Rule

Avoid using:

> Xcode → “Add Package Dependencies” UI

Why?

- It mutates the `.xcodeproj`
- Reduces source control clarity
- Increases the likelihood of merge conflicts

Instead:

- Declare dependencies explicitly in each `Package.swift`
- Pin to exact versions of remote packages

This ensures:
- Reproducible builds
- Transparent dependency graphs
- CI consistency

---

# Project Structure

App.swift only contains enough code to hand off the the first feature package which handles the entry/launch functionality before routing to the next package

```text
Xcode Project
├── App
│   └── App.swift
│
├── Packages
│   ├── Feature Packages
│   └── ...
│
├── Package Dependencies
│   ├── Common Packages (local + remote)
│   ├── ...
│   ├── 3rd Party Packages
│   └── ...
│
├── AGENTS.md
└── Harness/
```

---

# Feature Package Structure

Each feature is fully self-contained:

```text
Package/
├── Package.swift
├── Sources
│   ├── Concurrent/
│   │   ├── Models/ (Sendable)
│   │   └── Service.swift (actor)
│   │
│   └── Main/ (UI layer)
│       ├── Views/
│       ├── PackageCoordinator.swift
│       └── PackageRoute.swift
│
└── Tests/
```

---

## Concurrency Model

Swift Structured Concurrency can be difficult to adopt correctly, but when used well it provides significant benefits in predictability, correctness, and stability.
Each feature package is split into two logical layers:

- `Main` → `@MainActor` UI layer
- `Concurrent` → isolated concurrency layer

This provides a clear separation between:

- UI logic
- Asynchronous / concurrent operations

Example responsibilities:

- `Main`:
  - Views
  - Coordinators
  - Routing logic

- `Concurrent`:
  - Models (`Sendable`)
  - Services (`actor`)
  - Other actors

---

# Benefits

- Scalability through clear module boundaries  
- Parallel development across agent-assisted teams
- Faster CI by testing only affected packages  
- Single `.xcodeproj` (no `.xcworkspace`)  
- Agent-friendly, deterministic architecture  

---

# Reference Project

Explore the setup of the thin project, feature packages and the inter-package navigation with this reference project. 

Included is a harness to allow agents to contribute features while adhering to the project's architectral patterns.

<a href="https://github.com/fraserscottmorrison/SPM-Modular-Programming">github.com/fraserscottmorrison/SPM-Modular-Programming</a>

Google provided an excellent reference Android project for a similarly modular architecture utilising gradle

<a href="https://github.com/android/nowinandroid">github.com/android/nowinandroid</a>
