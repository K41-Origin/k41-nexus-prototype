# Project Overview — Current State

**Status:** Partially audited  
**Source of truth:** repository source, package metadata, README, CONTEXT.md, CLAUDE.md and protocol contract.

## Purpose

Describe the current product, users, execution modes, repository boundaries and terminology. Do not describe the proposed K41 Hybrid LLM Engine as if it already exists.

## Product identity

- **Verified:** package identity and product purpose are documented in the root README and package metadata.
- **Verified:** current product is Pixel Agents, a pixel-art office interface for observing/interacting with AI coding-agent sessions.
- **Open:** complete supported-provider matrix and current release/runtime support must be validated against implementation and release workflows.

## Runtime modes

Document each mode separately:
- VS Code extension with webview and editor-owned terminals.
- Standalone CLI with local HTTP/WebSocket server and browser UI.
- Remote browser/mobile access to a standalone host, including its actual security assumptions.

## Repository map

See [the architecture index](README.md) and each module's `MODULE-OVERVIEW.md`.

## Domain vocabulary

`CONTEXT.md` is the canonical glossary. Preserve its definitions; do not silently redefine Agent, Sub-agent, Team, Lead, Teammate, Host, Adapter, or Headless agent.

## Audit status

This overview is a starting point. Detailed claims must link to module/file references as the audit progresses.
