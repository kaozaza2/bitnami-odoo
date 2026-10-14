# Bitnami Docker Image for Odoo 17 (restored branch)

> Restored: Bitnami removed Odoo 17 from its maintained catalog in 2025. This branch was re-added from the last official Bitnami release so it can be built and maintained independently.

- Version: `17.0.20261014` (Odoo sourced from the nightly build at `nightly.odoo.com/17.0/nightly/tgz`, pinned by date and checksum-verified at build time)
- Base OS: Debian 12 (`debian-12`)
- Container image: `ghcr.io/kaozaza2/bitnami-odoo:17`

## Usage

```console
docker run --name odoo17 -p 8080:8069 ghcr.io/kaozaza2/bitnami-odoo:17
```

Or with the compose file provided in `debian-12/`:

```console
docker compose up -d
```

## Building

```console
docker build -t ghcr.io/kaozaza2/bitnami-odoo:17 17/debian-12
```

Refer to the [Odoo image documentation](../README.md) for details on proper usage, persistence, and configuration.