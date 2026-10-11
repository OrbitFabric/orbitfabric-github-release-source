# OrbitFabric GitHub Release Source

Provider-specific GitHub Releases acquisition for exact OrbitFabric adapter releases.

This package consumes the provider-neutral exact Catalog selection owned by OrbitFabric Core, acquires the selected Release Descriptor and descriptor-owned artifact from GitHub Releases, verifies exact identity and bytes, and constructs the existing Core `ResolvedAdapterRelease` handoff.

```text
OrbitFabric Core Catalog selection
    -> GitHub Release source binding
    -> exact GitHub Release lookup
    -> verify adapter-release.json against Catalog digest
    -> verify Source Coordinate + Release Version
    -> select descriptor-owned artifact
    -> verify artifact size + SHA-256
    -> ResolvedAdapterRelease
```

## Boundary

This product owns GitHub-specific acquisition only. It does not own Catalog selection semantics, Project Lock identity, installation semantics, Installed Adapter State, acceptance policy, publisher authority, registry topology or version solving.

GitHub release author/uploader metadata is retained as provider facts and is **not** treated as OrbitFabric publisher identity or authentication.

The consumer CLI orchestrates existing Core services. It does not redefine them.

## Consumer CLI

The package installs:

```text
orbitfabric-github-release-source
```

The provider CLI remains available for explicit resolution and Project Lock ensure. Core 1.4.0 adds the public `orbitfabric adapter install <adapter> --version <exact-version>` application composition.

### Resolve one exact release

```bash
orbitfabric-github-release-source resolve \
  path/to/catalog.json \
  github.com/OrbitFabric:orbitfabric/openc3-cosmos \
  --version 0.2.0 \
  --output-dir ./resolved-cosmos
```

The command:

```text
loads the local Catalog with the Core AdapterCatalog model
    -> performs Core exact Source Coordinate + version selection
    -> resolves through GitHubReleaseSource
    -> verifies descriptor identity and digest
    -> verifies descriptor-owned artifact size and SHA-256
    -> materializes verified descriptor/artifact bytes
```

If `GITHUB_TOKEN` is present in the environment it is used for GitHub requests. No token is required for normal public release access when unauthenticated GitHub limits are sufficient.

### Ensure one Project Lock entry

```bash
orbitfabric-github-release-source ensure \
  path/to/catalog.json \
  path/to/adapter-lock.json \
  github.com/OrbitFabric:orbitfabric/openc3-cosmos
```

`ensure` is deliberately Project-Lock-driven. It does **not** accept a second `--version` input because the Project Lock already owns the exact desired release version.

The ordering is:

```text
load Project Lock
    -> inspect current Installed Adapter State

if MATCH
    -> NOOP
    -> do not read Catalog
    -> do not contact GitHub

otherwise
    -> load local Catalog
    -> Core exact selection using the locked version
    -> verify Catalog descriptor digest agrees with Project Lock
    -> resolve through GitHubReleaseSource
    -> hand ResolvedAdapterRelease to Core ProjectLockInstallService
    -> require final MATCH
    -> remove temporary acquisition workspace
```

This preserves the important property that an already-satisfied project does not depend on Catalog or provider availability merely to remain satisfied.

## Core application composition

Core 1.4.0 calls this package's existing resolver and passes the resulting `ResolvedAdapterRelease` to `AdapterManager.install_resolved`. GitHub transport stays in this package; lifecycle semantics stay in Core. No generic provider protocol is introduced.

The coordinated product installation uses exact wheel assets and the SHA-256-bound `orbitfabric-install.txt` manifest from published [Core v1.4.0](https://github.com/OrbitFabric/orbitfabric/releases/tag/v1.4.0). This GitHub Release Source [v0.1.0](https://github.com/OrbitFabric/orbitfabric-github-release-source/releases/tag/v0.1.0) was published on 2026-09-25; no PyPI publication is assumed. The published product does not promote Core-owned adapter lifecycle or integration contracts from candidate to stable. See [public adapter onboarding](https://github.com/OrbitFabric/orbitfabric/blob/main/docs/reference/public-adapter-onboarding.md) for the consumer install path.

## Development baseline

OrbitFabric Core `v1.3.0` introduced the provider-neutral Catalog API and CLI required by this product. The coordinated published consumer onboarding baseline is Core `v1.4.0`.

The repository's pinned development/CI integration baseline remains the exact Core commit:

```text
714403034b49b7b7c67fcf42ab2c14feff79295f
```

The package dependency is:

```text
orbitfabric>=1.3,<2
```

Core `1.2.x` does not contain the Catalog API or consumer CLI used by this product.

## Published release status

**Published GitHub Release:** [`v0.1.0`](https://github.com/OrbitFabric/orbitfabric-github-release-source/releases/tag/v0.1.0) (2026-09-25). The released package version remains `0.1.0`.

The release provides:

```text
orbitfabric_github_release_source-0.1.0-py3-none-any.whl
orbitfabric_github_release_source-0.1.0.tar.gz
SHA256SUMS
```

The release workflow built the wheel and sdist, downloaded the published artifacts and checked their bytes against its local builds before marking the GitHub Release public. CI and provider-explicit Consumer CLI E2E were successful at the release commit; the latter preserves historical FAROTECH release identity as an intentional regression lane.

This confirms product publication and the stated CI evidence, not an independent post-publication clean-environment replay of the coordinated Core v1.4.0 + provider + F Prime v0.1.3 onboarding path. The Core-owned lifecycle and integration contracts retain candidate maturity. No universal Release Source provider protocol is defined by this repository.

See [0.1.0 release notes](docs/releases/0.1.0.md) and [Architecture and Ownership](docs/architecture-and-ownership.md).
