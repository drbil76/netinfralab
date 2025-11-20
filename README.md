# NetInfraLab – Hébergement local + mini stack Docker

_Petit labo d’infra pour tests locaux : DNS/FTP/Web/Mail sur un domaine fictif `bdraa.local`, plus une appli PHP+MariaDB en Docker qui liste des villes._

 **Les mots de passe de démo (mdp.txt) sont en clair (usage labo uniquement), à remplacer pour tout déploiement réel.**

## Aperçu
- **Domaine local** : `bdraa.local`
- **Services** : DNS (BIND), FTP (ProFTPD), Web (Apache), Mail (Postfix/Cyrus), enregistrements SPF/DKIM.
- **Stack Docker** : PHP 8.2 Apache + MariaDB, données seedées via init SQL.

## Arborescence
- `etc/` : configs (BIND, ProFTPD, Apache, Postfix, Cyrus).
  - DNS : `etc/named.conf`, zone `var/named/bdraa/bdraa.local.zone`
  - FTP : `etc/proftpd.conf` (admin + anonyme)
  - Web : `etc/httpd.conf` (`/local` restreint au LAN 192.168.0.0/16)
  - Mail : `etc/postfix/main.cf`, `etc/imapd.conf`
- `var/` : données/tests (logs, zones).
- `mdp.txt` : credentials FTP admin (`FTPadmin:bilal2025`).
- `tp3-537160958/` : stack Docker (PHP/MariaDB).

## DNS
- NS : `ns1.bdraa.local` (10.10.11.1), `ns2.bdraa.local` (10.10.11.2)
- A : `www`, `ftp`, `reception`, `envoi` → 10.10.11.3
- TXT SPF : `v=spf1 mx a envoi.bdraa.local -all`
- TXT DKIM : `mail._domainkey` (clé RSA)

## FTP (ProFTPD)
- Admin : `FTPadmin / bilal2025` (accès complet)
- Anonyme : lecture `/ftp`, écriture `/ftp/depot`, max 3 connexions.

## Web (Apache)
- DocumentRoot : `/var/www/html`
- `/local` accessible seulement depuis `192.168.0.0/16`.

## Mail (Postfix + Cyrus)
- `myhostname`: `envoi.bdraa.local`
- `mydomain`: `bdraa.local`
- IMAP via Cyrus (`admins: cyrus`)
- SPF/DKIM déjà publiés dans la zone.

## Stack Docker (TP3)
Chemin : `tp3-537160958/`

Contenu :
- `docker-compose.yml` : `apache` (PHP 8.2 Apache, port 8888:80) + `bd` (MariaDB).
- `src/Dockerfile` : extension `pdo_mysql`.
- `src/initdb/init.sql` : base `bdraa`, table `ville` avec 5 entrées.
- `src/villes.php` : tableau HTML des villes.

Lancement :
```bash
cd tp3-537160958
docker-compose up --build
# puis http://localhost:8888/villes.php
