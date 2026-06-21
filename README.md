# AfzaliWP AI Coding Standards

This repository contains context files and system prompts designed to guide AI assistants (like Cursor, Copilot, ChatGPT, or Claude) in developing WordPress plugins for AfzaliWP. 

By providing these files as context, the AI is forced to adhere to strict security, performance, and structural guidelines, while maximizing token efficiency.

## Repository Contents

* **`agents.md`**: The primary system prompt. It defines the AI's persona as a Senior WordPress Developer and strictly enforces zero-chatter, token-efficient outputs.
* **`SECURITY_CHECK.md`**: Mandates for input sanitization, output escaping, nonce verification, capability checks, and secure database interactions.
* **`PERFORMANCE_CHECK.md`**: Guidelines for database optimization, caching (Transients/Object Cache), DRY principles, and micro-method architecture.
* **`STRUCTURE_CHECK.md`**: AfzaliWP-specific architectural rules, including namespace/autoloading conventions, singleton patterns, React/Control Panel integration, and ES6 JS structure.

## Usage

When starting a new AI session or configuring an AI IDE workspace (like Cursor):
1. Add these four files to your project's root or `.github` folder.
2. Reference or `@`-include these files in your initial prompt or system instructions.
3. Instruct the AI to read `agents.md` first.
