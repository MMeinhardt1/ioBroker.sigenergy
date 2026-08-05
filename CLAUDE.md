# CLAUDE.md

Projektregeln für `ioBroker.sigenergy` — ioBroker-Adapter für Sigenergy-Solaranlagen via Modbus TCP/RTU.

## Mindestanforderungen (gültig bis auf Widerruf)

Diese Versionen sind die verbindliche Untergrenze. Sie dürfen **nicht** unterschritten werden;
Code, Dependencies und CI dürfen sich darauf verlassen.

| Komponente | Minimum | Wo gepflegt |
|------------|---------|-------------|
| js-controller | `>=6.0.11` | `io-package.json` → `common.dependencies` |
| admin | `>=8.0.0` | `io-package.json` → `common.globalDependencies` |
| Node.js | `>=22` | `package.json` → `engines.node`, `@tsconfig/node22`, CI-Matrix |

Bei jeder Änderung an diesen Werten alle drei Stellen plus die Requirements-Tabelle im
[README.md](README.md) gemeinsam aktualisieren — sie müssen konsistent bleiben.

Weil admin >= 8 vorausgesetzt wird, sind ältere Admin-Kompatibilitäts-Workarounds nicht mehr nötig
und dürfen entfernt werden.

## Admin-UI: React 19 + MUI 9

Ab `iobroker.admin` > 8.0.0 rendert der Admin mit **React 19 und MUI 9**. Alles, was der Adapter zur
Admin-UI beiträgt, muss dazu passen — bei Bedarf wird angepasst, nicht umgangen.

Aktueller Stand: Der Adapter hat **keinen eigenen React-/MUI-Code**. Die Konfiguration läuft
vollständig über `adminUI.config: "json"` mit [admin/jsonConfig.json](admin/jsonConfig.json), das der
Admin selbst rendert. Es gibt keine `.jsx`/`.tsx`-Dateien und kein `src-admin`.

Daraus folgt:

- Konfiguration bevorzugt weiterhin über `jsonConfig` lösen, nicht über eigene React-Komponenten.
- Nur dokumentierte `jsonConfig`-Typen und -Attribute verwenden. MUI-durchgereichte Props
  (`variant`, `color`, `style`) müssen unter MUI 9 gültig sein.
- Sollte doch eigener Admin-Code entstehen, gilt zwingend React 19 + MUI 9 (kein `@mui/styles`,
  keine Legacy-Lifecycle-Methoden, keine `defaultProps` an Funktionskomponenten).

## Admin-Übersetzungen

`admin/jsonConfig.json` läuft mit `"i18n": true`. Jeder neue `label`-, `help`- oder `text`-String
ist ein englischer Lookup-Key und muss in **allen 11** Sprachdateien unter [admin/i18n/](admin/i18n/)
ergänzt werden: `de, en, es, fr, it, nl, pl, pt, ru, uk, zh-cn`.

Die Dateien sind alphabetisch sortiert (mit angehängten Nachträgen am Ende) und nutzen 2 Space
Einrückung — beim Ergänzen die bestehende Reihenfolge und Formatierung beibehalten.
Wird ein String inhaltlich geändert, ist der alte Key zu ersetzen und nicht zusätzlich stehenzulassen.

## Konsistenzregeln

- Neue Config-Option: Key gehört in `io-package.json` → `native`, in `admin/jsonConfig.json` **und**
  wird im Code ausgewertet. Alle drei Stellen prüfen.
- Neuer State: Objektdefinition in [main.js](main.js) (`id`, `name`, `type`, `unit`, `role`) und das
  Schreiben im Statistik-Mapping müssen zusammenpassen.
- Version steht in `package.json` **und** `io-package.json` → `common.version`; dazu ein Eintrag in
  `io-package.json` → `common.news` (en + de) und im Changelog des README.

## Kommandos

```bash
npm run lint      # eslint (@iobroker/eslint-config)
npm run check     # tsc Typecheck via JSDoc
npm test          # package + unit + integration
```

Vor dem Commit müssen `lint` und `check` warnungsfrei durchlaufen.
