# WORKFLOW

## Ergebnis der Machbarkeitsprüfung

Eine automatische Übertragung von Dateien aus diesem GitHub-Repository auf einen cPanel-Server per **FTP/FTPS** ist **technisch möglich**.

Wichtig dabei:

- GitHub selbst stellt keine direkte Serververbindung her.
- Die Übertragung muss über einen **Deploy-Job** erfolgen, z. B. mit **GitHub Actions**.
- Zugangsdaten dürfen **nicht** im Repository gespeichert werden, sondern nur als **GitHub Secrets**.
- Der cPanel-Server muss FTP oder FTPS erlauben und aus dem Internet erreichbar sein.

## Voraussetzungen

### Auf dem cPanel-Server

- FTP-Dienst oder FTPS-Dienst ist aktiv.
- Ein Deployment-Benutzer existiert.
- Das Zielverzeichnis ist eindeutig festgelegt.
- Passive FTP-Verbindungen sind erlaubt.
- Die Firewall lässt die nötigen Ports zu.

### In GitHub

- Repository ist mit GitHub Actions nutzbar.
- Secrets sind hinterlegt:
  - `FTP_HOST`
  - `FTP_USER`
  - `FTP_PASSWORD`
  - optional `FTP_PORT`
  - optional `FTP_REMOTE_DIR`
  - optional `FTP_TLS`

## Empfohlener Workflow

### 1. Deployment-Datei anlegen

Erstelle im Repository eine GitHub-Action, die bei jedem Push auf `main` startet.

### 2. Verbindung aufbauen

Die Action verbindet sich per FTP oder FTPS mit dem cPanel-Server.

### 3. Dateien übertragen

Nur die gewünschten Projektdateien werden in das Zielverzeichnis hochgeladen.

### 4. Verifikation

Nach dem Upload muss geprüft werden:

- sind die Dateien vollständig angekommen,
- stimmen Rechte und Pfade,
- ist die Anwendung im Browser erreichbar.

## Technisch notwendige Schritte im Detail

1. In cPanel einen FTP-Benutzer anlegen.
2. Das Zielverzeichnis für das Deployment festlegen.
3. Prüfen, ob FTPS verfügbar ist. Wenn ja, FTPS bevorzugen.
4. GitHub Secrets im Repository anlegen.
5. Eine GitHub Action definieren, die:
   - bei Push auf `main` ausgelöst wird,
   - das Repository auscheckt,
   - die Zielverbindung per FTP/FTPS öffnet,
   - Dateien hochlädt.
6. Einen Test-Deploy ausführen.
7. Logausgabe der Action prüfen.
8. Bei Bedarf Ausschlüsse definieren, damit keine unnötigen Dateien übertragen werden.

## Technische Einschränkungen

- FTP ist unsicher, weil Zugangsdaten und Daten ohne TLS unverschlüsselt übertragen werden können.
- FTPS ist besser, aber nicht jeder cPanel-Server ist korrekt dafür konfiguriert.
- Firewalls und passive Ports müssen korrekt geöffnet sein.
- Große Deployments über FTP sind oft langsamer als SSH/rsync.
- Ein direkter "GitHub-zu-Server"-Link ohne CI/CD-Schritt ist nicht vorhanden.

## Praktikable Alternative für dauerhaftes Deployment

Wenn FTP/FTPS nicht sauber funktioniert oder nicht gewünscht ist:

### Empfohlen: SSH + rsync

- sichere Verbindung per SSH,
- schnelleres inkrementelles Deployment,
- besser für automatisierte, dauerhafte Deployments,
- Secrets bleiben ebenfalls in GitHub Secrets.

### Weitere Alternative

- ein externer Deploy-Service oder ein selbst gehosteter Runner mit Zugriff auf den Server.

## Fazit

Die gewünschte Automatisierung ist mit FTP/FTPS machbar, **wenn** der cPanel-Server FTP/FTPS akzeptiert und GitHub Actions die Zugangsdaten als Secrets verwenden darf.  
Für ein dauerhaft stabiles Setup ist SSH + rsync in der Praxis meist die bessere Lösung.
