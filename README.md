# WebApp Foundry

```text
Projektname:   WebApp Foundry
Repository:    webapp-foundry
Kurzname:      WAF
Repositorytyp: Versioniertes Web-App-Repository
```

## Zielstruktur

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
    │           └── data/
    └── weitere-app/
        ├── app.json
        └── versions/
            └── 1.0.0/
```

## `catalog.json`

`catalog.json` listet alle Apps und verweist auf deren `app.json`.

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

## `app.json`

`app.json` ist die einzige Datei direkt im App-Ordner. Neben ihr ist dort nur `versions/` erlaubt.

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
      "status": "stable"
    },
    {
      "version": "1.1.0",
      "path": "versions/1.1.0",
      "status": "stable"
    }
  ]
}
```

Versionen verwenden `MAJOR.MINOR.PATCH`, beispielsweise `1.0.0` oder `2.1.3`. `latest` muss auf eine vorhandene stabile Version verweisen.

## Inhalt einer Version

Jede Version ist vollständig und unabhängig aufgebaut:

```text
versions/<version>/
├── app/
├── assets/
├── config/
└── data/
```

### `app/`

Enthält den vollständigen ausführbaren App-Code. Der Standard-Einstiegspunkt ist:

```text
app/index.php
```

Weitere Dateien können logisch unter `src/`, `pages/`, `templates/` oder `includes/` liegen.

### `assets/`

Enthält unveränderliche Ressourcen der Version, beispielsweise:

```text
assets/
├── icon.svg
└── resources/
    ├── css/
    ├── js/
    ├── images/
    ├── fonts/
    └── languages/
```

### `config/`

Enthält genau eine AppConfig-Vorlage:

```text
config/<app-id>-config.json
```

Beispiel:

```text
config/hello-world-config.json
```

```json
{
  "title": "Hello World",
  "refreshSeconds": 0
}
```

Die Datei enthält nur sichere Standardwerte. Produktive Konfigurationen und Geheimnisse gehören nicht in dieses Repository.

### `data/`

Enthält die initiale AppData-Struktur für neue Instanzen:

```text
data/
├── rules.json
├── uploads/
│   └── .gitkeep
└── folders/
    └── .gitkeep
```

Der Ordner darf leere Ausgangsdateien, Standardregeln und technische Startdaten enthalten. Produktive Nutzerdaten, echte Uploads, Logs, Sessions und Zugangsschlüssel sind nicht erlaubt.

## Installation und Updates

Bei einer Neuinstallation:

1. wird die gewünschte Version aus `app.json` bestimmt;
2. wird `app/` als App-Code bereitgestellt;
3. werden `assets/` übernommen;
4. wird `<app-id>-config.json` als Instanzkonfiguration kopiert;
5. wird `data/` als neue AppData-Ausgangsstruktur kopiert.

Bei einem Update:

1. wird der neue Versionsordner separat geladen;
2. werden produktive AppConfig und AppData gesichert;
3. wird ausschließlich der versionsgebundene App-Code ersetzt;
4. werden neue notwendige Konfigurationsfelder und Datenstrukturen kontrolliert ergänzt;
5. bleiben vorhandene produktive Werte und Daten erhalten;
6. wird bei einem Fehler auf die vorherige Version zurückgerollt.

## Veröffentlichung

### Neue App

1. `apps/<app-id>/` anlegen.
2. `app.json` erstellen.
3. `versions/1.0.0/` mit `app/`, `assets/`, `config/` und `data/` anlegen.
4. App in `catalog.json` eintragen.
5. Struktur, JSON und PHP-Syntax prüfen.

### Neue Version

1. neuen Versionsordner anlegen;
2. vollständigen App-Code und alle versionsbezogenen Daten einfügen;
3. neue Version in `app.json` ergänzen;
4. `latest` bei einer stabilen Veröffentlichung aktualisieren;
5. vorherige Versionsordner unverändert erhalten.

## Verbindliche Regeln

1. Im Repository-Stamm liegen `README.md`, `LICENSE`, `catalog.json` und `apps/`.
2. `catalog.json` listet alle Apps.
3. Jeder App-Ordner enthält nur `app.json` und `versions/`.
4. `app.json` listet alle Versionen der App.
5. Jede Version enthält vollständig `app/`, `assets/`, `config/` und `data/`.
6. Die AppConfig-Vorlage heißt immer `<app-id>-config.json`.
7. Produktive AppConfig und AppData liegen außerhalb dieses Repositorys.
8. Geheimnisse werden nicht committed.
9. Veröffentlichte Versionsordner werden nicht verändert.
10. Alte Versionen bleiben für Rollback und Nachvollziehbarkeit erhalten.
