# Supply Chain Beer Game — Live-Version

Diese Version ermöglicht ein gemeinsames Spiel auf mehreren Geräten. GitHub enthält den Quellcode und veröffentlicht die Web-App über GitHub Pages. Firebase Realtime Database speichert Spielstände und synchronisiert Änderungen live.

## Firebase einrichten

1. Lege in Firebase ein Projekt an und registriere eine Web-App.
2. Aktiviere unter **Authentication → Sign-in method** die anonyme Anmeldung.
3. Erstelle unter **Realtime Database** eine Datenbank. Für erste Tests kann der gesperrte Modus verwendet werden.
4. Kopiere die Web-App-Werte in `public/firebase-config.js`. Verwende die Datenbank-URL deiner Datenbank. Die Browser-Konfiguration enthält keine Admin-Schlüssel.
5. Veröffentliche die Zugriffsregeln aus `database.rules.json` im Firebase-Projekt oder mit Firebase CLI:

   ```sh
   npm install -g firebase-tools
   firebase login
   firebase use --add
   firebase deploy --only database
   ```

Die Regeln trennen Moderator, Spielende und Rollen: Spielende können die Daten und Bestellungen ihrer Rolle lesen; die Moderation kann alle Rollen lesen und die Woche auflösen. Rollen-Codes werden pro Spiel neu generiert.

## GitHub Pages

Lege diese Dateien in ein GitHub-Repository und pushe den Branch `main`. Der Workflow `.github/workflows/pages.yml` veröffentlicht den Inhalt von `public/`. Aktiviere im Repository unter **Settings → Pages** die Bereitstellung **GitHub Actions**. Danach erscheint die URL der App in der abgeschlossenen Workflow-Ausführung.

GitHub speichert den Quellcode; Firebase speichert die Spielstände. Für die Live-Synchronisierung müssen alle Geräte dieselbe veröffentlichte App öffnen und denselben Spielcode sowie den privaten Code ihrer Rolle verwenden.

## Spielablauf

- Die Moderation eröffnet eine neue Lieferkette und teilt Spielcode sowie je einen Rollen-Code privat mit den vier Gruppen.
- Gruppen treten auf beliebig vielen Geräten mit demselben Rollen-Code bei. Alle Geräte dieser Rolle sehen denselben Bestand und dieselbe eingehende Bestellung.
- Eine Gruppe reicht ihre Bestellmenge ein. Sie kann diese bis zur Auflösung aktualisieren; andere Rollen sehen die Menge nicht.
- Wenn für alle vier Rollen eine Bestellmenge vorliegt, löst die Moderation die Woche auf. Die aktualisierten Rollendaten erscheinen automatisch auf allen verbundenen Geräten.
- Der Spielstand bleibt in Firebase erhalten. Ein Browser kann die zuletzt geöffnete Sitzung nach einem Neuladen wieder aufnehmen.

## Lokaler Aufruf

Die Web-App nutzt ES-Module und muss über einen Webserver geöffnet werden, nicht direkt per `file://`. Mit Python geht das aus dem Projektordner so:

```sh
python3 -m http.server 8000
```

Dann `http://localhost:8000/public/` öffnen. Zum lokalen Deploy in GitHub Pages liegt die veröffentlichte Startseite unter `public/index.html`.

## Kosten und Schutz

Realtime Database und anonyme Anmeldung sind für eine Schulung mit einer einzelnen Lieferkette typischerweise sehr klein. Firebase-Verbrauch und Limits hängen aber vom Projekt ab; prüfe die Firebase-Konsole. Die Firebase-Web-Konfiguration ist im Browser sichtbar und darf keine Admin-Schlüssel enthalten. Die Datenbankregeln sind die Zugriffskontrolle und müssen im Firebase-Projekt veröffentlicht sein.

`public/offline-version.html` enthält weiterhin die vorherige Einzelgeräte-Version.
