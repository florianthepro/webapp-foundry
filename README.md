# WebApp Foundry

```text
Projektname:    WebApp Foundry
Repository:     webapp-foundry
Kurzname:       WAF
Repositorytyp:  Strukturierter Web-App-Katalog
Schwerpunkt:    Portable, versionierte PHP-Webanwendungen
Kompatibilität: Eigenständig nutzbar, optional WAMS-kompatibel
```

> **WebApp Foundry** ist ein eigenständiges Repositoryformat für portable PHP-Webanwendungen. Jede Web-App liegt vollständig beschrieben, versioniert und prüfbar in einer festen Verzeichnisstruktur. Das Repository kann unabhängig gepflegt, getestet und veröffentlicht werden. Plattformen wie WAMS können es zusätzlich als App-Katalog und Installationsquelle verwenden.

---

## 1. Zweck

Das Repository enthält:

- Web-App-Quellcode;
- App-Manifeste;
- Icons und statische Assets;
- Konfigurationsschema und Standardwerte;
- optionale Migrationen;
- optionale Health-Checks;
- versionierte Releasepakete;
- Prüfsummen;
- Kataloginformationen.

Eine Foundry-App kann:

1. eigenständig entwickelt und getestet werden;
2. als normales PHP-Projekt veröffentlicht werden;
3. über ein Releasepaket verteilt werden;
4. optional von WAMS installiert und verwaltet werden.

---

## 3. Branding

### Name

### Beschreibung für GitHub

```text
Structured, portable and versioned PHP web applications with manifests, configuration schemas, releases and optional WAMS compatibility.
```

### Visuelle Richtung

```text
Grundfarbe:     #0b0d10
Fläche:         #151922
Linie:          #2b3442
Text:           #edf2f7
Sekundärtext:   #94a3b8
Akzent:         #38bdf8
Erfolg:         #4ade80
Warnung:        #facc15
Fehler:         #fb7185
```

Das Branding soll technisch, kompakt und neutral wirken. Es ist mit WAMS verwandt, aber als eigenständiges Projekt erkennbar.

### Logo-Idee

Das Logo kann aus einem stilisierten quadratischen „App-Baustein“ mit einem kleinen Amboss- oder Funkenmotiv bestehen. Im Repository müssen Apps jedoch eigene Icons verwenden und dürfen nicht automatisch das Foundry-Logo übernehmen.

---

## 4. Repository-Aufteilung im Gesamtsystem

### Repository 1: Management-System

```text
web-application-management-system
```

Verantwortlich für:

- Installation und Betrieb des Managers;
- Launcher;
- Benutzer und Gruppen;
- Rechte;
- Tenants;
- App-Installation;
- Updates;
- Backups;
- Synchronisierung.

### Repository 2: Web-App-Katalog

```text
webapp-foundry
```

Dieses Repository.

Verantwortlich für:

- App-Code;
- App-Manifeste;
- App-Versionen;
- Konfigurationsschema;
- Standardkonfiguration;
- App-Migrationen;
- Health-Checks;
- Releasepakete.

### Repository 3: Private Laufzeitdaten, optional

Empfohlener Name:

```text
webapp-vault
```

Mögliche Inhalte:

- Manager-Konfiguration;
- AppConfig installierter Instanzen;
- AppData installierter Instanzen;
- Synchronisierungsmanifest;
- Zeitstempel und Versionsinformationen.

Das dritte Repository ist optional und sollte privat sein.

---

## 5. Verzeichnisstruktur

```text
webapp-foundry/
├── README.md
├── LICENSE
├── catalog.json
├── catalog.schema.json
├── branding/
│   ├── logo.svg
│   ├── mark.svg
│   └── icon.png
├── apps/
│   ├── hello-world/
│   │   ├── app.json
│   │   ├── README.md
│   │   ├── LICENSE
│   │   ├── config.schema.json
│   │   ├── config.defaults.json
│   │   ├── assets/
│   │   │   ├── icon.svg
│   │   │   └── screenshots/
│   │   ├── public/
│   │   │   ├── index.php
│   │   │   └── assets/
│   │   │       ├── app.css
│   │   │       └── app.js
│   │   ├── src/
│   │   ├── migrations/
│   │   ├── health/
│   │   │   └── check.php
│   │   └── releases/
│   │       └── 1.0.0/
│   │           ├── manifest.json
│   │           ├── checksums.sha256
│   │           └── package.zip
│   └── weitere-app/
├── schemas/
│   ├── app.schema.json
│   ├── catalog.schema.json
│   └── release.schema.json
├── tools/
│   ├── validate.php
│   ├── package.php
│   └── checksum.php
└── tests/
    ├── fixtures/
    └── contract.php
```

### Verbindliche Hauptverzeichnisse

```text
apps/
schemas/
tools/
tests/
```

`branding/` ist für die Projektidentität vorgesehen und nicht für App-spezifische Icons.

---

## 6. Katalog

Die Datei `catalog.json` ist der zentrale Einstiegspunkt.

```json
{
  "schemaVersion": 1,
  "catalog": {
    "id": "webapp-foundry-main",
    "name": "WebApp Foundry",
    "description": "Portable and versioned PHP web applications.",
    "updatedAt": "2026-10-19T05:00:00Z"
  },
  "apps": [
    {
      "appId": "hello-world",
      "manifest": "apps/hello-world/app.json"
    }
  ]
}
```

Verbindliche Felder:

```text
schemaVersion
catalog.id
catalog.name
catalog.updatedAt
apps[].appId
apps[].manifest
```

Regeln:

- `appId` ist im gesamten Katalog eindeutig;
- Manifestpfade sind relativ zum Repository-Stamm;
- `..` ist in Pfaden nicht erlaubt;
- nur Apps mit gültigem Manifest werden veröffentlicht;
- die Reihenfolge im Katalog ist keine Installationsreihenfolge.

---

## 7. App-ID

Jede App liegt unter:

```text
apps/<app-id>/
```

Die App-ID darf ausschließlich enthalten:

```text
a-z
0-9
-
```

Gültig:

```text
hello-world
network-tools
asset-browser-2
```

Ungültig:

```text
HelloWorld
hello_world
hello world
../hello-world
```

Die App-ID bleibt über alle Versionen unverändert.

---

## 8. App-Manifest

Jede App benötigt `app.json`.

```json
{
  "schemaVersion": 1,
  "appId": "hello-world",
  "name": "Hello World",
  "summary": "Minimal portable PHP web application.",
  "version": "1.0.0",
  "icon": "assets/icon.svg",
  "entrypoint": "public/index.php",
  "license": "MIT",
  "homepage": "https://github.com/example/hello-world",
  "catalog": {
    "visible": true,
    "installable": true
  },
  "runtime": {
    "type": "php",
    "php": ">=8.2",
    "extensions": [
      "json"
    ]
  },
  "configuration": {
    "schema": "config.schema.json",
    "defaults": "config.defaults.json"
  },
  "storage": {
    "persistentData": false,
    "backup": false,
    "repositorySync": false
  },
  "permissions": [
    "use",
    "edit"
  ],
  "compatibility": {
    "wams": ">=5.0.0"
  },
  "healthCheck": "health/check.php",
  "migrations": "migrations",
  "update": {
    "strategy": "atomic",
    "rollback": true
  }
}
```

### Pflichtfelder

```text
schemaVersion
appId
name
summary
version
icon
entrypoint
license
catalog.visible
catalog.installable
runtime.type
runtime.php
configuration.schema
configuration.defaults
permissions
```

---

## 9. Versionsregeln

Apps verwenden Semantic Versioning:

```text
MAJOR.MINOR.PATCH
```

Beispiele:

```text
1.0.0
1.3.2
2.0.0
```

Bedeutung:

- `MAJOR`: inkompatible Änderung;
- `MINOR`: neue rückwärtskompatible Funktion;
- `PATCH`: rückwärtskompatible Fehlerbehebung.

Eine veröffentlichte Version wird nicht nachträglich verändert. Änderungen erzeugen immer eine neue Version.

---

## 10. Eigenständige Ausführung

Eine App soll lokal oder in einer normalen PHP-Umgebung testbar bleiben.

Empfohlener Entwicklungsstart:

```bash
cd apps/hello-world
php -S 127.0.0.1:8080 -t public
```

Eine App darf im eigenständigen Betrieb Standardwerte verwenden, wenn kein Plattformkontext bereitgestellt wird.

```php
<?php
declare(strict_types=1);

$platform = isset($webapp) && is_array($webapp)
    ? $webapp
    : [
        'managed' => false,
        'authorized' => true,
        'canManage' => true,
        'config' => require dirname(__DIR__) . '/config.defaults.php',
        'dataDir' => dirname(__DIR__) . '/var'
    ];
```

Eine App darf nicht davon abhängig sein, dass WAMS eine globale Variable bereitstellt, solange die App nicht ausdrücklich als ausschließlich verwaltete App dokumentiert ist.

---

## 11. Standardisierter Plattformkontext

Für verwaltete Ausführung verwendet WebApp Foundry den neutralen Kontext `$webapp`.

```php
$webapp = [
    'managed' => true,
    'authorized' => true,
    'canManage' => false,
    'platform' => [
        'id' => 'wams',
        'version' => '5.0.0'
    ],
    'urls' => [
        'base' => '/?page=app&id=<instance-id>',
        'launcher' => '/',
        'assets' => '/?page=asset&id=<instance-id>&file='
    ],
    'instance' => [
        'id' => '<instance-id>',
        'name' => 'Hello World München',
        'appId' => 'hello-world',
        'version' => '1.0.0',
        'tenantId' => null
    ],
    'user' => [
        'id' => '<user-id>',
        'username' => 'florian'
    ],
    'tenant' => null,
    'config' => [],
    'paths' => [
        'configFile' => '/absolute/path/config.json',
        'dataDir' => '/absolute/path/data'
    ]
];
```

WAMS kann diesen neutralen Kontext zusätzlich als `$wams` spiegeln, damit bestehende WAMS-Apps kompatibel bleiben.

Eine Foundry-App sollte bevorzugt `$webapp` verwenden.

---

## 12. Einstiegspunkt

Das Manifest definiert den Einstiegspunkt:

```json
{
  "entrypoint": "public/index.php"
}
```

Der Einstiegspunkt:

- liegt innerhalb des App-Verzeichnisses;
- darf keine Pfadtraversierung enthalten;
- darf keine fest codierte Installations-URL voraussetzen;
- verwendet relative oder bereitgestellte Asset-URLs;
- prüft den Plattformkontext bei verwaltetem Betrieb.

Beispiel:

```php
<?php
declare(strict_types=1);

$context = isset($webapp) && is_array($webapp)
    ? $webapp
    : ['managed' => false, 'authorized' => true, 'config' => []];

if ($context['managed'] && empty($context['authorized'])) {
    http_response_code(403);
    exit;
}

$message = htmlspecialchars(
    (string) ($context['config']['message'] ?? 'Hello World'),
    ENT_QUOTES | ENT_SUBSTITUTE,
    'UTF-8'
);
?>
<!doctype html>
<html lang="de">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Hello World</title>
</head>
<body>
    <h1><?= $message ?></h1>
</body>
</html>
```

---

## 13. Rückkehr zur Plattform

Eine App soll den Rückweg nicht fest in das Design einbauen müssen.

Bevorzugtes Modell:

- Eine Plattform darf einen eigenen Overlay-Button hinzufügen.
- Der Original-App-Code bleibt unverändert.
- Im eigenständigen Modus wird kein Plattformbutton angezeigt.

Wenn die App selbst einen Rückkehrlink anbietet, verwendet sie:

```php
$launcherUrl = $webapp['urls']['launcher'] ?? null;
```

Kein fest codiertes `/`, wenn die App auch außerhalb einer Verwaltungsplattform funktionieren soll.

---

## 14. Berechtigungen

Standardrechte:

```text
use
edit
```

### `use`

Erlaubt normale App-Nutzung.

### `edit`

Erlaubt AppConfig-Editor und app-spezifische Verwaltungsfunktionen.

Eine App darf zusätzliche Rechte deklarieren:

```json
{
  "permissions": [
    "use",
    "edit",
    "export",
    "delete-data"
  ]
}
```

Zusätzliche Rechte müssen in der App-README beschrieben werden.

Die Plattform prüft Rechte vor dem App-Aufruf. Die App prüft kritische Funktionen zusätzlich selbst.

---

## 15. Konfiguration

Das Repository enthält:

```text
config.schema.json
config.defaults.json
```

Eine konkrete Instanzkonfiguration gehört nicht in dieses Repository.

### Standardwerte

```json
{
  "message": "Hello World",
  "theme": "system",
  "pageSize": 50
}
```

### Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": [
    "message"
  ],
  "properties": {
    "message": {
      "title": "Nachricht",
      "type": "string",
      "minLength": 1,
      "maxLength": 200
    },
    "theme": {
      "title": "Darstellung",
      "type": "string",
      "enum": [
        "system",
        "light",
        "dark"
      ],
      "default": "system"
    },
    "pageSize": {
      "title": "Einträge pro Seite",
      "type": "integer",
      "enum": [25, 50, 100, 250],
      "default": 50
    }
  },
  "additionalProperties": false
}
```

Das Schema ist die Grundlage für eine automatisch erzeugte Verwaltungsoberfläche.

---

## 16. AppData

Veränderliche App-Daten werden nicht innerhalb des App-Codes gespeichert.

Eigenständiger Betrieb:

```text
apps/<app-id>/var/
```

Verwalteter Betrieb:

```text
<platform-state>/instances/<instance-id>/data/
```

Der tatsächliche Pfad wird über den Plattformkontext bereitgestellt:

```php
$dataDir = $webapp['paths']['dataDir'] ?? dirname(__DIR__) . '/var';
```

Die App muss das Datenverzeichnis bei Bedarf sicher anlegen und Schreibfehler klar melden.

---

## 17. Assets und Icons

App-spezifische Assets liegen in:

```text
assets/
public/assets/
```

Das App-Icon liegt bevorzugt unter:

```text
assets/icon.svg
```

Anforderungen:

- quadratisch;
- SVG bevorzugt;
- alternativ PNG oder WebP;
- keine externen Hotlinks;
- kein JavaScript im SVG;
- keine fremden Markenzeichen ohne Nutzungsrecht;
- bei Rastergrafiken mindestens 128 × 128 Pixel.

---

## 18. App-README

Jede App besitzt eine eigene `README.md` mit mindestens:

```text
Name
Kurzbeschreibung
Screenshots
Eigenständiger Start
Konfigurationsfelder
AppData-Struktur
Berechtigungen
Abhängigkeiten
Updatehinweise
Migrationen
Lizenz
Herkunft
```

Die Repository-README beschreibt den Standard. Die App-README beschreibt die konkrete Anwendung.

---

## 19. Migrationen

Migrationen liegen unter:

```text
migrations/
```

Namensschema:

```text
001-initial.php
002-add-field.php
003-normalize-data.php
```

Migrationen dürfen ausschließlich Daten der betroffenen App-Instanz verändern.

Beispiel:

```php
<?php
declare(strict_types=1);

return static function (array $context): void {
    $dataDir = (string) $context['paths']['dataDir'];
    $file = $dataDir . '/data.json';

    if (!is_file($file)) {
        return;
    }

    $data = json_decode((string) file_get_contents($file), true);

    if (!is_array($data)) {
        throw new RuntimeException('AppData ist ungültig.');
    }

    $data['schemaVersion'] = 2;

    $json = json_encode(
        $data,
        JSON_PRETTY_PRINT | JSON_UNESCAPED_SLASHES
    );

    if ($json === false || file_put_contents($file, $json, LOCK_EX) === false) {
        throw new RuntimeException('Migration konnte AppData nicht speichern.');
    }
};
```

---

## 20. Health-Check

Optional:

```text
health/check.php
```

```php
<?php
declare(strict_types=1);

return static function (array $context): array {
    $dataDir = (string) $context['paths']['dataDir'];

    return [
        'ok' => is_dir($dataDir) && is_writable($dataDir),
        'checks' => [
            'dataDirectoryExists' => is_dir($dataDir),
            'dataDirectoryWritable' => is_writable($dataDir)
        ]
    ];
};
```

Ein fehlgeschlagener Health-Check verhindert bei einer verwalteten Installation die Aktivierung der neuen App-Version.

---

## 21. Releases

Empfohlene Struktur:

```text
apps/<app-id>/releases/<version>/
├── manifest.json
├── checksums.sha256
└── package.zip
```

Ein Releasepaket enthält ausschließlich die Dateien der betreffenden App-Version.

Nicht enthalten:

```text
var/
AppData
produktive config.json
Passwörter
Tokens
Logs
Cache
```

---

## 22. Prüfsummen

Für jede Releaseversion wird `checksums.sha256` erzeugt.

```text
4a1f...  app.json
9b62...  public/index.php
0d8c...  public/assets/app.css
```

Die Plattform prüft die Werte vor der Aktivierung.

Bereits veröffentlichte Pakete und Prüfsummen werden nicht nachträglich verändert.

---

## 23. Updateablauf

1. neue Version separat herunterladen;
2. Paket entpacken;
3. Manifest validieren;
4. Prüfsummen validieren;
5. Laufzeitanforderungen prüfen;
6. AppConfig sichern;
7. AppData sichern;
8. Migrationen ausführen;
9. Health-Check ausführen;
10. Version atomar aktivieren;
11. bei Fehler vollständig zurückrollen.

Ein Update ändert nicht automatisch:

```text
Instanzname
Tenant-Zuordnung
Benutzerrechte
Gruppenrechte
AppConfig
AppData
```

---

## 24. Sicherheit

Verbindliche Regeln:

- `declare(strict_types=1);` in PHP-Dateien;
- HTML-Ausgaben escapen;
- vorbereitete SQL-Anweisungen;
- keine eigenen Plattformpasswörter, wenn die App verwaltet läuft;
- keine Secrets im Repository;
- keine Secrets in URLs;
- keine Ausführung hochgeladener Dateien;
- keine freien absoluten Pfade aus Benutzereingaben;
- keine Schreibzugriffe außerhalb des bereitgestellten Datenverzeichnisses;
- keine Shellbefehle mit unvalidierten Eingaben;
- keine Paketinstallation zur Laufzeit;
- keine Deaktivierung der TLS-Prüfung ohne dokumentierten Grund;
- keine Veränderung anderer Apps oder Instanzen.

---

## 25. Optionales privates Repository

Empfohlener Name:

```text
webapp-vault
```

Beispiel:

```text
webapp-vault/
├── README.md
└── vault/
    ├── manifest.json
    ├── manager/
    │   └── config.json
    ├── app-config/
    │   └── <instance-id>/
    │       └── config.json
    └── app-data/
        └── <instance-id>/
            └── ...
```

Mögliche Modi:

```text
Manager-Konfiguration
AppConfig
AppData
AppConfig und AppData
Alles
```

Nicht synchronisieren:

```text
state/secret.key
Repository-Tokens
private TLS-Schlüssel
Sessions
temporäre Dateien
Caches
```

Apps greifen nicht direkt auf `webapp-vault` zu. Eine Verwaltungsplattform übernimmt Import, Export und Synchronisierung.

---

## 26. Validierung

Vor Veröffentlichung müssen mindestens geprüft werden:

```text
catalog.json
app.json
config.schema.json
config.defaults.json
PHP-Lint
App-ID und Verzeichnisname
Semantic Versioning
Entry-Point-Pfad
Icon-Datei
Pfadtraversierung
Checksummen
Health-Check
Migrationen
Releasepaket
```

Der Validator muss bei Fehlern mit einem Exitcode ungleich null abbrechen.

---

## 27. Testanforderungen

```text
Eigenständiger Start
verwalteter Start
Installation ohne Tenant
Installation mit Tenant
zweite Instanz derselben App
use-Recht
edit-Recht
Gruppenrecht
Konfigurationsänderung
AppData-Schreibtest
Backup
Wiederherstellung
Update
Migration
Health-Check
Rollback
Checksummenprüfung
```

Eine App gilt erst nach erfolgreichen Tests als veröffentlichbar.

---

## 28. Veröffentlichung

1. App-ID festlegen.
2. App-Verzeichnis anlegen.
3. `app.json` schreiben.
4. Einstiegspunkt implementieren.
5. Icon ergänzen.
6. Standardkonfiguration ergänzen.
7. Konfigurationsschema ergänzen.
8. App-README schreiben.
9. AppData-Zugriffe auf das bereitgestellte Datenverzeichnis beschränken.
10. Migrationen ergänzen.
11. Health-Check ergänzen.
12. Tests ausführen.
13. Version erhöhen.
14. Releasepaket erzeugen.
15. Prüfsummen erzeugen.
16. `catalog.json` aktualisieren.
17. Commit und Release veröffentlichen.

---

## 29. Minimale Beispiel-App

```text
apps/hello-world/
├── app.json
├── README.md
├── LICENSE
├── config.schema.json
├── config.defaults.json
├── assets/
│   └── icon.svg
└── public/
    └── index.php
```

`app.json`:

```json
{
  "schemaVersion": 1,
  "appId": "hello-world",
  "name": "Hello World",
  "summary": "Minimal portable PHP web application.",
  "version": "1.0.0",
  "icon": "assets/icon.svg",
  "entrypoint": "public/index.php",
  "license": "MIT",
  "catalog": {
    "visible": true,
    "installable": true
  },
  "runtime": {
    "type": "php",
    "php": ">=8.2",
    "extensions": ["json"]
  },
  "configuration": {
    "schema": "config.schema.json",
    "defaults": "config.defaults.json"
  },
  "storage": {
    "persistentData": false,
    "backup": false,
    "repositorySync": false
  },
  "permissions": ["use", "edit"],
  "compatibility": {
    "wams": ">=5.0.0"
  },
  "update": {
    "strategy": "atomic",
    "rollback": true
  }
}
```

`config.defaults.json`:

```json
{
  "message": "Hello World"
}
```

`config.schema.json`:

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "type": "object",
  "required": ["message"],
  "properties": {
    "message": {
      "title": "Nachricht",
      "type": "string",
      "minLength": 1,
      "maxLength": 200
    }
  },
  "additionalProperties": false
}
```

`public/index.php`:

```php
<?php
declare(strict_types=1);

$context = isset($webapp) && is_array($webapp)
    ? $webapp
    : [
        'managed' => false,
        'authorized' => true,
        'config' => ['message' => 'Hello World']
    ];

if ($context['managed'] && empty($context['authorized'])) {
    http_response_code(403);
    exit;
}

$message = htmlspecialchars(
    (string) ($context['config']['message'] ?? 'Hello World'),
    ENT_QUOTES | ENT_SUBSTITUTE,
    'UTF-8'
);
?>
<!doctype html>
<html lang="de">
<head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Hello World</title>
</head>
<body>
    <h1><?= $message ?></h1>
</body>
</html>
```

---

## 30. Repository-Regeln

1. WebApp Foundry ist ein eigenständiges Projekt und kein Unterordner von WAMS.
2. Der Repositoryname lautet `webapp-foundry`.
3. Apps liegen ausschließlich unter `apps/<app-id>/`.
4. Produktive Instanzkonfigurationen gehören nicht in dieses Repository.
5. Produktive AppData gehört nicht in dieses Repository.
6. Geheimnisse werden niemals committed.
7. Jede App besitzt Manifest, README, Lizenz, Icon und Einstiegspunkt.
8. Jede veröffentlichte Version ist unveränderlich.
9. Updates erhalten AppConfig und AppData.
10. Eine App darf keine andere App oder Instanz verändern.
11. Apps bleiben nach Möglichkeit eigenständig ausführbar.
12. Plattformintegration erfolgt über den neutralen `$webapp`-Kontext.
13. WAMS-Kompatibilität ist optional und wird unter `compatibility.wams` deklariert.
14. Nur validierte Apps werden in `catalog.json` eingetragen.
15. Beispiel-Apps werden nur sichtbar veröffentlicht, wenn sie als echte installierbare Apps gedacht sind.

---

## 31. Abnahmekriterien

Das Repository ist veröffentlichbar, wenn:

- das Branding vollständig vorhanden ist;
- `catalog.json` dem Schema entspricht;
- alle App-Manifeste gültig sind;
- jede App eigenständig oder ausdrücklich nur verwaltet dokumentiert ist;
- keine produktiven Daten oder Geheimnisse enthalten sind;
- Releasepakete reproduzierbar gebaut werden können;
- Checksummen stimmen;
- Updates AppConfig und AppData erhalten;
- alle Tests aus Abschnitt 27 erfolgreich sind;
- WAMS-kompatible Apps den dokumentierten Kontext korrekt verwenden.
