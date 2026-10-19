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

## 2. Verzeichnisstruktur

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

## 21. Releases

Empfohlene Struktur:

```text
apps/<app-id>/releases/<version>/
├── manifest.json
├── checksums.sha256
└── package.zip
```

Ein Releasepaket enthält ausschließlich die Dateien der betreffenden App-Version.

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
