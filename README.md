# WebApp Foundry

<br>

```text
Projektname:   WebApp Foundry
Repository:    webapp-foundry
Kurzname:      WAF
Repositorytyp: Versioniertes Web-App-Repository
Schwerpunkt:   Logisch getrennte App-Dateien, Assets, AppConfig und AppData
```

<br>

> **WebApp Foundry** stellt Web-Apps in klar getrennten, vollständigen und unveränderlichen Versionen bereit. `catalog.json` listet alle Apps. Jede App besitzt genau eine `app.json`, welche die App beschreibt und sämtliche veröffentlichten Versionen auflistet. Der komplette Stand einer Version liegt ausschließlich im zugehörigen Versionsordner.

<br>

---

<br>

## 1. Zielbild

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
    │       │   │   └── hello-world-config.json
    │       │   └── data/
    │       │       └── .../
    │       └── 1.1.0/
    │           ├── app/
    │           ├── assets/
    │           ├── config/
    │           │   └── hello-world-config.json
    │           └── data/
    └── weitere-app/
        ├── app.json
        └── versions/
            └── 1.0.0/
                ├── app/
                ├── assets/
                ├── config/
                │   └── weitere-app-config.json
                └── data/
```

<br>

---

<br>

## 2. Grundprinzip

<br>

Das Repository trennt vier Bereiche:

<br>

```text
App-Katalog        catalog.json
App-Beschreibung   apps/<app-id>/app.json
App-Version        apps/<app-id>/versions/<version>/
Produktive Instanz außerhalb dieses Repositorys
```

<br>

Für jede App gilt:

<br>

- direkt im App-Ordner befindet sich als einzige Datei `app.json`;
- neben `app.json` existiert ausschließlich das Verzeichnis `versions/`;
- `app.json` beschreibt die App und listet alle veröffentlichten Versionen;
- jede Version enthält einen vollständigen und unabhängig nutzbaren App-Stand;
- App-Code, Assets, AppConfig-Vorlage und AppData-Ausgangsstruktur sind klar getrennt;
- produktive Instanzkonfigurationen und produktive AppData liegen außerhalb dieses Repositorys;
- veröffentlichte Versionsordner werden nicht nachträglich verändert.

<br>

---

<br>

## 3. `catalog.json`

<br>

`catalog.json` ist der zentrale Einstiegspunkt. Die Datei listet alle Apps und verweist auf deren `app.json`.

<br>

```json
{
  "schemaVersion": 1,
  "catalog": {
    "id": "webapp-foundry-main",
    "name": "WebApp Foundry",
    "updatedAt": "2026-10-19T08:53:00Z"
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

- jede `appId` ist im Katalog eindeutig;
- jeder Pfad verweist auf eine vorhandene `app.json`;
- die `appId` im Katalog entspricht der `appId` in der referenzierten `app.json`;
- alle Pfade sind relativ zum Repository-Stamm;
- absolute Pfade und Pfadsegmente wie `..` sind verboten;
- Versionsdetails stehen nicht in `catalog.json`, sondern ausschließlich in der jeweiligen `app.json`.

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

Die App-ID darf ausschließlich enthalten:

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

Die App-ID bleibt über sämtliche Versionen unverändert.

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
- aktuelle stabile Version;
- Liste aller veröffentlichten Versionen;
- Status jeder Version;
- Veröffentlichungszeitpunkt;
- relativen Pfad zum Versionsordner.

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
      "releasedAt": "2026-10-19T08:53:00Z"
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

- `latest` verweist auf eine vorhandene Version mit Status `stable`;
- jede Versionsnummer ist innerhalb der App eindeutig;
- jeder Versionspfad liegt unter `versions/`;
- jeder gelistete Versionsordner muss vorhanden sein;
- stabile Versionen werden nach Veröffentlichung nicht verändert;
- veraltete Versionen können als `deprecated` markiert werden;
- Versionen bleiben für Rollbacks und Nachvollziehbarkeit erhalten.

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

Jeder Versionsordner enthält den vollständigen Stand dieser App-Version.

<br>

```text
versions/<version>/
├── app/
├── assets/
├── config/
└── data/
```

<br>

Alle vier Verzeichnisse sind verbindlich:

<br>

```text
app/       ausführbarer App-Code
assets/    unveränderliche Assets und Ressourcen
config/    versionsbezogene AppConfig-Vorlage
data/      versionsbezogene AppData-Ausgangsstruktur
```

<br>

Eine App-Version darf keine Dateien aus einem anderen Versionsordner benötigen.

<br>

---

<br>

## 8. `app/` – ausführbarer App-Code

<br>

`app/` enthält den vollständigen ausführbaren Quellcode der betreffenden Version.

<br>

Beispiel:

<br>

```text
app/
├── index.php
└── src/
    ├── Application.php
    ├── Router.php
    └── Storage.php
```

<br>

Bei Bedarf sind weitere logische Unterverzeichnisse möglich:

<br>

```text
app/
├── index.php
├── src/
├── pages/
├── templates/
├── includes/
└── public/
```

<br>

Regeln:

<br>

- der App-Code ist vollständig;
- der Einstiegspunkt liegt innerhalb von `app/`;
- der Standard-Einstiegspunkt ist `app/index.php`;
- ein abweichender Einstiegspunkt wird innerhalb des App-Codes nachvollziehbar dokumentiert;
- produktive AppConfig gehört nicht nach `app/`;
- produktive AppData gehört nicht nach `app/`;
- Cache-, Session- und Logdateien gehören nicht nach `app/`.

<br>

---

<br>

## 9. `assets/` – unveränderliche Ressourcen

<br>

`assets/` enthält alle unveränderlichen Ressourcen, die zur betreffenden App-Version gehören.

<br>

Beispiel:

<br>

```text
assets/
├── icon.svg
└── resources/
    ├── css/
    ├── js/
    ├── images/
    ├── fonts/
    ├── languages/
    ├── templates/
    └── sample-files/
```

<br>

Geeignete Inhalte:

<br>

- App-Icon;
- Stylesheets;
- JavaScript-Dateien;
- Bilder;
- Schriften;
- Sprachdateien;
- statische Vorlagen;
- Beispieldateien;
- mitgelieferte Dokumente;
- unveränderliche Referenzdaten.

<br>

Nicht nach `assets/` gehören:

<br>

- produktive Benutzerdaten;
- veränderliche Laufzeitdaten;
- produktive Konfiguration;
- Zugangsschlüssel;
- Logs;
- Sessions;
- Cache-Dateien.

<br>

### Icon

<br>

Das App-Icon liegt unter:

<br>

```text
assets/icon.svg
```

<br>

Alternativ sind möglich:

<br>

```text
assets/icon.png
assets/icon.webp
```

<br>

Empfohlen:

<br>

- quadratisches Format;
- SVG bevorzugt;
- mindestens 128 × 128 Pixel bei Rastergrafiken;
- keine externen Hotlinks;
- kein ausführbares JavaScript in SVG-Dateien.

<br>

---

<br>

## 10. `config/` – AppConfig-Vorlage

<br>

`config/` enthält genau eine versionsbezogene Konfigurationsvorlage.

<br>

Das feste Namensschema lautet:

<br>

```text
<app-id>-config.json
```

<br>

Der vollständige Pfad lautet:

<br>

```text
apps/<app-id>/versions/<version>/config/<app-id>-config.json
```

<br>

Beispiele:

<br>

```text
apps/hello-world/versions/1.0.0/config/hello-world-config.json
apps/status-page/versions/2.1.0/config/status-page-config.json
apps/network-tools/versions/1.4.2/config/network-tools-config.json
```

<br>

Die Datei enthält sichere Standardwerte für eine neu angelegte Instanz.

<br>

Beispiel:

<br>

```json
{
  "title": "Hello World",
  "refreshSeconds": 0
}
```

<br>

Regeln:

<br>

- der Dateiname entspricht immer der App-ID;
- pro Version existiert genau eine AppConfig-Vorlage;
- die Datei muss gültiges JSON enthalten;
- die Vorlage darf keine Passwörter, Tokens oder privaten Schlüssel enthalten;
- die Vorlage wird beim Erstellen einer neuen Instanz kopiert;
- die Vorlage ist nicht die produktive Konfiguration einer Instanz;
- ein Update darf eine vorhandene produktive AppConfig nicht ungefragt ersetzen.

<br>

### Produktive AppConfig

<br>

Die produktive Konfiguration einer installierten Instanz liegt außerhalb dieses Repositorys.

<br>

Empfohlene Laufzeitstruktur:

<br>

```text
instances/<instance-id>/config/<app-id>-config.json
```

<br>

Beim Update gilt:

<br>

- vorhandene Benutzerwerte bleiben erhalten;
- neue Standardfelder können kontrolliert ergänzt werden;
- entfernte oder umbenannte Felder benötigen eine app-spezifische Migration;
- vor Änderungen wird die produktive Konfiguration gesichert.

<br>

---

<br>

## 11. `data/` – AppData-Ausgangsstruktur

<br>

`data/` enthält die vollständige initiale Datenstruktur einer neuen App-Instanz.

<br>

Beispiel:

<br>

```text
data/
├── rules.json
├── database.json
├── uploads/
│   └── .gitkeep
└── folders/
    └── .gitkeep
```

<br>

Oder bei einer App ohne initiale Daten:

<br>

```text
data/
└── .gitkeep
```

<br>

Geeignete Inhalte:

<br>

- leere Ausgangsdateien;
- Standardregeln;
- initiale JSON-Strukturen;
- Datenbankvorlagen;
- erforderliche leere Ordner;
- technische Startdaten;
- nicht produktive Beispieldaten, wenn sie für den Start erforderlich sind.

<br>

Nicht nach `data/` gehören:

<br>

- produktive Benutzerdaten;
- echte Uploads;
- reale Kunden- oder Personendaten;
- Passwörter oder Tokens;
- Logs;
- Sessions;
- Cache-Dateien.

<br>

### Installation

<br>

Beim Erstellen einer neuen Instanz wird der Inhalt von `data/` in den getrennten Laufzeitbereich der Instanz kopiert.

<br>

Empfohlene Laufzeitstruktur:

<br>

```text
instances/<instance-id>/data/
```

<br>

### Update

<br>

Bei einem Update gilt:

<br>

- vorhandene produktive AppData bleiben erhalten;
- der Inhalt des neuen `data/`-Ordners überschreibt keine vorhandenen produktiven Dateien;
- neue verpflichtende Dateien oder Ordner werden kontrolliert ergänzt;
- strukturelle Änderungen benötigen eine app-spezifische Migration;
- vor einer Migration werden die produktiven AppData gesichert.

<br>

---

<br>

## 12. Trennung der Datenarten

<br>

```text
app/       ausführbarer Quellcode
assets/    unveränderliche Ressourcen der Version
config/    AppConfig-Vorlage der Version
data/      AppData-Ausgangsstruktur der Version
```

<br>

Beispiele:

<br>

```text
PHP-Anwendung                         app/
Klassen und Funktionen                app/src/
CSS und JavaScript                    assets/resources/
App-Icon                              assets/icon.svg
Standard-Seitentitel                  config/<app-id>-config.json
leere Regeldatei                      data/rules.json
erforderlicher Upload-Ordner          data/uploads/.gitkeep
produktive Benutzerregeln             nicht in diesem Repository
produktive Uploads                    nicht in diesem Repository
```

<br>

---

<br>

## 13. Neue App veröffentlichen

<br>

1. App-ID festlegen.
2. `apps/<app-id>/` anlegen.
3. `app.json` anlegen.
4. `versions/1.0.0/` anlegen.
5. App-Code unter `app/` ablegen.
6. Icon und weitere Ressourcen unter `assets/` ablegen.
7. `<app-id>-config.json` unter `config/` anlegen.
8. initiale Datenstruktur unter `data/` anlegen.
9. Version `1.0.0` in `app.json` eintragen.
10. `latest` in `app.json` auf `1.0.0` setzen.
11. App in `catalog.json` eintragen.
12. Struktur, JSON und PHP-Dateien prüfen.
13. committen und veröffentlichen.

<br>

---

<br>

## 14. Neue Version veröffentlichen

<br>

1. neue Semantic-Version festlegen;
2. neuen Versionsordner anlegen;
3. vollständigen App-Code unter `app/` ablegen;
4. vollständige Ressourcen unter `assets/` ablegen;
5. aktuelle AppConfig-Vorlage unter `config/<app-id>-config.json` ablegen;
6. aktuelle AppData-Ausgangsstruktur unter `data/` ablegen;
7. neue Version in `app.json` eintragen;
8. `latest` bei einer stabilen Veröffentlichung aktualisieren;
9. Tests ausführen;
10. committen und veröffentlichen.

<br>

Vorherige Versionsordner werden nicht geändert oder gelöscht.

<br>

---

<br>

## 15. Updateablauf

<br>

Eine Verwaltungsplattform aktualisiert eine App nach folgendem Ablauf:

<br>

1. `catalog.json` laden;
2. zugehörige `app.json` laden;
3. installierte Version mit `latest` vergleichen;
4. neuen Versionsordner separat herunterladen;
5. Pflichtverzeichnisse und JSON-Dateien prüfen;
6. produktive AppConfig sichern;
7. produktive AppData sichern;
8. neuen App-Code bereitstellen;
9. erforderliche neue Konfigurationsfelder ergänzen;
10. erforderliche neue Datenstrukturen ergänzen;
11. neue Version aktivieren;
12. Funktion prüfen;
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

---

<br>

## 16. Validierung

<br>

Vor Veröffentlichung wird mindestens geprüft:

<br>

```text
catalog.json ist gültiges JSON
jede referenzierte app.json ist vorhanden
jede App-ID ist eindeutig
App-ID entspricht dem Verzeichnisnamen
latest verweist auf eine vorhandene stabile Version
alle Versionspfade sind vorhanden
Versionsnummern entsprechen MAJOR.MINOR.PATCH
app/ ist vorhanden
assets/ ist vorhanden
config/ ist vorhanden
data/ ist vorhanden
app/index.php oder dokumentierter Einstiegspunkt ist vorhanden
assets/icon.svg, icon.png oder icon.webp ist vorhanden
config/<app-id>-config.json ist vorhanden
config/<app-id>-config.json ist gültiges JSON
PHP-Lint ist erfolgreich
keine Pfadtraversierung
keine produktiven Geheimnisse
keine produktiven AppData
```

<br>

---

<br>

## 17. Testanforderungen

<br>

```text
Neuinstallation aus einer Version
Start des Einstiegspunkts
Laden statischer Ressourcen
Kopieren der AppConfig-Vorlage
Kopieren der AppData-Ausgangsstruktur
zweite unabhängige Instanz
Update auf die nächste Version
Erhalt produktiver AppConfig
Erhalt produktiver AppData
Ergänzung neuer Konfigurationsfelder
Ergänzung neuer Datenstrukturen
Rollback des App-Codes
Rollback der AppConfig
Rollback der AppData
```

<br>

Eine Version gilt erst nach erfolgreichen Tests als veröffentlichbar.

<br>

---

<br>

## 18. Sicherheitsregeln

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
- Ausgangsdaten dürfen keine realen Personen-, Kunden- oder Zugangsdaten enthalten.

<br>

---

<br>

## 19. Minimales Beispiel

<br>

```text
webapp-foundry/
├── README.md
├── LICENSE
├── catalog.json
└── apps/
    └── hello-world/
        ├── app.json
        └── versions/
            └── 1.0.0/
                ├── app/
                │   ├── index.php
                │   └── src/
                ├── assets/
                │   ├── icon.svg
                │   └── resources/
                ├── config/
                │   └── hello-world-config.json
                └── data/
                    └── .gitkeep
```

<br>

### `catalog.json`

<br>

```json
{
  "schemaVersion": 1,
  "catalog": {
    "id": "webapp-foundry-main",
    "name": "WebApp Foundry",
    "updatedAt": "2026-10-19T08:53:00Z"
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

### `apps/hello-world/app.json`

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
      "releasedAt": "2026-10-19T08:53:00Z"
    }
  ]
}
```

<br>

### `apps/hello-world/versions/1.0.0/app/index.php`

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

### `apps/hello-world/versions/1.0.0/config/hello-world-config.json`

<br>

```json
{
  "title": "Hello World",
  "refreshSeconds": 0
}
```

<br>

---

<br>

## 20. Repository-Regeln

<br>

1. Im Repository-Stamm liegen `README.md`, `LICENSE`, `catalog.json` und `apps/`.
2. `catalog.json` listet alle Apps und verweist auf deren `app.json`.
3. Im App-Ordner ist `app.json` die einzige Datei.
4. Neben `app.json` ist im App-Ordner ausschließlich `versions/` erlaubt.
5. `app.json` listet alle veröffentlichten Versionen der App.
6. Jede Version besitzt einen vollständigen und unabhängigen Versionsordner.
7. Jede Version enthält `app/`, `assets/`, `config/` und `data/`.
8. App-Code gehört nach `app/`.
9. Unveränderliche Ressourcen gehören nach `assets/`.
10. Die AppConfig-Vorlage liegt unter `config/<app-id>-config.json`.
11. Die AppData-Ausgangsstruktur liegt unter `data/`.
12. Produktive AppConfig gehört nicht in dieses Repository.
13. Produktive AppData gehört nicht in dieses Repository.
14. Geheimnisse werden nicht committed.
15. Veröffentlichte Versionsordner werden nicht nachträglich verändert.
16. Änderungen erzeugen eine neue Version und einen neuen Eintrag in `app.json`.
17. `latest` verweist immer auf eine vorhandene stabile Version.
18. Alte Versionen bleiben für Rollback und Nachvollziehbarkeit erhalten.

<br>

---

<br>

## 21. Abnahmekriterien

<br>

Das Repository ist verwendbar, wenn:

<br>

- `catalog.json` alle Apps korrekt listet;
- jede App genau eine `app.json` direkt im App-Ordner besitzt;
- jede `app.json` alle vorhandenen Versionen korrekt listet;
- `latest` auf eine vorhandene stabile Version verweist;
- jeder Versionsordner vollständig ist;
- App-Code, Assets, AppConfig-Vorlage und AppData-Ausgangsstruktur getrennt sind;
- der Einstiegspunkt vorhanden ist;
- die AppConfig-Datei dem Schema `<app-id>-config.json` folgt;
- die AppConfig-Datei gültiges JSON enthält;
- `data/` ausschließlich nicht produktive Ausgangsdaten enthält;
- keine produktiven Konfigurationen enthalten sind;
- keine produktiven AppData enthalten sind;
- keine Geheimnisse enthalten sind;
- PHP-Dateien syntaktisch gültig sind;
- Updates vorhandene AppConfig und AppData erhalten;
- Rollbacks auf frühere Versionen möglich sind.
