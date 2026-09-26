# Bitnami Docker Image for Odoo 16 (restored branch)

> Restored: Bitnami removed Odoo 16 from its maintained catalog in October 2025. This branch was re-added from the last official Bitnami release so it can be built and maintained independently.

- Version: `16.0.20260926` (Odoo sourced from the nightly build at `nightly.odoo.com/16.0/nightly/tgz`, pinned by date and checksum-verified at build time)
- Base OS: Debian 12 (`debian-12`)
- Container image: `ghcr.io/kaozaza2/bitnami-odoo:16`

## Usage

```console
docker run --name odoo16 -p 8080:8069 ghcr.io/kaozaza2/bitnami-odoo:16
```

Or with the compose file provided in `debian-12/`:

```console
docker compose up -d
```

## Building

```console
docker build -t ghcr.io/kaozaza2/bitnami-odoo:16 16/debian-12
```

Refer to the [Odoo image documentation](../README.md) for details on proper usage, persistence, and configuration.