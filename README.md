# Docker Image for Odoo 16, 17, 18 and 19

> Odoo is an open source ERP and CRM platform, formerly known as OpenERP, that can connect a wide variety of business operations such as sales, supply chain, finance, and project management.

[Overview of Odoo](https://www.odoo.com/)

These images are built from the [Bitnami Odoo container recipes](https://github.com/bitnami/containers/tree/main/bitnami/odoo), which is why the filesystem and environment variable behavior match the familiar Bitnami images. Bitnami dropped Odoo 16 and 17 from its catalog in 2025 and now maintains Odoo 19 only; this repository keeps 16/17/18 buildable by sourcing those releases from the official Odoo nightly builds and keeps 19 on the last official Bitnami release.

## Published images

All tags are published to `ghcr.io/kaozaza2/bitnami-odoo`. Each major version is published with three aliases:

| Alias | Example | Immutable |
|-------|---------|-----------|
| `X` (rolling)      | `ghcr.io/kaozaza2/bitnami-odoo:16`  | no, tracks latest nightly for the series |
| `X.0` (rolling)    | `ghcr.io/kaozaza2/bitnami-odoo:16.0` | no, tracks latest nightly for the series |
| `X.0.<date>` (pin) | `ghcr.io/kaozaza2/bitnami-odoo:16.0.20260831` | yes, pinned to that nightly build |

| Version | Odoo source                                    | Pinned build tag          |
|---------|------------------------------------------------|---------------------------|
| 16      | nightly.odoo.com `16.0` tgz, checksum-verified | `16.0.20260831`           |
| 17      | nightly.odoo.com `17.0` tgz, checksum-verified | `17.0.20260831`           |
| 18      | nightly.odoo.com `18.0` tgz, checksum-verified | `18.0.20260830`           |
| 19      | last supported Bitnami release                 | `19.0.20260801`           |

The nightly tarballs are pinned by date (not the moving `latest` file), downloaded and verified by their SHA-256 during the Docker build, then applied on top of the Bitnami scaffold. Bumping a version means editing `ENV APP_VERSION` and the pinned checksum in the `Dockerfile` — the [CI workflow](.github/workflows/build-ghcr.yaml) derives the tags from that value, so the workflow file never needs to change.

## TL;DR

```console
docker run --name odoo -p 8069:8069 ghcr.io/kaozaza2/bitnami-odoo:18
```

Odoo requires PostgreSQL. The recommended way is with Docker Compose:

```console
docker compose up -d
```

This uses the root `docker-compose.yml` (PostgreSQL + Odoo 19). Per-version compose files live in `16/debian-12/`, `17/debian-12/`, `18/debian-12/` and `19/debian-12/`.

## How to use this image

Odoo needs a PostgreSQL database. Start one and connect the Odoo container:

```console
# PostgreSQL
docker run -d --name odoo-postgresql \
  -e POSTGRESQL_USERNAME=bn_odoo \
  -e POSTGRESQL_PASSWORD=bitnami \
  -e POSTGRESQL_DATABASE=bitnami_odoo \
  docker.io/bitnami/postgresql:16

# Odoo 18
docker run -it --rm \
  --name odoo \
  --link odoo-postgresql:postgresql \
  -p 8069:8069 \
  -e ODOO_DATABASE_HOST=postgresql \
  -e ODOO_DATABASE_PORT_NUMBER=5432 \
  -e ODOO_DATABASE_USER=bn_odoo \
  -e ODOO_DATABASE_PASSWORD=bitnami \
  -e ODOO_DATABASE_NAME=bitnami_odoo \
  ghcr.io/kaozaza2/bitnami-odoo:18
```

Open `http://localhost:8069` and use the admin credentials `admin` / `bitnami`.

### Secrets, `*_FILE` variables

Every configurable environment variable has a `*_FILE` companion as defined in `odoo-env.sh`: point it at a readable file and its contents are used as the value at container start (useful with Docker secrets and Kubernetes secret volumes).

```console
docker run --rm -it \
  -v /run/secrets/odoo-pass:/run/secrets/odoo-pass:ro \
  -e ODOO_DATABASE_PASSWORD_FILE=/run/secrets/odoo-pass \
  ghcr.io/kaozaza2/bitnami-odoo:18
```

Supported `*_FILE` variables include `ODOO_PASSWORD_FILE`, `ODOO_DATABASE_HOST_FILE`, `ODOO_DATABASE_PORT_NUMBER_FILE`, `ODOO_DATABASE_NAME_FILE`, `ODOO_DATABASE_USER_FILE`, `ODOO_DATABASE_PASSWORD_FILE`, `ODOO_SMTP_HOST_FILE`, `ODOO_SMTP_PORT_NUMBER_FILE`, `ODOO_SMTP_USER_FILE`, `ODOO_SMTP_PASSWORD_FILE`, and the corresponding `POSTGRESQL_*_FILE` variables.

## Persisting your application

Mount a volume at `/bitnami/odoo` to persist Odoo data, and persist PostgreSQL separately:

```yaml
services:
  postgresql:
    image: docker.io/bitnami/postgresql:16
    volumes:
      - postgresql_data:/bitnami/postgresql
  odoo:
    image: ghcr.io/kaozaza2/bitnami-odoo:18
    volumes:
      - odoo_data:/bitnami/odoo
volumes:
  postgresql_data:
  odoo_data:
```

> **NOTE** As this is a non-root container, the mounted files and directories must have the proper permissions for the UID `1001`.

## Environment variables

The following tables list the main variables you can set.

### Customizable environment variables

| Name                           | Description                                                                                                                | Default Value                                          |
|--------------------------------|----------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------|
| `ODOO_DATA_TO_PERSIST`         | Files to persist relative to the Odoo installation directory. To provide multiple values, separate them with a whitespace. | `${ODOO_ADDONS_DIR} ${ODOO_CONF_DIR} ${ODOO_DATA_DIR}` |
| `ODOO_PORT_NUMBER`             | Port number in which Odoo will run.                                                                                        | `8069`                                                 |
| `ODOO_LONGPOLLING_PORT_NUMBER` | Port number in which the Odoo Longpolling service will run.                                                                | `8072`                                                 |
| `ODOO_SKIP_BOOTSTRAP`          | Whether to perform initial bootstrapping for the application.                                                              | `no`                                                   |
| `ODOO_SKIP_MODULES_UPDATE`     | Whether to perform initial update of the modules installed.                                                                | `no`                                                   |
| `ODOO_LOAD_DEMO_DATA`          | Whether to load demo data.                                                                                                 | `no`                                                   |
| `ODOO_LIST_DB`                 | Whether the database selector is available.                                                                                | `no`                                                   |
| `ODOO_EMAIL`                   | Odoo user e-mail address.                                                                                                  | `user@example.com`                                     |
| `ODOO_PASSWORD`                | Odoo user password.                                                                                                        | `nil`                                                  |
| `ODOO_SMTP_HOST`               | Odoo SMTP server host.                                                                                                     | `nil`                                                  |
| `ODOO_SMTP_PORT_NUMBER`        | Odoo SMTP server port number.                                                                                              | `nil`                                                  |
| `ODOO_SMTP_USER`               | Odoo SMTP server user.                                                                                                     | `nil`                                                  |
| `ODOO_SMTP_PASSWORD`           | Odoo SMTP server user password.                                                                                            | `nil`                                                  |
| `ODOO_SMTP_PROTOCOL`           | Odoo SMTP server protocol to use.                                                                                          | `nil`                                                  |
| `ODOO_DATABASE_HOST`           | Database server host.                                                                                                      | `postgresql`                                           |
| `ODOO_DATABASE_PORT_NUMBER`    | Database server port.                                                                                                      | `5432`                                                 |
| `ODOO_DATABASE_NAME`           | Database name.                                                                                                             | `bitnami_odoo`                                         |
| `ODOO_DATABASE_USER`           | Database user name.                                                                                                        | `bn_odoo`                                              |
| `ODOO_DATABASE_PASSWORD`       | Database user password.                                                                                                    | `nil`                                                  |
| `ODOO_DATABASE_FILTER`         | Database filter.                                                                                                           | `nil`                                                  |

### Read-only environment variables

| Name                         | Description                                     | Value                                         |
|------------------------------|-------------------------------------------------|-----------------------------------------------|
| `ODOO_BASE_DIR`              | Odoo installation directory.                    | `${BITNAMI_ROOT_DIR}/odoo`                    |
| `ODOO_BIN_DIR`               | Odoo directory for binary executables.          | `${ODOO_BASE_DIR}/bin`                        |
| `ODOO_CONF_DIR`              | Odoo directory for configuration files.         | `${ODOO_BASE_DIR}/conf`                       |
| `ODOO_CONF_FILE`             | Configuration file for Odoo.                    | `${ODOO_CONF_DIR}/odoo.conf`                  |
| `ODOO_DATA_DIR`              | Odoo directory for data files.                  | `${ODOO_BASE_DIR}/data`                       |
| `ODOO_ADDONS_DIR`            | Odoo directory for extra addons.                | `${ODOO_ADDONS_DIR:-${ODOO_BASE_DIR}/addons}` |
| `ODOO_TMP_DIR`               | Odoo directory for temporary files.             | `${ODOO_BASE_DIR}/tmp`                        |
| `ODOO_PID_FILE`              | PID file for Odoo.                              | `${ODOO_TMP_DIR}/odoo.pid`                    |
| `ODOO_LOGS_DIR`              | Odoo directory for log files.                   | `${ODOO_BASE_DIR}/log`                        |
| `ODOO_LOG_FILE`              | Log file for Odoo.                              | `${ODOO_LOGS_DIR}/odoo-server.log`            |
| `ODOO_VOLUME_DIR`            | Odoo directory for mounted configuration files. | `${BITNAMI_VOLUME_DIR}/odoo`                  |

### Connecting to an existing database

If the database already contains data from a previous Odoo installation, set `ODOO_SKIP_BOOTSTRAP` to `yes` so the container skips the installation wizard. With `ODOO_SKIP_BOOTSTRAP=yes`, variables such as `ODOO_EMAIL` and `ODOO_PASSWORD` are ignored.

## Logging

The container sends its logs to `stdout`. Use the Docker `--log-driver` option to change the logging driver.

## Building locally

```console
docker build -t ghcr.io/kaozaza2/bitnami-odoo:16 16/debian-12
docker build -t ghcr.io/kaozaza2/bitnami-odoo:17 17/debian-12
docker build -t ghcr.io/kaozaza2/bitnami-odoo:18 18/debian-12
docker build -t ghcr.io/kaozaza2/bitnami-odoo:19 19/debian-12
```

The Docker builds require outbound access to the Bitnami downloads proxy (scaffold), `nightly.odoo.com` (16/17/18 sources) and PyPI (dependency sync).

## License

Copyright &copy; 2026 kaozaza2.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

<http://www.apache.org/licenses/LICENSE-2.0>

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.