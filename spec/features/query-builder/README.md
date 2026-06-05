# Feature: Query Builder Skill

> [SpecScore.**Studio**](https://specscore.studio): | [Explore](https://specscore.studio/app/github.com/datatug/datatug-ai-skills/spec/features/query-builder?op=explore) | [Edit](https://specscore.studio/app/github.com/datatug/datatug-ai-skills/spec/features/query-builder?op=edit) | [Ask question](https://specscore.studio/app/github.com/datatug/datatug-ai-skills/spec/features/query-builder?op=ask) | [Request change](https://specscore.studio/app/github.com/datatug/datatug-ai-skills/spec/features/query-builder?op=request-change) |
**Status:** Approved
**Date:** 2026-06-05
**Owner:** alexander.trakhimenok
**Source Ideas:** —
**Supersedes:** —
**Grade:** A

## Summary

The agent **skill** that teaches an AI-agent terminal to build and progressively refine a DataTug query by driving the serve-brokered query builder's MCP tools. It is the agent-side surface of the Serve-Brokered AI Query Builder Capability (`specscore:feature/serve-brokered-query-builder@github.com/datatug/datatug`): the skill ensures a `datatug serve` daemon is reachable, creates a tab and hands the user a deep link to open the Web UI, and on each natural-language request constructs the prose + structured delta + full query and calls `apply_change` — staying quiet in the terminal by default and offering candidate options when unsure. This Feature specifies what the skill must instruct; the skill file (`skills/query-builder/SKILL.md`) is its implementation.

## Problem

In the serve-brokered architecture the conversation lives in the agent terminal, but only if the agent knows the protocol: ensure the daemon, create a tab, share the link, send each change as a prose+delta+full-query triple over MCP, keep the terminal quiet, re-read state the user changed in the Web UI, and offer options instead of guessing. Without a skill encoding that contract, every agent would improvise — writing SQL by hand, echoing noise, or letting the backend interpret prose — defeating the architecture. This skill pins the agent's half of the protocol so any MCP-capable terminal drives the builder consistently.

## Behavior

### Scope and activation

#### REQ: skill-scope

The skill MUST instruct the agent to build and refine queries exclusively by driving the daemon's MCP tools (`create_tab`, `apply_change`, `inspect`, `run`) — not by writing SQL files, editing project files, or any path that bypasses the daemon.

#### REQ: daemon-preflight

The skill MUST include a pre-flight that verifies `datatug` is installed and a `serve` daemon exposing the query-builder MCP endpoint is reachable; on a miss it MUST direct the user to start `datatug serve` (or run `/datatug:install`) and stop, rather than proceeding, consistent with the other skills' pre-flight convention.

### Session bootstrap

#### REQ: create-tab-and-share-link

When the user starts building a query, the skill MUST have the agent call `create_tab` (selecting the target connection) and present the returned deep link to the user so they can open the Web UI for that tab.

### Refinement loop

#### REQ: apply-change-triple

For each natural-language request against an existing tab, the skill MUST have the agent construct the prose description, the structured delta, and the full resulting query, and call `apply_change` with the explicit tab id carrying all three. The agent MUST NOT rely on the daemon to interpret prose.

#### REQ: terminal-quiet

By default the skill MUST keep the agent from echoing the query in the terminal; it sends each change to the daemon and points the user to the Web UI. The agent MUST show the query (in dalgo or native form) only when the user asks, either once or on every change.

#### REQ: reconcile-web-edits

Before applying each new change, the skill MUST have the agent re-read the tab's current query (the MCP resource) so deterministic edits the user made in the Web UI are preserved and the next delta builds on the true current state.

### Disambiguation

#### REQ: offer-options-when-unsure

When the agent cannot confidently resolve a request to a single query, the skill MUST have it submit multiple candidate options for the tab rather than commit one guess, so the user can compare their results in the Web UI.

### Safety

#### REQ: read-only-relay

The skill MUST instruct the agent to produce only read-only queries and, when the daemon refuses a mutating or DDL request, to relay that refusal in the terminal rather than retrying or working around it.

## Interaction with Other Features

| Feature | Interaction |
|---|---|
| Daemon `serve-brokered-query-builder@github.com/datatug/datatug-cli` | The MCP server this skill drives (`create_tab` / `apply_change` / `inspect` / `run`). |
| Capability `serve-brokered-query-builder@github.com/datatug/datatug` | This skill is the agent-side surface realizing the Capability's `apply-change-payload`, `terminal-quiet-by-default`, `candidate-options`, and `read-only-queries` from the agent's perspective. |
| [install](../../skills/install/SKILL.md), other skills | Shares the `datatug`-on-PATH pre-flight convention and the `/datatug:install` fallback. |

## Acceptance Criteria

### AC: drives-mcp-only (verifies REQ:skill-scope)

**Given** an agent following the query-builder skill
**When** the user asks to build a query
**Then** the agent uses the daemon's MCP tools and does not write SQL files or edit project files to construct the query.

### AC: preflight-stops-on-miss (verifies REQ:daemon-preflight)

**Given** an environment where `datatug` is missing or no query-builder daemon is reachable
**When** the agent starts the skill
**Then** it runs the pre-flight check and directs the user to start `datatug serve` (or `/datatug:install`) and stops, instead of attempting to build a query.

### AC: creates-tab-and-shares-link (verifies REQ:create-tab-and-share-link)

**Given** a reachable daemon and a chosen connection
**When** the user begins building a query
**Then** the agent calls `create_tab` and presents the returned deep link for the user to open the Web UI.

### AC: applies-change-as-triple (verifies REQ:apply-change-triple)

**Given** a tab with a current query
**When** the user requests a refinement
**Then** the agent calls `apply_change` with the tab id, supplying the prose, the structured delta, and the full resulting query — not relying on the daemon to interpret prose.

### AC: stays-quiet-then-shows (verifies REQ:terminal-quiet)

**Given** an active build session
**When** the user has not asked to see the query
**Then** the agent does not print the query in the terminal; and when the user asks, it prints the dalgo or native form.

### AC: rereads-before-next-change (verifies REQ:reconcile-web-edits)

**Given** the user made a deterministic edit in the Web UI
**When** the agent applies the next request
**Then** it first re-reads the tab's current query via MCP so the user's Web-UI edit is preserved in the next delta.

### AC: offers-options-when-unsure (verifies REQ:offer-options-when-unsure)

**Given** a request the agent cannot confidently resolve to one query
**When** the agent responds
**Then** it submits multiple candidate options for the tab rather than committing a single guess.

### AC: refuses-and-relays-mutation (verifies REQ:read-only-relay)

**Given** a tab with a current query
**When** the user asks to mutate data or schema
**Then** the agent produces no mutating query and relays the daemon's refusal in the terminal.

## Rehearse Integration

Each AC is an observable agent behavior (which tools are called, with what payload, what is or isn't printed, the pre-flight stop, the options path) and is testable as a skill-behavior scenario. Stub scaffolding under `_tests/` is deferred to the Implement phase so the stub set tracks the authored `SKILL.md`, consistent with how the other repos in this Capability defer Rehearse stubs.

## Not Doing / Out of Scope

- The daemon's server-side behavior (state, MCP endpoint, execution) — specified in `serve-brokered-query-builder@github.com/datatug/datatug-cli`.
- The Web UI screen and its comms layer — specified in `datatug-apps` (`query-builder`, `ai-terminal-query-builder`).
- Choice of LLM provider/model — the skill is provider-neutral; it instructs whatever agent runs it.
- Metadata/project authoring by conversation — a separate concern (see the `conversational-metadata-authoring` idea); this skill builds queries only.
- The exact MCP tool argument schemas — owned by the daemon Feature; this skill consumes them.

## Open Questions

- Should the skill be `user-invocable` (a `/datatug:query-builder` command) in addition to being model-invoked, and under what name?
- Should the skill auto-start `datatug serve` (with the builder) when it is not running, or only instruct the user to start it?

---
*This document follows the https://specscore.md/feature-specification*
