# WORKFLOW

## Ergebnis der Machbarkeitsprüfung

Eine automatische Übertragung von Dateien aus diesem GitHub-Repository auf einen cPanel-Server per **FTPS** ist **technisch möglich**.

Wichtig dabei:

- GitHub selbst stellt keine direkte Serververbindung her.
- Die Übertragung muss über einen **Deploy-Job** erfolgen, z. B. mit **GitHub Actions**.
- Zugangsdaten dürfen **nicht** im Repository gespeichert werden, sondern nur als **GitHub Secrets**.
- Der cPanel-Server muss FTPS erlauben und aus dem Internet erreichbar sein.
- Das FTP-Ziel ist in diesem Fall das FTP-Root selbst, also `server-dir: /`.
- Das kombinierte Problem aus diesem Fall ist: Das Login war korrekt auf das FTP-Root gesetzt, aber der 530-Fehler zeigt eindeutig, dass die echten GitHub Secrets (`FTP_HOST`, `FTP_USER`, `FTP_PASSWORD` und ggf. `FTP_PORT`) nicht korrekt oder nicht vorhanden sind.

## Gegebener Serverstatus

Der FTP-Login landet bereits direkt hier:

- `/home/web1819/public_html/index/app/neutral`

Aus Sicht der FTP-Verbindung ist das FTP-Root:

- `/`

Daher ist der Workflow exakt so konfiguriert:

- `server-dir: /`

Keine zusätzlichen Ordner wie `public_html`, `neutral`, `home`, `web1819`, `index` oder `app` dürfen durch den Workflow eingefügt werden.

## Voraussetzungen

### Auf dem cPanel-Server

- FTPS-Dienst ist aktiv.
- Ein FTP-Benutzer mit Zugriff auf das gewünschte FTP-Root existiert.
- Passive FTPS-Verbindungen sind erlaubt.
- Die Firewall lässt Port 21 bzw. den FTPS-Port zu.

### In GitHub

- Repository ist mit GitHub Actions nutzbar.
- Secrets sind hinterlegt:
  - `FTP_HOST`
  - `FTP_USER`
  - `FTP_PASSWORD`
  - optional `FTP_PORT` (Standard: `21`)

## Empfohlener Workflow

### 1. Deployment-Datei anlegen

Erstelle im Repository eine GitHub-Action, die bei jedem Push auf `main` startet.

### 2. Verbindung aufbauen

Die Action verbindet sich per FTPS mit dem cPanel-Server und verwendet dabei das FTP-Root direkt.

### 3. Dateien übertragen

Die Projektdateien werden in das FTP-Root hochgeladen.

### 4. Verifikation

Nach dem Upload muss geprüft werden:

- sind die Dateien vollständig angekommen,
- stimmen Rechte und Pfade,
- ist das FTP-Root richtig gesetzt.

## Technische Diagnose zum aktuellen Fehler

Der Fehler:

- `FTPError: 530 Login authentication failed`

bedeutet in diesem Fall nicht, dass der Pfad falsch ist. Es ist ein reiner Login-/Authentifizierungsfehler.

Die wahrscheinlichen Ursachen sind:

1. `FTP_USER` ist falsch.
2. `FTP_PASSWORD` ist falsch.
3. `FTP_HOST` ist falsch.
4. `FTP_PORT` stimmt nicht zum FTPS-Server.
5. Das Secret wurde im Repository nicht korrekt hinterlegt.

Der Workflow selbst bleibt auf dem korrekten Pfad `server-dir: /` und nutzt FTPS. Deshalb sollte bei einem 530-Fehler zuerst genau das Secret in GitHub geprüft werden.

## Praktisch zu prüfende GitHub Secrets

Bitte in GitHub prüfen:

- `FTP_HOST`
- `FTP_USER`
- `FTP_PASSWORD`
- `FTP_PORT`

Wenn eines davon falsch oder leer ist, schlägt der Login mit `530 Login authentication failed` fehl.

## Fazit

Die Workflow-Konfiguration ist jetzt auf den gültigen FTP-Root-Satz `server-dir: /` und FTPS abgestimmt. Wenn der Fehler weiterhin auftritt, liegt er eindeutig an fehlerhaften oder fehlenden GitHub Secrets, nicht an der Repo-Strategie.
