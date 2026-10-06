# Repository Guide

## Repository Overview

Meta repository for Posit container images. Contains documentation, design principles, and links across all image repos. No `bakery.yaml`; no buildable images.

## Sibling Repositories

This project is part of a multi-repo ecosystem for Posit container images. **Read the
AGENTS.md in each affected sibling repo before making changes there.**

Do not make changes directly on `main`; use a topic branch.

- `../images-shared/` - Posit Bakery CLI tool for building, testing, and managing container images. Jinja2 templates, macros, and shared build tooling.
- `../images-connect/` - Posit Connect images: `connect` (Standard/Minimal variants), `connect-content` (matrix of R x Python), `connect-content-init`.
- `../images-package-manager/` - Posit Package Manager image: `package-manager` (Standard/Minimal variants). Supports multi-platform builds (amd64/arm64).
- `../images-workbench/` - Posit Workbench images: `workbench` (Standard/Minimal variants), `workbench-session` (R x Python matrix), `workbench-session-init`.
- `../images-examples/` - Examples for using and extending Posit container images.
- `../helm/` - Helm charts for Posit products: Connect, Workbench, Package Manager, and Chronicle.
