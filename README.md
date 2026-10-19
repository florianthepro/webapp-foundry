# WebApp Foundry

<br>

```text
Projektname:   WebApp Foundry
Repository:    webapp-foundry
Kurzname:      WAF
Repositorytyp: Versioniertes Web-App-Repository
Schwerpunkt:   Logisch getrennte App-Dateien, AppConfig und AppData
```

<br>

> **WebApp Foundry** stellt Web-Apps in klar getrennten und unveränderlichen Versionen bereit. `catalog.json` listet alle Apps. Jede App besitzt genau eine Datei `app.json`. Diese Datei beschreibt die App und listet alle verfügbaren Versionen. Der vollständige Inhalt jeder Version liegt ausschließlich im zugehörigen Versionsordner.

<br>

---

<br>

## 1. Grundprinzip

<br>

Das Repository trennt vier Bereiche:

<br>

```text
App-Katalog        catalog.json
App-Beschreibung   apps/<app-id>/app.json
App-Version        apps/<app-id>/versions/<version>/
Laufzeitinstanz    außerhalb dieses Repositorys
```

<br>

Für jede App gilt:

<br>

- im App-Ordner befindet sich als einzige Datei `app.json`;
- zusätzlich enthält der App-Ordner ausschließlich das Verzeichnis `versions/`;
- `app.json` beschreibt die App und listet ihre Versionen;
- jede Version enthält den vollständigen, eigenständig nutzbaren Versionsstand;
- App-Code, Nutzdaten, AppConfig-Daten und AppData-Vorlagen sind innerhalb jeder Version getrennt;
- produktive Instanzdaten und produktive Geheimnisse werden nicht in diesem Repository gespeichert;
- veröffentlichte Versionsordner werden nicht nachträglich verändert.

<br>

---

<br>

## 2. Repository-Struktur

<br>

```text
webapp-foundry/
├── README.md
├── LICENSE
├── catalog.json
└── apps/
    ├── hello-world/
    │   ├── app.json
    │   └── versions/
    │       ├── 1.0.0/
    │       │   ├── app/
    │       │   │   ├── index.php
    │       │   │   └── src/
    │       │   ├── assets/
    │       │   │   ├── icon.svg
    │       │   │   └── resources/
    │       │   ├── config/
    │       │   │   ├── defaults.json
    │       │   │   ├── schema.json
    │       │   │   └── README.md
    │       │   ├── data/
    │       │   │   ├── defaults/
    │       │   │   ├── schema.json
    │       │   │   └── README.md
    │       │   ├── README.md
    │       │   └── LICENSE
    │       └── 1.1.0/
    │           ├── .../
    └── weitere-app/
        ├── app.json
        └── versions/
            └── 1.0.0/
```

<br>

Im App-Ordner sind damit nur diese beiden Einträge erlaubt:

<br>

```text
apps/<app-id>/app.json
apps/<app-id>/versions/
```

<br>

Icons, READMEs, Lizenzen, Konfigurationsvorlagen, Datenvorlagen und sonstige App-Dateien liegen immer innerhalb des jeweiligen Versionsordners.

<br>

---

<br>

## 3. `catalog.json`

<br>

`catalog.json` ist der zentrale Einstiegspunkt und listet alle Apps über den Pfad zu deren `app.json`.

<br>

```json
{
  "schemaVersion": 1,
  "catalog": {
    "id": "webapp-foundry-main",
    "name": "WebApp Foundry",
    "updatedAt": "2026-10-19T06:00:00Z"
  },
  "apps": [
    {
      "appId": "hello-world",
      "app": "apps/hello-world/app.json"
    },
    {
      "appId": "status-page",
      "app": "apps/status-page/app.json"
    }
  ]
}
```

<br>

Verbindliche Felder:

<br>

```text
schemaVersion
catalog.id
catalog.name
catalog.updatedAt
apps[].appId
apps[].app
```

<br>

Regeln:

<br>

- jede `appId` ist eindeutig;
- der angegebene Pfad verweist auf eine vorhandene `app.json`;
- die `appId` in `catalog.json` entspricht der `appId` in `app.json`;
- Pfade sind relativ zum Repository-Stamm;
- `..` und absolute Pfade sind nicht erlaubt;
- `catalog.json` enthält keine Versionsdetails; Versionen werden ausschließlich in der jeweiligen `app.json` geführt.

<br>

---

<br>

## 4. App-ID

<br>

Jede App liegt unter:

<br>

```text
apps/<app-id>/
```

<br>

Erlaubte Zeichen:

<br>

```text
a-z
0-9
-
```

<br>

Gültig:

<br>

```text
hello-world
status-page
asset-browser-2
```

<br>

Ungültig:

<br>

```text
HelloWorld
hello_world
hello world
../hello-world
```

<br>

Die App-ID bleibt über alle Versionen unverändert.

<br>

---

<br>

## 5. `app.json`

<br>

`app.json` ist die einzige Datei direkt im App-Ordner. Sie enthält:

<br>

- App-ID;
- Anzeigename;
- Kurzbeschreibung;
- aktuelle Version;
- Liste aller veröffentlichten Versionen;
- Status der Versionen;
- relative Pfade zu den Versionsordnern.

<br>

Beispiel:

<br>

```json
{
  "schemaVersion": 1,
  "appId": "hello-world",
  "name": "Hello World",
  "summary": "Einfache Beispiel-Web-App.",
  "latest": "1.1.0",
  "versions": [
    {
      "version": "1.0.0",
      "path": "versions/1.0.0",
      "status": "stable",
      "releasedAt": "2026-10-01T10:00:00Z"
    },
    {
      "version": "1.1.0",
      "path": "versions/1.1.0",
      "status": "stable",
      "releasedAt": "2026-10-19T06:00:00Z"
    }
  ]
}
```

<br>

Verbindliche Felder:

<br>

```text
schemaVersion
appId
name
summary
latest
versions[].version
versions[].path
versions[].status
versions[].releasedAt
```

<br>

Zulässige Statuswerte:

<br>

```text
stable
preview
deprecated
```

<br>

Regeln:

<br>

- `latest` verweist auf eine vorhandene Version in `versions`;
- jede Versionsnummer ist innerhalb der App eindeutig;
- jeder Versionspfad liegt unter `versions/`;
- der Versionsordner muss vorhanden sein;
- stabile Versionen werden nicht nachträglich verändert;
- veraltete Versionen werden als `deprecated` markiert, aber nicht entfernt, solange sie noch für Rollbacks erforderlich sind.

<br>

---

<br>

## 6. Versionsformat

<br>

Versionen verwenden Semantic Versioning:

<br>

```text
MAJOR.MINOR.PATCH
```

<br>

Beispiele:

<br>

```text
1.0.0
1.3.2
2.0.0
```

<br>

Bedeutung:

<br>

- `MAJOR`: inkompatible Änderung;
- `MINOR`: neue rückwärtskompatible Funktion;
- `PATCH`: rückwärtskompatible Fehlerbehebung.

<br>

Der Name des Versionsordners entspricht exakt der Versionsnummer:

<br>

```text
apps/hello-world/versions/1.1.0/
```

<br>

---

<br>

## 7. Inhalt eines Versionsordners

<br>

Jeder Versionsordner enthält den vollständigen Stand der jeweiligen App-Version.

<br>

```text
versions/<version>/
├── app/
├── app-assets/
├── app-config/
├── app-data/
├── migrations/
├── health/
├── README.md
└── LICENSE
```

<br>

Pflichtverzeichnisse:

<br>

```text
app/
app-assets/
app-config/
app-data/
```

<br>

Optionale Verzeichnisse:

<br>

```text
migrations/
health/
```

<br>

Pflichtdateien:

<br>

```text
README.md
LICENSE
```

<br>

---

<br>

## 8. `app/` – ausführbarer App-Code

<br>

`app/` enthält ausschließlich den ausführbaren, versionsgebundenen App-Code.

<br>

Beispiel:

<br>

```text
app/
├── index.php
├── src/
├── templates/
└── public/
    └── assets/
        ├── app.css
        └── app.js
```

<br>

Der Einstiegspunkt wird in der versionsbezogenen `README.md` dokumentiert:

<br>

```text
Einstiegspunkt: app/index.php
```

<br>

Regeln:

<br>

- der App-Code ist vollständig;
- es gibt keine Abhängigkeit zu Dateien aus einer anderen App-Version;
- es gibt keine produktiven AppData in `app/`;
- es gibt keine produktiven AppConfig-Dateien in `app/`;
- der Versionsordner kann separat geprüft und bereitgestellt werden.

<br>

---

<br>

## 9. `app-assets/` – Nutzdaten und statische Ressourcen

<br>

`app-assets/` enthält nicht veränderliche Nutzdaten und Ressourcen, die zur jeweiligen App-Version gehören.

<br>

Beispiele:

<br>

```text
app-assets/
├── icon.svg
├── screenshots/
├── templates/
├── dictionaries/
├── sample-files/
└── resources/
```

<br>

Typische Inhalte:

<br>

- App-Icon;
- Bilder;
- mitgelieferte Vorlagen;
- statische Referenzdaten;
- Sprachdateien;
- Beispieldateien;
- mitgelieferte Dokumente;
- andere unveränderliche Nutzdaten.

<br>

Nicht hierher gehören:

<br>

- produktive Benutzerdaten;
- veränderliche Laufzeitdaten;
- produktive Konfiguration;
- Zugangsschlüssel;
- Cache-Dateien;
- Logs.

<br>

---

<br>

## 10. `app-config/` – AppConfig-Struktur

<br>

`app-config/` enthält ausschließlich die Konfigurationsstruktur und die Standardkonfiguration der Version.

<br>

```text
app-config/
├── defaults.json
├── schema.json
└── README.md
```

<br>

### `defaults.json`

<br>

Enthält sichere Standardwerte:

<br>

```json
{
  "title": "Hello World",
  "refreshSeconds": 0
}
```

<br>

### `schema.json`

<br>

Beschreibt die erlaubte Konfiguration:

<br>

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": [
    "title"
  ],
  "properties": {
    "title": {
      "type": "string",
      "minLength": 1,
      "maxLength": 120
    },
    "refreshSeconds": {
      "type": "integer",
      "minimum": 0,
      "maximum": 86400
    }
  },
  "additionalProperties": false
}
```

<br>

### `README.md`

<br>

Dokumentiert:

<br>

- alle Konfigurationsfelder;
- Standardwerte;
- Pflichtfelder;
- erlaubte Werte;
- Änderungen gegenüber der vorherigen Version;
- geheime Werte, die extern eingetragen werden müssen.

<br>

Produktive Konfigurationswerte gehören nicht in dieses Repository.

<br>

---

<br>

## 11. `app-data/` – AppData-Struktur und Startdaten

<br>

`app-data/` enthält die Struktur, Vorlagen und optionalen Startdaten für veränderliche AppData.

<br>

```text
app-data/
├── defaults/
├── schema.json
└── README.md
```

<br>

### `defaults/`

<br>

Kann initiale, nicht geheime Startdaten enthalten:

<br>

```text
app-data/defaults/
├── database.json
├── rules.json
└── folders/
```

<br>

Diese Daten werden bei einer neuen Instanz initial kopiert. Ein Update darf bestehende AppData nicht mit diesen Standarddaten überschreiben.

<br>

### `schema.json`

<br>

Beschreibt optional die erwartete AppData-Struktur oder deren Versionsstand.

<br>

### `README.md`

<br>

Dokumentiert:

<br>

- erzeugte Dateien und Ordner;
- Datenformat;
- Schreibrechte;
- Backup-Anforderungen;
- Migrationsanforderungen;
- Daten, die bei einem Update erhalten bleiben müssen.

<br>

Produktive AppData gehört nicht in dieses Repository.

<br>

---

<br>

## 12. Trennung der Datenarten

<br>

```text
app/          ausführbarer Quellcode
app-assets/   unveränderliche Nutzdaten und Ressourcen
app-config/   Konfigurationsschema und Standardkonfiguration
app-data/     AppData-Struktur und initiale Startdaten
```

<br>

Beispiele:

<br>

```text
Logo der App                         app-assets/
CSS und JavaScript des Programms     app/
Standard-Seitentitel                 app-config/defaults.json
Konfigurationsfeld-Beschreibung      app-config/schema.json
leere Regeln als Ausgangszustand     app-data/defaults/rules.json
produktive Regeln eines Nutzers      nicht in diesem Repository
hochgeladene Dokumente               nicht in diesem Repository
```

<br>

---

<br>

## 13. Versionsbezogene README

<br>

Jede Version benötigt:

<br>

```text
apps/<app-id>/versions/<version>/README.md
```

<br>

Die README enthält mindestens:

<br>

```text
App-Name
App-ID
Version
Status
Einstiegspunkt
PHP-Version
benötigte PHP-Erweiterungen
Code-Struktur
Nutzdaten
AppConfig
AppData
Installation
Update
Migrationen
bekannte Einschränkungen
```

<br>

Empfohlener Anfang:

<br>

```markdown
# Hello World 1.1.0

```text
App-ID:         hello-world
Version:        1.1.0
Status:         stable
Einstiegspunkt: app/index.php
PHP:            >= 8.2
```
```

<br>

---

<br>

## 14. Migrationen

<br>

App-spezifische Migrationen liegen innerhalb der betreffenden Version:

<br>

```text
migrations/
├── config/
└── data/
```

<br>

Beispiel:

<br>

```text
migrations/
├── config/
│   └── from-1.0.0.php
└── data/
    └── from-1.0.0.php
```

<br>

Regeln:

<br>

- Konfigurationsmigrationen verändern nur AppConfig;
- Datenmigrationen verändern nur AppData;
- produktive Daten werden vor Migrationen gesichert;
- Migrationen dürfen keine andere App oder Instanz verändern;
- Fehler müssen einen Rollback auslösen;
- erforderliche Migrationsreihenfolgen werden in der Versions-README dokumentiert.

<br>

---

<br>

## 15. Health-Check

<br>

Optional:

<br>

```text
health/check.php
```

<br>

Der Health-Check prüft die lauffähige App-Version nach Installation oder Update.

<br>

Er darf prüfen:

<br>

- Einstiegspunkt vorhanden;
- benötigte PHP-Erweiterungen vorhanden;
- AppConfig gültig;
- AppData-Verzeichnis vorhanden;
- erforderliche AppData-Dateien vorhanden;
- AppData beschreibbar;
- grundlegende App-Funktion erreichbar.

<br>

Ein fehlgeschlagener Health-Check verhindert die Aktivierung der neuen Version.

<br>

---

<br>

## 16. Veröffentlichung einer neuen App

<br>

1. App-ID festlegen.
2. `apps/<app-id>/` anlegen.
3. `app.json` anlegen.
4. `versions/1.0.0/` anlegen.
5. vollständigen App-Code unter `app/` ablegen.
6. statische Nutzdaten unter `app-assets/` ablegen.
7. AppConfig-Struktur unter `app-config/` ablegen.
8. AppData-Struktur unter `app-data/` ablegen.
9. Versions-README und Lizenz ergänzen.
10. optional Migrationen und Health-Check ergänzen.
11. `1.0.0` in `app.json` eintragen.
12. `latest` in `app.json` auf `1.0.0` setzen.
13. App in `catalog.json` eintragen.
14. Validierung und Tests ausführen.
15. committen und veröffentlichen.

<br>

---

<br>

## 17. Veröffentlichung einer neuen Version

<br>

1. neue Semantic-Version festlegen;
2. neuen Versionsordner anlegen;
3. vollständigen Code der neuen Version unter `app/` ablegen;
4. vollständige statische Nutzdaten unter `app-assets/` ablegen;
5. aktuelle AppConfig-Standards und Schema unter `app-config/` ablegen;
6. aktuelle AppData-Struktur und Startdaten unter `app-data/` ablegen;
7. Migrationen von unterstützten Vorgängerversionen ergänzen;
8. Health-Check ergänzen oder aktualisieren;
9. Versions-README aktualisieren;
10. neue Version in `app.json` eintragen;
11. `latest` in `app.json` aktualisieren;
12. Tests ausführen;
13. committen und veröffentlichen.

<br>

Vorherige Versionsordner werden dabei nicht verändert.

<br>

---

<br>

## 18. Updateablauf

<br>

Eine Verwaltungsplattform aktualisiert eine App nach folgendem Ablauf:

<br>

1. `catalog.json` laden;
2. zugehörige `app.json` laden;
3. installierte Version mit `latest` vergleichen;
4. neuen Versionsordner separat herunterladen;
5. Struktur prüfen;
6. AppConfig sichern;
7. AppData sichern;
8. Konfigurationsmigrationen ausführen;
9. AppData-Migrationen ausführen;
10. neuen App-Code aktivieren;
11. Health-Check ausführen;
12. bei Erfolg neue Version festschreiben;
13. bei Fehler App-Code, AppConfig und AppData zurückrollen.

<br>

Ein Update ändert nicht automatisch:

<br>

```text
Instanzname
Tenant-Zuordnung
Benutzerrechte
Gruppenrechte
produktive AppConfig
produktive AppData
```

<br>

Standardwerte aus `app-config/defaults.json` und `app-data/defaults/` gelten nur für neue Instanzen oder ausdrücklich dokumentierte Ergänzungen.

<br>

---

<br>

## 19. Validierung

<br>

Vor Veröffentlichung wird mindestens geprüft:

<br>

```text
catalog.json gültig
jede referenzierte app.json vorhanden
App-ID eindeutig
App-ID entspricht dem Verzeichnisnamen
latest verweist auf vorhandene Version
alle Versionspfade vorhanden
Semantic Versioning gültig
Pflichtverzeichnisse vorhanden
Versions-README vorhanden
Lizenz vorhanden
Einstiegspunkt vorhanden
PHP-Lint erfolgreich
app-config/defaults.json gültig
app-config/schema.json gültig
app-data/defaults logisch lesbar
keine Pfadtraversierung
keine produktiven Geheimnisse
keine produktiven AppData
Migrationen syntaktisch gültig
Health-Check syntaktisch gültig
```

<br>

---

<br>

## 20. Testanforderungen

<br>

```text
Neuinstallation ohne vorhandene AppConfig
Neuinstallation ohne vorhandene AppData
Start mit Standard-AppConfig
Start mit initialen AppData
zweite unabhängige Instanz
Update von jeder unterstützten Vorgängerversion
AppConfig-Migration
AppData-Migration
Erhalt produktiver AppConfig
Erhalt produktiver AppData
Health-Check
Rollback von App-Code
Rollback von AppConfig
Rollback von AppData
```

<br>

Eine Version gilt erst nach erfolgreichen Tests als veröffentlichbar.

<br>

---

<br>

## 21. Sicherheitsregeln

<br>

- keine Passwörter, Tokens oder privaten Schlüssel committen;
- keine produktiven AppConfig-Dateien committen;
- keine produktiven AppData committen;
- keine Logs, Sessions oder Cache-Dateien committen;
- keine absoluten Serverpfade fest codieren;
- keine fremden App-Verzeichnisse verändern;
- keine ungeprüften Benutzereingaben an Shellbefehle übergeben;
- keine hochgeladenen Dateien ungeprüft ausführen;
- HTML-Ausgaben escapen;
- SQL-Abfragen vorbereiten;
- TLS-Prüfungen nicht ohne dokumentierten Grund deaktivieren;
- Startdaten dürfen keine realen Personen-, Kunden- oder Zugangsdaten enthalten.

<br>

---

<br>

## 22. Minimales Beispiel

<br>

```text
webapp-foundry/
├── README.md
├── catalog.json
└── apps/
    └── hello-world/
        ├── app.json
        └── versions/
            └── 1.0.0/
                ├── app/
                │   └── index.php
                ├── app-assets/
                │   └── icon.svg
                ├── app-config/
                │   ├── defaults.json
                │   ├── schema.json
                │   └── README.md
                ├── app-data/
                │   ├── defaults/
                │   └── README.md
                ├── README.md
                └── LICENSE
```

<br>

`catalog.json`:

<br>

```json
{
  "schemaVersion": 1,
  "catalog": {
    "id": "webapp-foundry-main",
    "name": "WebApp Foundry",
    "updatedAt": "2026-10-19T06:00:00Z"
  },
  "apps": [
    {
      "appId": "hello-world",
      "app": "apps/hello-world/app.json"
    }
  ]
}
```

<br>

`apps/hello-world/app.json`:

<br>

```json
{
  "schemaVersion": 1,
  "appId": "hello-world",
  "name": "Hello World",
  "summary": "Einfache Beispiel-Web-App.",
  "latest": "1.0.0",
  "versions": [
    {
      "version": "1.0.0",
      "path": "versions/1.0.0",
      "status": "stable",
      "releasedAt": "2026-10-19T06:00:00Z"
    }
  ]
}
```

<br>

`apps/hello-world/versions/1.0.0/app/index.php`:

<br>

```php
<?php
declare(strict_types=1);
?>
<!doctype html>
<html lang="de">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Hello World</title>
</head>
<body>
    <h1>Hello World</h1>
</body>
</html>
```

<br>

`apps/hello-world/versions/1.0.0/app-config/defaults.json`:

<br>

```json
{
  "title": "Hello World"
}
```

<br>

`apps/hello-world/versions/1.0.0/app-data/defaults/.gitkeep` sorgt dafür, dass der leere Startdatenordner im Repository erhalten bleibt.

<br>

---

<br>

## 23. Repository-Regeln

<br>

1. `catalog.json` listet alle Apps und verweist auf deren `app.json`.
2. Im App-Ordner ist `app.json` die einzige Datei.
3. Neben `app.json` ist im App-Ordner nur `versions/` erlaubt.
4. `app.json` listet alle verfügbaren Versionen der App.
5. Jede Version besitzt einen vollständigen, eigenständigen Versionsordner.
6. Jede Version trennt `app/`, `app-assets/`, `app-config/` und `app-data/`.
7. App-Code gehört ausschließlich nach `app/`.
8. Unveränderliche Nutzdaten und Ressourcen gehören nach `app-assets/`.
9. AppConfig-Standards und Schema gehören nach `app-config/`.
10. AppData-Struktur und initiale Startdaten gehören nach `app-data/`.
11. Produktive AppConfig gehört nicht in dieses Repository.
12. Produktive AppData gehört nicht in dieses Repository.
13. Geheimnisse werden nicht committed.
14. Veröffentlichte Versionsordner werden nicht nachträglich verändert.
15. Änderungen erzeugen einen neuen Versionsordner und einen neuen Eintrag in `app.json`.
16. `latest` in `app.json` verweist immer auf eine vorhandene stabile Version.
17. Alte Versionen bleiben für Rollback und Nachvollziehbarkeit erhalten.

<br>

---

<br>

## 24. Abnahmekriterien

<br>

Das Repository ist verwendbar, wenn:

<br>

- `catalog.json` alle Apps korrekt listet;
- jede App genau eine `app.json` direkt im App-Ordner besitzt;
- jede `app.json` alle vorhandenen Versionen korrekt listet;
- `latest` auf eine vorhandene stabile Version verweist;
- jeder Versionsordner vollständig ist;
- App-Code, Nutzdaten, AppConfig und AppData logisch getrennt sind;
- Einstiegspunkte dokumentiert und vorhanden sind;
- AppConfig-Standardwerte und Schema gültig sind;
- AppData-Startdaten keine produktiven Daten enthalten;
- produktive Konfigurationen und AppData fehlen;
- keine Geheimnisse enthalten sind;
- PHP-Dateien syntaktisch gültig sind;
- Updates vorhandene AppConfig und AppData erhalten;
- Rollbacks auf vorherige Versionen möglich sind.
