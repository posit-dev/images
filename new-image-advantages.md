# New Posit Container Images: Advantages for Marketing

## What Changed

Until now, Posit shipped container images from a single monorepo called
`rstudio/rstudio-docker-products`. That repo contained hand-written Dockerfiles for
Connect, Workbench, and Package Manager, along with shared base images (`product-base`
and `product-base-pro`) that all three products inherited from. A single `docker-bake.hcl`
file at the repo root defined versions for R, Python, Quarto, and professional drivers
across all products at once.

The new images replace that monorepo with six purpose-built repositories under the
`posit-dev` GitHub organization. Each product has its own image definitions, CI pipelines,
and release cycle. Shared build tooling (Jinja2 template macros, CI workflows) lives in a
common `images-shared` repository, but no product image depends on another product's image
at build time or runtime.

---

## 1. Clean and Simple Naming

The old images used the `rstudio/` namespace with inconsistent naming conventions:

| Old Name | Role |
|----------|------|
| `rstudio/rstudio-connect` | Connect server |
| `rstudio/rstudio-workbench` | Workbench server |
| `rstudio/rstudio-package-manager` | Package Manager server |
| `rstudio/r-session-complete` | Workbench session for Kubernetes |
| `rstudio/content-base` | Connect content runtime |
| `rstudio/content-pro` | Connect content runtime with drivers |
| `rstudio/product-base` | Shared base layer |
| `rstudio/product-base-pro` | Shared base layer with drivers |

Eight images, three naming patterns, two that only exist as intermediate build layers.

The new images use the `posit/` namespace with one naming pattern:

| New Name | Role |
|----------|------|
| `posit/connect` | Connect server |
| `posit/workbench` | Workbench server |
| `posit/package-manager` | Package Manager server |
| `posit/connect-content` | Connect content runtime |
| `posit/connect-content-init` | Connect init container |
| `posit/workbench-session` | Workbench session for Kubernetes |
| `posit/workbench-session-init` | Workbench session init container |
| `posit/workbench-positron-init` | Positron IDE init container |

The product name comes first. Related images share the prefix. No intermediate build layers
are published. All images are available from both Docker Hub (`posit/`) and GitHub Container
Registry (`ghcr.io/posit-dev/`).

### Tag format

The old tags put the OS first (`rstudio/rstudio-connect:ubuntu2204-2026.03.1`) and used
codenames as aliases (`jammy-2026.03.1`). The new tags put the product version first, with
OS and variant as optional suffixes:

```
posit/connect:2026.02.0                          # default OS, standard variant
posit/connect:2026.02.0-ubuntu-24.04-min         # explicit OS, minimal variant
posit/connect-content:R4.5.2-python3.14.3-ubuntu-24.04   # content/session matrix
```

`docker pull posit/connect:2026.02.0` gives you a working image without knowing which
Ubuntu release it runs on. A `latest` tag is also available for quick evaluation.

---

## 2. Significant Security Improvements

The old repo's images could go weeks or months between OS-level security patches.
The new images change things:

**Weekly automated rebuilds for all supported product versions.** And not just the latest
release. If Connect 2025.12 and 2026.02 are both under active support, both get rebuilt
weekly with current OS patches.

**Vulnerability scanning in CI.** Scanning runs in the build pipeline before
images are published.

The new Minimal variant is relevant here too: it gives security teams a
small, well-defined image to scan instead of a large image with two R versions,
two Python versions, and drivers they may not need.

---

## 3. New Platform Support: ARM and Ubuntu 24

The old images were AMD64-only. As Posit Team products add ARM support, the new
images ship ARM builds as well.

The multi-architecture images require no user configuration. `docker pull` selects
the correct architecture for the host.

### Ubuntu 24.04

The old images supported only Ubuntu 22.04 after dropping Ubuntu 18.04 and CentOS 7 in
2025. Users who wanted a newer OS had to build their own images from scratch. The new images
default to Ubuntu 24.04 with Ubuntu 22.04 still available via tag.

---

## 4. Standard and Minimal Variants

The old images shipped one variant per product. Each image bundled two R versions, two
Python versions, Quarto, and professional drivers. You got a large image with predetermined
language versions. If you needed different versions or a smaller footprint, the README
advised you to "consider creating a fork of this repo" and maintain your own Dockerfiles.

The new images ship two variants:

**Standard** (`-std` tag suffix) includes R, Python, and Quarto. You pull it, provide a
license, and the product runs:

```bash
docker run -d --privileged -p 3939:3939 \
  -v /path/to/license.lic:/etc/rstudio-connect/license.lic \
  posit/connect:2026.02.0
```

**Minimal** (`-min` tag suffix) provides the product binaries on a clean Ubuntu base
without R, Python, Quarto, or drivers. Teams can layer in the specific language
versions and system libraries they need.

---

## 5. Documented Customization Path

The old repo's guidance for customization was to fork the entire monorepo:

> Consider creating a fork of this repo, where you can continue to merge in changes we
> make while having your own security scanning, base OS in use, or other custom changes.

Forking a monorepo that builds all three products means you inherit the full CI pipeline,
the shared base image chain, and the version management for products you may not use. Merging
upstream changes requires resolving conflicts across products.

The new images offer three paths:

**Use Standard images directly.** No customization. Best for evaluation and production
deployments that don't need custom system packages.

**Extend Minimal images with a Dockerfile.** The
[images-examples](https://github.com/posit-dev/images-examples) repository provides
tested, copy-paste Dockerfile recipes for the most common needs:
- Adding custom CA certificates for corporate proxies
- Configuring pip for private PyPI mirrors
- Installing specific R or Python versions
- Adding system dependencies like GDAL or database drivers

Each recipe layers onto a Minimal image. A platform team combines the recipes they need
into a single Dockerfile. This didn't exist in the old repo. The old repo's equivalent was
reading the product Dockerfile and figuring out what to change.

**Use Bakery for managing a fleet of images.** Organizations maintaining many image variants
across products and OS versions can use
[Posit Bakery](https://github.com/posit-dev/images-shared/tree/main/posit-bakery), the same
Jinja2 templating tool Posit uses to build its own images. A single `bakery.yaml`
configuration file defines the build matrix (images, versions, variants, OS choices, language
version constraints). Bakery resolves compatible dependency versions, renders static
Containerfiles, and orchestrates parallel builds via `docker buildx bake`.

The old repo also used `docker buildx bake`, but version management was manual. To update
a product version, an engineer edited version variables in the Justfile (`RSC_VERSION`,
`RSW_VERSION`, `RSPM_VERSION`), ran `just update-versions`, and opened a PR. Bakery replaces
this with constraint-based resolution: you define "latest 2 R versions matching >=4.3" and
Bakery determines the specific versions at build time.

---

## 6. No Shared Base Image Coupling

The old repo used `product-base` and `product-base-pro` as shared foundation layers.
Connect, Workbench, and Package Manager all inherited from them. If someone updated
`product-base` to change a system package, add an R version, or modify the Python
installation, that change propagated to all three products. A bug in the base layer
could break all product images in a single build. The CI pipeline built all products
together or not at all.

The new images have no shared base images. Each product builds from `ubuntu:24.04` (or
`ubuntu:22.04`) and uses shared macros to install R, Python, and system packages.
The macros are imported at template-rendering time, not at Docker build time. Updating how
R is installed for Connect does not change anything in the Workbench or Package Manager
images.

Each product has its own CI workflows with independent schedules:

| Repo | Production builds | Content/session builds | Dev builds |
|------|------------------|----------------------|------------|
| images-connect | Weekly, Sun 03:15 UTC | Weekly, Sun 04:15 UTC | Daily |
| images-workbench | Weekly, Sun 03:15 UTC | Weekly, Sun 04:15 UTC | Daily |
| images-package-manager | Weekly, Sun 03:15 UTC | N/A | Daily |

A CI failure in Connect does not block a Workbench release.

---

## Summary

| Area | Old (`rstudio/rstudio-docker-products`) | New (`posit-dev/images-*`) |
|------|----------------------------------------|--------------------------|
| Naming | 8 images, 3 naming patterns, `rstudio/` namespace | 8 images, 1 naming pattern, `posit/` namespace |
| Variants | One variant per product (large, bundled) | Standard (runs immediately) + Minimal (extend it) |
| Base images | Shared `product-base` / `product-base-pro` coupling all products | No shared base. Each product builds from Ubuntu + shared macros |
| Kubernetes | Content/session images as secondary, fixed 9-combo matrix | Full image sets per product, configurable matrix per product |
| Security | "Provided AS IS," periodic image removal | Weekly rebuilds across all supported versions, CI scanning |
| Customization | "Fork the monorepo" | Extending examples, Bakery CLI, or just use Standard |
| Version management | Manual edits to Justfile variables | Constraint-based resolution in `bakery.yaml` |
| ARM | Not available | Package Manager ships ARM, others following |
| CI isolation | All products in one pipeline | Independent CI per product, independent release cycles |
| OS support | Ubuntu 22.04 only | Ubuntu 24.04 default, 22.04 available |
| Documentation | Varies per product, mixed depth | Standalone README per image with consistent structure |
