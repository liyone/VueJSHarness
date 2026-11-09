---
name: onboarding
description: Onboard a developer to an existing microservice codebase across repositories. Start from key services or layers, trace their immediate callers, dependencies, event producers and consumers, and teach architecture, models, and code with TypeScript comparisons. Use for guided learning, not routine implementation or review.
---

# Codebase onboarding

Teach the learner to navigate and reason about an existing system independently. Default to extended explanations first, then practical scenarios. Assume comfort with everyday TypeScript; explain advanced concepts when they appear. Adapt to the learner's corrections and requests for a different pace or depth.

## Establish the starting set

Accept a parent workspace containing repositories, key repositories or services, optional priority layers (UI, BFF, backend domain), and an optional business journey, endpoint, or event. Reuse supplied context instead of asking for it again. Resolve names against the workspace; clarify only material ambiguity. If no starting points were supplied, inventory the workspace and recommend a small starting set with a reason before beginning detailed lessons.

Treat explicitly named priority layers as additional starting points. Resolve broad labels such as "UI" to concrete applications or services; a workspace can contain several. Distinguish repositories from runtime units: one repository may contain a BFF, workers, and shared libraries. Record the concrete starting set so the coverage boundary is reviewable.

Inspect applicable project instructions, repository roots, language/framework versions, manifests, entry points, infrastructure, tests, and existing architecture documentation. Exclude dependencies, generated output, and build artifacts from broad source searches unless needed to resolve a connection. Inventory broadly, then search selectively; do not load entire repositories into context.

Read application code without changing it. Save learning notes as described below. Running services, modifying application code, or making practice changes requires a request for that activity; onboarding alone does not request it. Do not clone or pull repositories automatically. If files or tools are unavailable, state the access gap and continue with accessible evidence rather than inventing contents.

## Discover the service neighborhood

Use concrete relationship labels rather than assuming a shared meaning of "upstream" and "downstream": calls, publishes, consumes, and reads/writes. Find both incoming and outgoing connections for every starting service.

- For requests, connect the caller's method, URL/path and configuration to the receiver's route, handler and contract. Resolve gateway rewrites, BFF clients, generated clients, and dependency-injection wiring where relevant. An endpoint definition alone does not identify its callers.
- For events, find publication sites and handlers across repositories. Match topic/exchange, routing key or subscription filter, event schema/version, and configuration. Distinguish a schema declaration from a running producer or consumer. A matching event class name alone does not establish a connection.
- Search for every evidenced direct consumer, including fan-out. Where relevant, distinguish independent subscriptions from workers sharing a consumer group. Preserve unresolved aliases, environment substitutions, wildcard bindings, external consumers, and deployment-specific behavior as open questions.
- Identify database/cache ownership and read/write sites. A shared library is a code dependency, not a runtime service hop. Shared storage is a data coupling: show which services access it and how, without inventing a request or event edge between them.

Apply this depth rule consistently:

1. Deep coverage includes the starting services and the union of their immediate incoming and outgoing application-service neighbors. Resolve each neighbor relative to the original starting set, not relative to the latest service discovered.
2. A producer-to-broker-to-consumer relationship is one logical service connection. Describe the transport and its configured behavior separately. An application service that transforms or forwards an event is a service boundary, even if called a relay.
3. Follow relevant code paths inside each direct neighbor deeply enough to explain its contract, transformations, models, side effects, failures, and tests. Include all evidenced direct neighbors in the map and learning backlog; teach large neighborhoods across sessions instead of silently dropping neighbors or explaining every unrelated function.
4. Summarize farther services only as needed to complete the story. Do not recursively promote neighbors into starting points. Deduplicate overlapping neighborhoods and cycles. Recompute the boundary when the learner explicitly adds a repository, service, or priority layer.

Build an evidence-backed map with the starting set, direct neighbors, farther context, and unresolved connections visually distinguished. For each meaningful edge, preserve both endpoint references when available, the operation/event and payload contract, and the configuration that binds them. Use repository-relative paths, symbols and line references; record relevant revisions. State what was searched before claiming a producer or consumer was not found. Absence from the checkout does not establish absence from the deployed system.

Treat documentation as evidence to check against code and configuration. Preserve disagreements rather than silently choosing a convenient story. Distinguish source-supported behavior, runtime-observed behavior, and hypotheses; source inspection cannot establish which configuration is deployed or that a path actually executed.

## Teach through business journeys

Choose a representative journey with a clear trigger, meaningful connections, and available evidence when the learner has not chosen one. Explain its business purpose and vocabulary before implementation details. Include an architecture map and sequence diagram where they clarify the explanation; use portable Markdown/Mermaid with a short textual explanation.

Give an extended, coherent walkthrough before introducing exercises. Keep individual code excerpts readable while completing the connected story. End at a natural stopping point for questions; do not repeatedly interrupt the explanation with quizzes or dump every service's internals at once. Honor requests to pause, go deeper, or explain a particular line immediately.

For each journey:

1. Trace a concrete synthetic input from its trigger to its observable outcome. Show execution order, branching, synchronous calls, asynchronous messages, state changes, and what the caller can know at each stage. Include failures, not just the happy path. Do not present invented payloads as captured production traffic.
2. When UI/BFF layers are involved, connect the user action, frontend state, request construction, BFF aggregation/transformation, backend behavior, and returned UI state. Apply the same depth boundary to these layers; do not omit them merely because they are not called microservices.
3. Connect UI models, request/response DTOs, domain objects, event schemas, and stored records. Explain identifiers, relationships, validation, ownership, state transitions, serialization, schema evolution, and consistency where relevant. Distinguish shared business concepts from actually shared schemas or databases.
4. Walk through selected original-language functions line by line. Explain each line's role, inputs and outputs, control flow, types, mutation, side effects and errors; follow important callers, callees and framework wiring. Separate behavior established by syntax from behavior supplied by frameworks or configuration. Group repetitive lines only after explaining the pattern, and offer expansion.
5. Pair unfamiliar constructs with small TypeScript examples. Label analogies or pseudocode and explain semantic gaps such as ownership, nullability, concurrency, exceptions, transactions, or serialization. Do not imply that TypeScript syntax reproduces another runtime. Check unfamiliar behavior against official documentation for the versions actually used; if that cannot be verified, mark the uncertainty.
6. Explain relevant authentication and authorization, timeout/retry ownership, duplicate handling, partial failure, consistency boundaries, tests, logs and correlation identifiers. Do not assume idempotency, atomic writes plus publication, or delivery guarantees without evidence. Identify the tests or runtime observations that could settle unresolved behavior, and distinguish proposed checks from checks actually run.

After the walkthrough, invite questions and offer a related practical scenario: trace a new path, explain a model transformation, predict a failure's effects, or identify where a small requirement belongs and how to verify it. Wait for the learner's attempt before revealing the solution. Offer progressively stronger hints and then source-grounded feedback. If they prefer to see the answer or skip practice, comply and record the topic as explained rather than independently demonstrated. Never invent learner answers.

## Preserve continuity

Keep Markdown notes in `onboarding-notes/` under the target parent workspace, outside the reusable skill and service repositories. Extend existing notes without overwriting unrelated material. Use a small structure:

- `system.md`: workspace/repository inventory, versions and revisions, starting set and boundary, service map with evidence, glossary, and unresolved architecture questions.
- `journeys/<descriptive-name>.md`: the journey, sequence diagram, model transformations, source references, code explanations, practical scenario and any actual learner response or feedback.
- `progress.md`: coverage and understanding by topic, open questions, remaining direct neighbors, last session, and the next recommended lesson.

Record repository commit IDs and relevant uncommitted changes; for non-Git sources, record that limitation and enough file information to recheck the lesson. On resume, read progress and the relevant notes first, compare source revisions and working-tree changes, and refresh affected claims and references. A changed commit alone does not invalidate unrelated lessons; an unchanged commit does not rule out local edits. Keep historical learner evidence while flagging explanations that need updating.

Update notes at meaningful stopping points. If note writing is unavailable, provide a copyable Markdown handoff containing the same essentials and disclose that it was not saved. Do not copy secrets or real personal data into examples or notes.

Track each topic as **mapped**, **explained**, **practiced with help**, or **demonstrated independently**. Tie the latter two to actual learner attempts. A correct answer after seeing the solution is not independent evidence. Coverage measures what was visited; it does not certify understanding.

The initial onboarding cycle is complete when the learner independently explains the selected service neighborhood, traces a related unfamiliar flow, interprets its important code and models, reasons through a failure, and locates a small change with a credible verification approach. State the assessed scope and remaining gaps; continue deeper lessons where needed rather than claiming proficiency across unread code.
