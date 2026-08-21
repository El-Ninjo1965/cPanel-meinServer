# cPanel-meinServer

Dieses Repository enthält den GitHub-Actions-Deploy-Workflow für den cPanel-FTP-Upload.

## Ziel

Der Upload muss direkt in das FTP-Root erfolgen, nicht in einen zusätzlichen Unterordner.

Der bekannte cPanel-FTP-Root liegt hier:

- `/home/web1819/public_html/index/app/neutral`

Aus Sicht der FTP-Verbindung ist das FTP-Root also:

- `/`

Daher ist im Workflow exakt:

- `server-dir: /`

## Benötigte GitHub Secrets

Im GitHub-Repository müssen diese Secrets hinterlegt sein:

- `FTP_HOST` – Hostname oder IP des cPanel-Servers
- `FTP_USER` – FTP-Benutzer für den FTP-Root-Zugriff
- `FTP_PASSWORD` – Passwort des FTP-Benutzers
- `FTP_PORT` – optional, Standard ist `21`

Es werden keine zusätzlichen Secrets benötigt.

## Wichtige Diagnostik zum 530-Fehler

Ein `530 Login authentication failed` bedeutet normalerweise:

- falscher `FTP_USER`
- falsches `FTP_PASSWORD`
- falscher `FTP_HOST`
- falscher Port oder falsch konfiguriertes FTPS-Protokoll

Der Pfad `server-dir: /` ist hier bewusst korrekt und darf nicht durch `public_html` oder andere Unterpfade ersetzt werden.

## Transmit / FTP-Client

Wenn du den FTP-Login manuell mit Transmit prüfst, nutze dieselben Werte:

- Host: `FTP_HOST`
- Benutzer: `FTP_USER`
- Passwort: `FTP_PASSWORD`
- Port: `FTP_PORT` (normalerweise `21` für FTPS)
- Protokoll: FTPS
- Ordner: `/`

## Workflow-Datei

Die komplette Konfiguration liegt in [.github/workflows/ftp-upload.yml](.github/workflows/ftp-upload.yml).
