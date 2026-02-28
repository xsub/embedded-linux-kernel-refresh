# README.md

[# embedded-linux-kernel-refresh](https://github.com/xsub/embedded-linux-kernel-refresh/)

# Kernel Refresh for an Embedded Linux Engineer
#### Note: This course is aimed at Senior/Staff-level preparation.

## Start here
- Read: `docs/syllabus.md`
- Track progress: `ROADMAP.md`
- Set up labs: `docs/environment.md`
- Output format rules: `docs/methodology.md`
- Passing criteria: see `docs/syllabus.md#minimum-passing-standard-measurable`

## Repository layout
- `docs/` — syllabus, environment, methodology
- `kernel-notes/` — topic notes (mechanism → invariants → failure modes → debug plan)
- `labs/` — reproducible labs + patches + scripts
- `cheatsheets/` — condensed reference sheets
- `grilling/` — weekly interview drilling Q/A

## Working rules
- Each week produces **at least three commits**:
  1) notes, 2) lab, 3) grilling
- Every technical statement must be backed by:
  - a file/function reference in the kernel tree, or
  - a lab reproduction
- Prefer short notes that can be re-derived over long copied explanations.

## Licensing
This repository is multi-licensed by file type:
- **MIT** — tooling and scripts (default `LICENSE`)
- **CC BY 4.0** — documentation in `docs/`, `kernel-notes/`, `cheatsheets/`, `grilling/` (see `LICENSE-docs`)
- **GPL-2.0-only** — kernel modules and kernel patch files (see `LICENSE-kernel`)

Where files include an SPDX header, the SPDX identifier is authoritative.
