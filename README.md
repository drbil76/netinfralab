# NetInfraLab – Local hosting + mini Docker stack

_Small infra lab for local tests: DNS/FTP/Web/Mail on a fictitious domain `bdraa.local`, plus a PHP+MariaDB Docker app that lists cities._

**Demo passwords (mdp.txt) are in cleartext (lab use only); replace them for any real deployment.**

## Overview
- **Local domain**: `bdraa.local`
- **Services**: DNS (BIND), FTP (ProFTPD), Web (Apache), Mail (Postfix/Cyrus), SPF/DKIM records.
- **Docker stack**: PHP 8.2 Apache + MariaDB, data seeded via init SQL.

## Structure
- `etc/`: service configs (BIND, ProFTPD, Apache, Postfix, Cyrus).
  - DNS: `etc/named.conf`, zone `var/named/bdraa/bdraa.local.zone`
  - FTP: `etc/proftpd.conf` (admin + anonymous)
  - Web: `etc/httpd.conf` (`/local` limited to LAN 192.168.0.0/16)
  - Mail: `etc/postfix/main.cf`, `etc/imapd.conf`
- `var/`: data/tests (logs, zones).
- `mdp.txt`: FTP admin credentials (`FTPadmin:bilal2025`).
- `tp3-537160958/`: Docker stack (PHP/MariaDB).

## DNS
- NS: `ns1.bdraa.local` (10.10.11.1), `ns2.bdraa.local` (10.10.11.2)
- A: `www`, `ftp`, `reception`, `envoi` → 10.10.11.3
- TXT SPF: `v=spf1 mx a envoi.bdraa.local -all`
- TXT DKIM: `mail._domainkey` (RSA key)

## FTP (ProFTPD)
- Admin: `FTPadmin / bilal2025` (full access)
- Anonymous: read `/ftp`, write `/ftp/depot`, max 3 connections.
- **mdp.txt**: provided for lab/evaluation, not for production use.

## Web (Apache)
- DocumentRoot: `/var/www/html`
- `/local` accessible only from `192.168.0.0/16`.

## Mail (Postfix + Cyrus)
- `myhostname`: `envoi.bdraa.local`
- `mydomain`: `bdraa.local`
- IMAP via Cyrus (`admins: cyrus`)
- SPF/DKIM already published in the DNS zone.

## Docker stack
Path: `tp3-537160958/`

Contents:
- `docker-compose.yml`: `apache` (PHP 8.2 Apache, port 8888:80) + `bd` (MariaDB).
- `src/Dockerfile`: `pdo_mysql` extension.
- `src/initdb/init.sql`: database `bdraa`, table `ville` with 5 entries.
- `src/villes.php`: HTML table of cities.

Run:
```bash
cd tp3-537160958
docker-compose up --build
# then open http://localhost:8888/villes.php
