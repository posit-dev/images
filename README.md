<p>
<a href="https://posit.co/">
<picture>
  <source media="(prefers-color-scheme: dark)" srcset="https://cdn.posit.co/platform/containers/logos/Posit-Logos-2024_horiz-reverse-quarto-web.svg">
  <source media="(prefers-color-scheme: light)" srcset="https://cdn.posit.co/platform/containers/logos/Posit-Logos-2024_horiz-full-color-quarto-web.svg">
  <img alt="Posit Logo" src="https://cdn.posit.co/platform/containers/logos/Posit-Logos-2024_horiz-full-color-quarto-web.svg" height="120">
</picture>
&nbsp;&nbsp;&nbsp;&nbsp;
<img alt="Container cube" src="https://cdn.posit.co/platform/containers/logos/container_cube.svg" height="120">
</a>
</p>

# Posit Container Images

This is the home repository for Posit Container Images, which includes images for [Posit Connect](https://docs.posit.co/connect/), [Posit Package Manager](https://docs.posit.co/rspm/), and [Posit Workbench](https://docs.posit.co/ide/server-pro/).

> [!NOTE]
> These images are in preview as Posit migrates container images from [rstudio/rstudio-docker-products](https://github.com/rstudio/rstudio-docker-products). The existing images remain supported.

## Prerequisites

| Tool | Required for | Install                                                                    |
|------|-------------|----------------------------------------------------------------------------|
| [Docker](https://docs.docker.com/get-docker/) | Running containers locally | [Get Docker](https://docs.docker.com/get-docker/)                          |
| Product license | All products | [Licensing FAQ](https://docs.posit.co/licensing/licensing-faq.html), [Request a trial license](https://posit.co/trial-license/) |

## Quick Start

### [Posit Connect](https://github.com/posit-dev/images-connect)

```bash
PCT_VERSION="2026.02.0"
PCT_IMAGE="ghcr.io/posit-dev/connect"  # or docker.io/posit/connect
PCT_LICENSE="/path/to/license.lic"
docker run -d \
  --name connect \
  --privileged \
  -p 3939:3939 \
  -v ${PCT_LICENSE}:/etc/rstudio-connect/license.lic \
  ${PCT_IMAGE}:${PCT_VERSION}
```

Access Posit Connect at `http://localhost:3939`.

See the [Connect installation guide](https://docs.posit.co/connect/admin/getting-started/) for full setup instructions.

### [Posit Package Manager](https://github.com/posit-dev/images-package-manager)

```bash
PPM_VERSION="2025.12.0"
PPM_IMAGE="ghcr.io/posit-dev/package-manager"  # or docker.io/posit/package-manager
PPM_LICENSE="/path/to/license.lic"
docker run -d \
  --name package-manager \
  -p 4242:4242 \
  -v ${PPM_LICENSE}:/etc/rstudio-pm/license.lic \
  ${PPM_IMAGE}:${PPM_VERSION}
```

Access Package Manager at `http://localhost:4242`.

See the [Package Manager installation guide](https://docs.posit.co/rspm/admin/getting-started/installation/) for full setup instructions.

### [Posit Workbench](https://github.com/posit-dev/images-workbench)

```bash
PWB_VERSION="2026.01.1"
PWB_IMAGE="ghcr.io/posit-dev/workbench"  # or docker.io/posit/workbench
PWB_LICENSE="/path/to/license.lic"
docker run -d \
  --name workbench \
  -p 8787:8787 \
  -e PWB_TESTUSER=posit \
  -e PWB_TESTUSER_PASSWD=posit \
  -v ${PWB_LICENSE}:/etc/rstudio-server/license.lic \
  ${PWB_IMAGE}:${PWB_VERSION}
```

Access Workbench at `http://localhost:8787`. Log in with username `posit` and password `posit`.

See the [Workbench installation guide](https://docs.posit.co/ide/server-pro/getting_started/installation/) for full setup instructions.

A [product license](https://docs.posit.co/licensing/licensing-faq.html) is required for each product. Posit recommends license file activation.

### With Docker Compose

Run all three products together. Update the license file paths on the host (and the optional configuration file paths) before starting the stack.

```yaml
services:
  connect:
    image: ghcr.io/posit-dev/connect:latest
    privileged: true
    ports:
      - "3939:3939"
    volumes:
      - /path/to/connect-license.lic:/etc/rstudio-connect/license.lic
      - /path/to/rstudio-connect.gcfg:/etc/rstudio-connect/rstudio-connect.gcfg:ro
      - connect-data:/var/lib/rstudio-connect
    restart: unless-stopped

  package-manager:
    image: ghcr.io/posit-dev/package-manager:latest
    ports:
      - "4242:4242"
    volumes:
      - /path/to/package-manager-license.lic:/etc/rstudio-pm/license.lic
      - /path/to/rstudio-pm.gcfg:/etc/rstudio-pm/rstudio-pm.gcfg:ro
      - package-manager-data:/var/lib/rstudio-pm
    restart: unless-stopped

  workbench:
    image: ghcr.io/posit-dev/workbench:latest
    ports:
      - "8787:8787"
    environment:
      PWB_TESTUSER: posit
      PWB_TESTUSER_PASSWD: posit
    volumes:
      - /path/to/workbench-license.lic:/etc/rstudio-server/license.lic
      - /path/to/rstudio:/etc/rstudio:ro
      - workbench-home:/home
      - workbench-shared:/var/lib/rstudio-server
    restart: unless-stopped

volumes:
  connect-data:
  package-manager-data:
  workbench-home:
  workbench-shared:
```

Start the stack with `docker compose up -d`. Access Connect at `http://localhost:3939`, Package Manager at `http://localhost:4242`, and Workbench at `http://localhost:8787` (log in with username `posit` and password `posit`).

## Images

### [Posit Connect](https://github.com/posit-dev/images-connect)

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `connect` | [`docker.io/posit/connect`](https://hub.docker.com/r/posit/connect) | [`ghcr.io/posit-dev/connect`](https://github.com/posit-dev/images-connect/pkgs/container/connect) |
| `connect-content` | [`docker.io/posit/connect-content`](https://hub.docker.com/r/posit/connect-content) | [`ghcr.io/posit-dev/connect-content`](https://github.com/posit-dev/images-connect/pkgs/container/connect-content) |
| `connect-content-init` | [`docker.io/posit/connect-content-init`](https://hub.docker.com/r/posit/connect-content-init) | [`ghcr.io/posit-dev/connect-content-init`](https://github.com/posit-dev/images-connect/pkgs/container/connect-content-init) |

### [Posit Package Manager](https://github.com/posit-dev/images-package-manager)

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `package-manager` | [`docker.io/posit/package-manager`](https://hub.docker.com/r/posit/package-manager) | [`ghcr.io/posit-dev/package-manager`](https://github.com/posit-dev/images-package-manager/pkgs/container/package-manager) |

### [Posit Workbench](https://github.com/posit-dev/images-workbench)

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `workbench` | [`docker.io/posit/workbench`](https://hub.docker.com/r/posit/workbench) | [`ghcr.io/posit-dev/workbench`](https://github.com/posit-dev/images-workbench/pkgs/container/workbench) |
| `workbench-session` | [`docker.io/posit/workbench-session`](https://hub.docker.com/r/posit/workbench-session) | [`ghcr.io/posit-dev/workbench-session`](https://github.com/posit-dev/images-workbench/pkgs/container/workbench-session) |
| `workbench-session-init` | [`docker.io/posit/workbench-session-init`](https://hub.docker.com/r/posit/workbench-session-init) | [`ghcr.io/posit-dev/workbench-session-init`](https://github.com/posit-dev/images-workbench/pkgs/container/workbench-session-init) |
| `workbench-positron-init` | [`docker.io/posit/workbench-positron-init`](https://hub.docker.com/r/posit/workbench-positron-init) | [`ghcr.io/posit-dev/workbench-positron-init`](https://github.com/posit-dev/images-workbench/pkgs/container/workbench-positron-init) |

## Deploying on Kubernetes

These images work with the [Posit Helm charts](https://docs.posit.co/helm/) for Kubernetes deployments. See each product repository for Helm values and deployment instructions:

- [Connect Helm deployment](https://github.com/posit-dev/images-connect#deploying-on-kubernetes)
- [Package Manager Helm deployment](https://github.com/posit-dev/images-package-manager#deploying-on-kubernetes)
- [Workbench Helm deployment](https://github.com/posit-dev/images-workbench#deploying-on-kubernetes)

## Image Variants

| Variant | Suffix | Description                                                        |
|---------|--------|--------------------------------------------------------------------|
| Standard | `-std` | Includes R, Python, and Quarto. Runs out of the box.               |
| Minimal | `-min` | Base image for custom builds. Not fully functional until extended. |

For examples of extending Minimal base images, see the [extending examples](https://github.com/posit-dev/images-examples/tree/main/extending).

## Image Tag Format

Ubuntu 24.04 is the default OS for all images.

- `{version}` — Latest OS, standard variant (e.g., `2026.02.0`)
- `{version}-{os}` — Explicit OS, standard variant (e.g., `2026.02.0-ubuntu-24.04`)
- `{version}-{os}-{variant}` — Explicit OS and variant (e.g., `2026.02.0-ubuntu-24.04-std`)
- `latest` — Latest version, default OS, standard variant
- **Content/session images**: `R{r_version}-python{python_version}-{os}` (e.g., `R4.5.2-python3.14.3-ubuntu-24.04`)

## Registries

- Docker Hub: [`posit`](https://hub.docker.com/u/posit)
- GitHub Container Registry: [`posit-dev`](https://github.com/orgs/posit-dev/packages)

## Code Repositories

### Image Definitions

| Repository | Description |
|------------|-------------|
| [images-connect](https://github.com/posit-dev/images-connect) | Posit Connect Container Images |
| [images-package-manager](https://github.com/posit-dev/images-package-manager) | Posit Package Manager Container Images |
| [images-workbench](https://github.com/posit-dev/images-workbench) | Posit Workbench Container Images |

### Examples and Tooling

| Repository | Description |
|------------|-------------|
| [images](https://github.com/posit-dev/images) | Posit Container Image Meta Repository |
| [images-examples](https://github.com/posit-dev/images-examples) | Examples for using and extending Posit Container Images |
| [images-shared](https://github.com/posit-dev/images-shared) | Shared Tooling for Container Images |

## Shared Tooling

All product image repositories are built using shared tooling from the [images-shared](https://github.com/posit-dev/images-shared) repository:

- [Bakery CLI](https://github.com/posit-dev/images-shared/tree/main/posit-bakery) — Manages matrixed container image builds with variant support (Standard `std` / Minimal `min`), version management, and parallel building via Docker Buildx Bake
- [CI Workflows](https://github.com/posit-dev/images-shared/blob/main/CI.md) — Shared GitHub Actions workflows for building, testing, and pushing images
- [Bakery Examples](https://github.com/posit-dev/images-examples/tree/main/bakery) — Step-by-step examples for building custom images with Bakery
- [Extending Examples](https://github.com/posit-dev/images-examples/tree/main/extending) — Examples of extending Minimal base images with additional layers (Python, R, system dependencies, etc.)

## Design Principles

### Static Definitions

Each `Containerfile` (or `Dockerfile`) is static and can be built using multiple backends  (e.g. [docker buildx bake](https://github.com/docker/buildx#installing), [podman](https://podman-desktop.io/docs/installation)). Containerfiles are generated from [Jinja2 templates](https://github.com/posit-dev/images-shared/blob/main/posit-bakery/TEMPLATING.md) using Bakery, which resolves [dependency versions][bakery-dependency] and renders templates into build-ready files with no runtime logic.

### Security

Posit rebuilds images for [all supported product versions](https://docs.posit.co/supported-versions/) on a weekly basis to ensure system packages are using the latest security patches.

### ARM Support

We are adding [multi-platform images](https://docs.docker.com/build/building/multi-platform/) as Posit rolls out support for ARM64 chipsets.

[Package Manager](https://github.com/posit-dev/images-package-manager) and [Connect](https://github.com/posit-dev/images-connect) now have multi-platform images that support both `linux/amd64` and `linux/arm64`.

### Extensibility

A single [configuration file][bakery-configuration] (`bakery.yaml`) defines:

| Property | Examples |
|----------|----------|
| [Images][bakery-image] | `connect`, `workbench`, `package-manager` |
| [Versions][bakery-version] | `2024.12.0`, `2025.01.0` |
| [Variants][bakery-variant] | Minimal (`min`), Standard (`std`) |
| [Dependencies][bakery-dependency] | Python, R, Quarto |
| [Operating system][bakery-os] | Ubuntu 24.04, Ubuntu 22.04 |
| Platforms | `linux/amd64`, `linux/arm64` |
| [Tags][bakery-tag] | `latest`, `2024.12.0-ubuntu2204` |
| [Registries][bakery-registry] | Docker Hub, GHCR |

## Share your Feedback

We invite you to join us on [GitHub Discussions](https://github.com/posit-dev/images/discussions) to ask questions and share feedback.

## Code of Conduct

We expect all contributors to adhere to the project's [Code of Conduct](CODE_OF_CONDUCT.md) and create a positive and inclusive community.

## License

Posit Container Images and associated tooling are licensed under the [MIT License](LICENSE.md)

[bakery-configuration]: https://posit-dev.github.io/images-shared/configuration.html#bakery-configuration
[bakery-image]: https://posit-dev.github.io/images-shared/configuration.html#image
[bakery-version]: https://posit-dev.github.io/images-shared/configuration.html#imageversion
[bakery-variant]: https://posit-dev.github.io/images-shared/configuration.html#imagevariant
[bakery-dependency]: https://posit-dev.github.io/images-shared/configuration.html#dependencyconstraint
[bakery-os]: https://posit-dev.github.io/images-shared/configuration.html#imageversionos
[bakery-tag]: https://posit-dev.github.io/images-shared/configuration.html#tagpattern
[bakery-registry]: https://posit-dev.github.io/images-shared/configuration.html#registry
