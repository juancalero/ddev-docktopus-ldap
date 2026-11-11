[![tests](https://github.com/juancalero/ddev-docktopus-ldap/actions/workflows/tests.yml/badge.svg?branch=main)](https://github.com/juancalero/ddev-docktopus-ldap/actions/workflows/tests.yml)
[![release](https://img.shields.io/github/v/release/juancalero/ddev-docktopus-ldap)](https://github.com/juancalero/ddev-docktopus-ldap/releases/latest)

# DDEV Docktopus LDAP

[OpenLDAP](https://www.openldap.org/) directory service for DDEV, taken from the [docktopus](https://github.com/juancalero/docktopus) stack, for Drupal sites that authenticate against a corporate directory with the [`ldap`](https://www.drupal.org/project/ldap) module.

The add-on also adds the PHP `ldap` extension to the web image (for the project's configured PHP version), so `ddev restart` will rebuild the web container once.

> Part of [ddev-docktopus](https://github.com/juancalero/ddev-docktopus) — the meta add-on that installs the whole docktopus stack with one command.

## Installation

```
ddev add-on get juancalero/ddev-docktopus-ldap
ddev restart
```

## Configuration

All settings live in `.ddev/.env.ddev-docktopus-ldap`:

| Variable | Default | Description |
| --- | --- | --- |
| `LDAP_ORGANISATION` | `MiEmpresa` | Organisation name shown in the directory |
| `LDAP_DOMAIN` | `miempresa.local` | Base DN becomes `dc=miempresa,dc=local` |
| `LDAP_ADMIN_PASSWORD` | `admin` | Password for the admin bind |
| `LDAP_HOST_PORT` | `10389` | Host port for external LDAP clients |

Change any value and run `ddev restart`. To change the directory contents after the first start, wipe the volumes: `ddev delete` (or remove the `ddev-<project>_ldap_*` docker volumes).

## Access

| From | URL / DSN |
| --- | --- |
| Drupal (web container) | `ldap://openldap:389` |
| Host (external clients) | `ldap://localhost:10389` |
| Bind DN | `cn=admin,dc=miempresa,dc=local` |
| Password | `admin` |

For a visual manager install [`ddev-docktopus-phpldapadmin`](https://github.com/juancalero/ddev-docktopus-phpldapadmin).

## Drupal configuration

1. Install the module: `ddev composer require drupal/ldap` (or add it to `composer.json`).
2. Enable `ldap_servers` and `ldap_authentication` at `/admin/modules`.
3. At `/admin/config/people/ldap` add a server:
   - Host: `openldap`, port `389`, protocol `ldap` (no TLS inside the Docker network).
   - Bind DN: `cn=admin,dc=miempresa,dc=local`, password `admin`.
   - Base DN: `dc=miempresa,dc=local`.

## Uninstallation

```
ddev add-on remove ddev-docktopus-ldap
ddev restart
```

The `web-build/Dockerfile.ddev-docktopus-ldap` file is removed with the add-on; run `ddev restart` afterwards to rebuild the image without the PHP extension. The LDAP data volumes are not deleted automatically.

## License

GPL-2.0-or-later
