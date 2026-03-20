# Posit Container Images

> [!NOTE]
> These images are in preview as Posit migrates container images from [rstudio/rstudio-docker-products](https://github.com/rstudio/rstudio-docker-products). The existing images remain supported.

## Prerequisites

| Tool | Required for | Install |
|------|-------------|---------|
| [Docker](https://docs.docker.com/get-docker/) | Running containers locally | [Get Docker](https://docs.docker.com/get-docker/) |
| Posit License | All products | [Licensing FAQ](https://docs.posit.co/licensing/licensing-faq.html) |

## Quick Start

### Posit Connect

```bash
PCT_VERSION="2025.12.1"
docker run -d \
  --name connect \
  --privileged \
  -p 3939:3939 \
  -v /path/to/license.lic:/etc/rstudio-connect/license.lic \
  ghcr.io/posit-dev/connect:${PCT_VERSION}
  # Can also use docker.io/posit/connect
```

Access Posit Connect at `http://localhost:3939`.

### Posit Package Manager

```bash
PPM_VERSION="2025.12.0-14"
docker run -d \
  --name package-manager \
  -p 4242:4242 \
  -v /path/to/license.lic:/etc/rstudio-pm/license.lic \
  ghcr.io/posit-dev/package-manager:${PPM_VERSION}
  # Can also use docker.io/posit/package-manager
```

Access Package Manager at `http://localhost:4242`.

### Posit Workbench

```bash
PWB_VERSION="2025.09.2-418.pro4"
docker run -d \
  --name workbench \
  -p 8787:8787 \
  -e PWB_TESTUSER=posit \
  -e PWB_TESTUSER_PASSWD=posit \
  -v /path/to/license.lic:/etc/rstudio-server/license.lic \
  ghcr.io/posit-dev/workbench:${PWB_VERSION}
  # Can also use docker.io/posit/workbench
```

Access Workbench at `http://localhost:8787`. Log in with username `posit` and password `posit`.

A valid [Posit license](https://docs.posit.co/licensing/licensing-faq.html) is required for each product. Posit recommends license file activation. For detailed configuration, environment variables, and volume mounts, see each product's documentation linked below.

## Images

### Posit Connect

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `connect` | `docker.io/posit/connect` | `ghcr.io/posit-dev/connect` |
| `connect-content` | `docker.io/posit/connect-content` | `ghcr.io/posit-dev/connect-content` |
| `connect-content-init` | `docker.io/posit/connect-content-init` | `ghcr.io/posit-dev/connect-content-init` |

### Posit Package Manager

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `package-manager` | [`docker.io/posit/package-manager`](https://hub.docker.com/repository/docker/posit/package-manager/tags) | [`ghcr.io/posit-dev/package-manager`](https://github.com/posit-dev/images-package-manager/pkgs/container/package-manager) |

### Posit Workbench

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `workbench` | `docker.io/posit/workbench` | `ghcr.io/posit-dev/workbench` |
| `workbench-session` | `docker.io/posit/workbench-session` | `ghcr.io/posit-dev/workbench-session` |
| `workbench-session-init` | `docker.io/posit/workbench-session-init` | `ghcr.io/posit-dev/workbench-session-init` |
| `workbench-positron-init` | `docker.io/posit/workbench-positron-init` | `ghcr.io/posit-dev/workbench-positron-init` |

## Deploying on Kubernetes

These images work with the [Posit Helm charts](https://docs.posit.co/helm/) for Kubernetes deployments. See each product repository for Helm values and deployment instructions:

- [Connect Helm deployment](https://github.com/posit-dev/images-connect#deploying-on-kubernetes)
- [Package Manager Helm deployment](https://github.com/posit-dev/images-package-manager#deploying-on-kubernetes)
- [Workbench Helm deployment](https://github.com/posit-dev/images-workbench#deploying-on-kubernetes)

## Image Variants

| Variant | Suffix | Description |
|---------|--------|-------------|
| Standard | `-std` | Includes R, Python, and Quarto. Runs out of the box. |
| Minimal | `-min` | Base image for custom builds. Will not run as-is. |

For examples of extending Minimal base images, see https://github.com/posit-dev/images-examples/tree/main/extending

## Image Tag Format

- `{version}` — Latest OS, standard variant (e.g., `2025.12.1`)
- `{version}-{os}` — Explicit OS, standard variant (e.g., `2025.12.1-ubuntu-24.04`)
- `{version}-{os}-{variant}` — Explicit OS and variant (e.g., `2025.12.1-ubuntu-24.04-std`)
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

Rapidly address security concerns by supporting scanning and routinely rebuilding images for [all supported product versions](https://docs.posit.co/supported-versions/).

### ARM Support

We are adding [multi-platform images](https://docs.docker.com/build/building/multi-platform/) as Posit rolls out support for ARM chipsets.

[Package Manager](https://github.com/posit-dev/images-package-manager) now has [multi-platform images](https://github.com/orgs/posit-dev/packages/container/package/package-manager).

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

[bakery-configuration]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#bakery-configuration
[bakery-image]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#image
[bakery-version]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#imageversion
[bakery-variant]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#imagevariant
[bakery-dependency]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#dependencyconstraint
[bakery-os]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#imageversionos
[bakery-tag]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#tagpattern
[bakery-registry]: https://github.com/posit-dev/images-shared/blob/main/posit-bakery/CONFIGURATION.md#registry
