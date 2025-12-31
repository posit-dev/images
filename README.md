# Posit Container Images

> [!IMPORTANT]
> These images are under active development and testing and are not yet supported by Posit.
>
> Please see [rstudio/rstudio-docker-products](https://github.com/rstudio/rstudio-docker-products) for officially supported images.

| Image | Docker Hub | GitHub Container Registry |
|-------|------------|---------------------------|
| `connect` | `docker.io/posit/connect` | `ghcr.io/posit-dev/connect` |
| `package-manager` | [`docker.io/posit/package-manager`](https://hub.docker.com/repository/docker/posit/package-manager/tags) | [`ghcr.io/posit-dev/package-manager`](https://github.com/posit-dev/images-package-manager/pkgs/container/package-manager) |
| `workbench` | `docker.io/posit/workbench` | `ghcr.io/posit-dev/workbench` |

## Registries

- Docker Hub: [`posit`](https://hub.docker.com/u/posit)
- GitHub Container Registry: [`posit-dev`](https://github.com/orgs/posit-dev/packages)

## Code Repositories

### Image Definitions

| Repository | Description |
|------------|-------------|
| `images-connect` | Posit Connect Container Images |
| [images-package-manager](https://github.com/posit-dev/images-package-manager) | Posit Package Manager Container Images |
| `images-workbench` | Posit Workbench Container Images |

### Examples and Tooling

| Repository | Description |
|------------|-------------|
| [images](https://github.com/posit-dev/images) | Posit Container Image Meta Repository |
| [images-examples](https://github.com/posit-dev/images-examples) | Examples for using and extending Posit Container Images |
| [images-shared](https://github.com/posit-dev/images-shared) | Shared Tooling for Container Images |

## Design Principles

### Static Definitions

Each `Containerfile` (or `Dockerfile`) is static and can be built using multiple backends  (e.g. [docker buildx bake](https://github.com/docker/buildx#installing), [podman](https://podman-desktop.io/docs/installation)).

### Security

Rapidly address security concerns by supporting scanning and routinely rebuilding images for [all supported product versions](https://docs.posit.co/supported-versions/).

### ARM Support

We are adding [multi-platform images](https://docs.docker.com/build/building/multi-platform/) as Posit rolls out support for ARM chipsets.

[Package Manager](https://github.com/posit-dev/images-package-manager) now has [multi-platform images](https://github.com/orgs/posit-dev/packages/container/package/package-manager).

### Extensibity

A single configuration file defines:

| Property         | Examples                                  |
|------------------|-------------------------------------------|
| Images           | `connect`, `workbench`, `package-manager` |
| Versions         | `2024.12.0`, `2025.01.0`                  |
| Variants         | Minimal (`min`), Standard (`std`)         |
| Dependencies     | Python, R, Quarto                         |
| Operating system | Ubuntu 24.04, Ubuntu 22.04                |
| Platforms        | `linux/amd64`, `linux/arm64`              |
| Tags             | `latest`, `2024.12.0-ubuntu2204`          |
| Registries       | Docker Hub, GHCR                          |

## Share your Feedback

We invite you to join us on [GitHub Discussions](https://github.com/posit-dev/images/discussions) to ask questions and share feedback.

## Code of Conduct

We expect all contributors to adhere to the project's [Code of Conduct](CODE_OF_CONDUCT.md) and create a positive and inclusive community.

## License

Posit Container Images and associated tooling are licensed under the [MIT License](LICENSE.md)
