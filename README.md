# Bash-CMD-PowerShell-Cheat-Sheet

# Command Line Reference Collection

A practical command-line reference library for **Bash/Linux**, **Windows CMD/Batch**, and **PowerShell**.

This repository contains large, example-driven cheat sheets designed as a fast lookup resource for day-to-day terminal work, scripting, automation, troubleshooting, and system administration.

## Overview

The project is a compact knowledge base focused on three major command-line environments:

- **Bash & Linux** — Unix/Linux shell usage and common system commands
- **CMD & Batch** — classic Windows command prompt and batch scripting
- **PowerShell** — Windows automation and object-based scripting

The documentation is written in **German** and organized as standalone Markdown references.

## Contents

### Included guides

- [`bash.md`](./bash.md) — Bash and Linux command reference
- [`cmd.md`](./cmd.md) — Windows CMD and Batch command reference
- [`powershell.md`](./powershell.md) — PowerShell command and automation reference

## What’s inside

Depending on the file, topics include:

- filesystem navigation and directory management
- file creation, copying, moving, and deletion
- reading and editing file contents
- searching and filtering with command-line tools
- text processing
- permissions and ownership
- processes and services
- networking and diagnostics
- scripting and automation workflows
- administration and troubleshooting commands

## Who this is for

This repository is useful for:

- developers switching between Linux and Windows environments
- system administrators and DevOps engineers
- students learning shell fundamentals
- power users who want quick command examples
- anyone building a local command reference for AI/dev tooling workflows

## Repository structure

```text
.
├── bash.md
├── cmd.md
└── powershell.md
```

## How to use

### Read on GitHub
Browse the Markdown files directly in the repository for quick reference.

### Use as a local cheat sheet
Clone the repository and open the files in your editor:

```bash
git clone https://github.com/ChrisFle2003/Bash-CMD-PowerShell-Cheat-Sheet.git
cd Bash-CMD-PowerShell-Cheat-Sheet
```

### Search specific commands
Use your editor search or command-line tools such as:

```bash
grep -n "rsync" bash.md
grep -n "robocopy" cmd.md
grep -n "Get-Process" powershell.md
```

## Suggested use cases

- refresh syntax you do not use every day
- compare equivalent commands across platforms
- copy example commands into scripts or notes
- keep as a quick terminal reference during development
- provide command knowledge to local AI assistants or MCP-based tooling

## Language

The documentation content is currently written in **German**.

A few files also use ASCII spellings such as `ae`, `oe`, and `ue` instead of umlauts for compatibility and portability.

## Notes

- This repository is primarily a **reference/cheat-sheet collection**, not a software package.
- Commands may behave differently depending on OS version, shell version, permissions, installed tools, or execution policy.
- Review destructive commands carefully before running them, especially commands involving deletion, ownership changes, permissions, or elevated access.

## Contributing

Contributions are welcome. Useful improvements include:

- fixing mistakes or outdated commands
- expanding missing topics
- improving formatting and consistency
- adding English translations
- adding examples for newer shell features

## License

Add a license file if you plan to publish this repository publicly.

Common choices include MIT, Apache-2.0, or CC BY 4.0 depending on how you want the documentation reused.
