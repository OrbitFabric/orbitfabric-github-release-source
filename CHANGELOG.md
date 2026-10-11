# Changelog

## Unreleased

## 0.1.0 - 2026-09-25

### Added

- establish the first GitHub Releases source product for OrbitFabric adapter acquisition;
- consume Core exact Catalog release selections;
- verify Release Descriptor and artifact identity fail-closed;
- preserve GitHub provider facts separately from OrbitFabric trust/acceptance semantics;
- add the provider-explicit `orbitfabric-github-release-source resolve` command for exact Catalog-selected GitHub release materialization;
- add Project-Lock-driven `orbitfabric-github-release-source ensure`, including `MATCH -> NOOP` before Catalog/provider access and temporary acquisition cleanup after installation;
- validate the consumer CLI against the provider-neutral Core Catalog CLI baseline without defining a generic provider dispatch protocol.

### Publication

- published GitHub Release `v0.1.0` with the wheel, source distribution and `SHA256SUMS`;
- confirmed publication bytes through the release workflow's download-and-compare step;
- kept Core-owned adapter lifecycle and integration contracts at candidate maturity; no separate post-publication greenfield E2E acceptance is asserted.
