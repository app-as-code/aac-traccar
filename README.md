<p align="center">
  <picture>
    <source media="(prefers-color-scheme: dark)" srcset="assets/icon-dark.svg">
    <source media="(prefers-color-scheme: light)" srcset="assets/icon-light.svg">
    <img src="assets/icon.svg" alt="Traccar logo" width="128">
  </picture>
</p>

<h1 align="center">aac-traccar</h1>

<p align="center">Self-hosted deployment definition for Traccar, an open source GPS tracking platform.</p>

<p align="center">
  <a href="https://github.com/traccar/traccar"><img alt="Upstream" src="https://img.shields.io/badge/upstream-traccar%2Ftraccar-blue?logo=github"></a>
  <img alt="Image" src="https://img.shields.io/badge/image-traccar%2Ftraccar%3A6.16-2496ED?logo=docker&logoColor=white">
  <a href="https://github.com/traccar/traccar/blob/master/LICENSE.txt"><img alt="License" src="https://img.shields.io/badge/license-Apache--2.0-green"></a>
</p>

## About

Traccar is a free, open source GPS tracking platform. Its Java-based back-end server supports
a large number of GPS protocols and tracking device models and stores its data in a SQL
database. A web dashboard, a REST API and mobile apps are available on top of the server, so
vehicles, assets and people can be tracked from one place. Traccar can be self-hosted for
free.

Key features (as provided by upstream):

- Real-time GPS tracking
- Driver behaviour monitoring
- Detailed and summary reports
- Geofencing
- Alarms and notifications
- Account and device management
- E-mail and SMS notifications

## Upstream

- Project: <https://github.com/traccar/traccar>
- Website: <https://www.traccar.org>
- Container image: `traccar/traccar:6.16` (Docker Hub)
- Database: `mariadb:12.3.1-ubi10-rc` (deployed as `aac-traccar-db`)
- License: Apache License 2.0 (upstream project)

## Quick start (Docker Compose)

The Compose file is rendered from `service.yml` by the App-as-Code template engine. For a
manual run, the external network used for Traefik routing must exist first:

```bash
docker network create services-secured
docker network create services-exposed

# Provide the values listed under "Configuration" in .env / stack.env
docker compose up -d
```

Secrets are not stored in this repository. Provide them through the deployment pipeline
or create the `.env` and `stack.env` files yourself (see below). Do not commit them.

## Configuration

Environment variables (defaults from `service.yml`; `CHANGE_ME` must be set per deployment):

| Variable | Default | Description |
|---|---|---|
| `TRACCAR_DB_DRIVER` | `CHANGE_ME` | Database driver used by Traccar |
| `TRACCAR_DB_URL` | `CHANGE_ME` | JDBC URL of the database, pointing at the `aac-traccar-db` dependency |
| `TRACCAR_DB_USER` | `traccar` | Database user Traccar connects as |
| `TRACCAR_DB_PASSWORD` | `CHANGE_ME` (secret) | Database password for `TRACCAR_DB_USER` |
| `MARIADB_DATABASE` | `traccar` | Database created by the MariaDB dependency |
| `MARIADB_USER` | `traccar` | Database user created by the MariaDB dependency |
| `MARIADB_PASSWORD` | `CHANGE_ME` (secret) | Password for `MARIADB_USER` |
| `MARIADB_ROOT_PASSWORD` | `CHANGE_ME` (secret) | Root password of the MariaDB dependency |
| `LOG_LEVEL` | `info` | Log level of the application |
| `PUID` / `PGID` | `1000` | User and group ID the container runs as |
| `TZ` | `Europe/Berlin` | Time zone |

Ports:

| Container port | Published port | Protocol | Purpose |
|---|---|---|---|
| `8082` | `8882` | TCP | Web interface and API (`web`) |

Volumes:

| Mount | Description |
|---|---|
| `data` -> `/opt/traccar/data` | Traccar application data |
| `logs` -> `/opt/traccar/logs` | Traccar log files |
| `db` -> `/var/lib/mysql` | MariaDB data (database dependency) |

## Deploy with App-as-Code

1. Edit `service.yml`. It is the single source of truth for image, ports, environment,
   volumes, dependencies and per-stage overrides. Do not edit the rendered output.
2. Pushing to `main` (or a configured stage branch) runs the service pipeline, which renders
   the manifests with `aac-template-engine` and triggers the deployment.
3. Releases of this definition are published under <https://github.com/app-as-code/aac-traccar>.

## Credits and license

Traccar is developed by the Traccar project and licensed under the Apache License 2.0. This
repository only contains the deployment definition for it and is not a fork of the
application. The logo is provided by the Traccar project; see [`assets/ICON_SOURCE.md`](assets/ICON_SOURCE.md)
for icon sources and licenses.
