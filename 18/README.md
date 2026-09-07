# Bitnami Docker Image for Odoo 18 (restored branch)

> Restored: Bitnami switched its free catalog to Odoo 19 only (October 2025). This branch was re-added from the last official Bitnami release so it can be built and maintained independently.

- Version: `18.0.20260907` (Odoo sourced from the nightly build at `nightly.odoo.com/18.0/nightly/tgz`, pinned by date and checksum-verified at build time)
- Base OS: Debian 12 (`debian-12`)
- Container image: `ghcr.io/kaozaza2/bitnami-odoo:18`

## Usage

```console
docker run --name odoo18 -p 8080:8069 ghcr.io/kaozaza2/bitnami-odoo:18
```

Or with the compose file provided in `debian-12/`:

```console
docker compose up -d
```

## Building

```console
docker build -t ghcr.io/kaozaza2/bitnami-odoo:18 18/debian-12
```

Refer to the [Odoo image documentation](../README.md) for details on proper usage, persistence, and configuration.