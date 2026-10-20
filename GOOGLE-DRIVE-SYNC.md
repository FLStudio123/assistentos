# Google-Drive-Synchronisierung für AssistentOS

## Stand

Die App enthält eine optionale direkte Drive-Synchronisierung. Die Google-OAuth-Web-Client-ID muss einmalig in deinem Google-Konto eingerichtet und in den App-Einstellungen eingetragen werden. Ohne sie bleibt die App vollständig lokal nutzbar. Die Drive-Verbindung von ChatGPT ist unabhängig davon und kann nicht für eine öffentliche PWA wiederverwendet werden.

Kein eigener Server, kein Client-Secret, kein OpenAI-API-Schlüssel, keine zusätzlichen kostenpflichtigen Dienste. Googles Standardnutzung der Drive API ist derzeit ohne zusätzliche Kosten; für sehr hohe Kontingentüberschreitungen sind Änderungen angekündigt. Kein Abrechnungskonto aktivieren, keine Test-Abos starten und keine Kontingenterhöhung buchen. Dein vorhandenes Drive-Speicherkontingent gilt weiterhin.

## Einmalige Google-Einrichtung

1. Öffne https://console.cloud.google.com/ im gewünschten Google-Konto. Erstelle ein Projekt „AssistentOS Sync“, ohne Abrechnungskonto. Falls Google Vertragsbedingungen oder zusätzliche Berechtigungen fordert, prüfe und bestätige diese selbst. Aktiviere unter **APIs und Dienste → Bibliothek** die **Google Drive API**.
2. Richte unter **Google Auth Platform** die App ein: Name „AssistentOS“, eigene Support-/Kontaktadresse, Zielgruppe bei einem privaten Gmail-Konto „Extern“. Belasse den Status zunächst auf „Testing“ und füge deine eigene Google-Adresse als Testnutzer hinzu. Unter Datenzugriff nur `https://www.googleapis.com/auth/drive.appdata` hinzufügen. Dieser Umfang ist laut Google nicht sensibel und erlaubt ausschließlich den ausgeblendeten Appbereich. Keine vollständige Drive-Freigabe, keinen Service-Account und keine geheimen Schlüssel einrichten.
3. Erstelle unter **Clients → Client erstellen** eine OAuth-Client-ID vom Typ **Webanwendung**. Autorisierter JavaScript-Ursprung: `https://flstudio123.github.io` (ohne `/assistentos/`, ohne abschließenden Pfad). Für lokale Entwicklung optional `http://127.0.0.1:4174` ergänzen. Für den hier verwendeten Popup-Tokenablauf ist keine Weiterleitungsadresse nötig. Kopiere nur die Client-ID mit Endung `.apps.googleusercontent.com`.

Die Bezeichnungen in der Google-Konsole können sich ändern. Falls eine App-Homepage, Datenschutzerklärung oder Domainbestätigung vorgeschrieben wird, keine fremde Domain als eigene ausgeben. Erst den konkreten Dialog prüfen. Für die Nutzung durch weitere Personen und einen Produktionsstatus können zusätzliche OAuth-Anforderungen gelten; diese Einrichtung ist für deine private Nutzung.

## Auf beiden Geräten aktivieren

1. Zuerst auf PC und iPhone unter **Einstellungen → Backup exportieren** je eine aktuelle JSON-Sicherung speichern. Die neue App übernimmt vorhandene Daten; die unabhängigen Sicherungen schützen zusätzlich vor Fehlbedienung.
2. Im aktualisierten AssistentOS unter **Einstellungen → Google Drive · Gerätesynchronisierung** dieselbe Client-ID eingeben. **Google-Anmeldung vorbereiten**, dann **Verbinden & synchronisieren** wählen. Im Popup auf beiden Geräten dasselbe Google-Konto auswählen und ausschließlich den angebotenen App-Datenzugriff erlauben.
3. Zuerst den PC vollständig synchronisieren, dann das iPhone verbinden. Bestehende Einträge werden anhand ihrer IDs zusammengeführt. Inhaltlich gleiche, zuvor separat angelegte Einträge haben unterschiedliche IDs und können deshalb als zwei Einträge erscheinen. Die App behauptet keine automatische Duplikaterkennung.

Nach einem Neustart oder nach Ablauf des kurzlebigen Tokens ist eine erneute Verbindung nötig. Der Token bleibt ausschließlich im Arbeitsspeicher und wird nie in localStorage, Backups, Quellcode oder Google-Datenpaketen gespeichert. Die Client-ID ist eine öffentliche App-Kennung. Ändere sie nicht zwischen Geräten; ein anderes OAuth-Projekt hat einen anderen App-Datenbereich.

Falls die Anmeldung in der installierten iPhone-PWA scheitert, bitte die genaue Fehlermeldung prüfen. Ein erfolgreicher Login in Safari überträgt den Token nicht automatisch in die separat installierte PWA. Inhalteblocker/Popup-Sperren können die Anmeldung beeinflussen. Es wurde noch kein echter OAuth-/iPhone-Abgleich bestätigt.

## Verhalten und Konflikte

- Lokal speichern funktioniert auch offline. Während die App sichtbar geöffnet und verbunden ist, erfolgt alle 30 Sekunden ein Abgleich; zusätzlich beim Wiederöffnen der sichtbaren App und bei Rückkehr der Internetverbindung. **Jetzt synchronisieren** startet ihn manuell. Zum Übertragen muss Internet vorhanden sein; der PC muss dafür nicht laufen, wenn das iPhone selbst verbunden ist. Eine geschlossene iPhone-PWA hat keine garantierte Hintergrundausführung.
- Drive enthält unveränderliche Änderungspakete statt einer gemeinsam überschriebenen JSON-Datei. Gleichzeitige Uploads können deshalb nebeneinander bestehen. Jeder Eintrag hat eine ID, jede Änderung eine Gerätekennung und eine logische Reihenfolge. Mehrere lokale Tabs werden beim Abgleich nach Möglichkeit über Web Locks koordiniert; Änderungen eines inzwischen aktualisierten Tabs bleiben beim Speichern anderer Felder erhalten.
- Aufgaben, Projektfelder, einzelne Meilensteine, Links und die ChatGPT-Verknüpfung werden feldweise zusammengeführt. Bei verschiedenen gleichzeitigen Werten desselben Feldes zeigt die App beide Fassungen zur Auswahl und wartet mit dem Upload. Die Anzeige verwendet bis zur Entscheidung eine deterministische vorläufige Fassung; sie ist kein endgültiger Vorrang eines bestimmten Geräts. Nach Auswahl **Jetzt synchronisieren** drücken.
- Löschungen werden als Markierung gespeichert. Ein altes Gerät lässt gelöschte Einträge dadurch nicht einfach wieder auftauchen. Löschen hat gegenüber parallelem Bearbeiten anderer Felder Vorrang; die ursprünglichen Bearbeitungen bleiben im vollständigen Sync-Verlauf erhalten. Aufgaben aus gelöschten Projekten bleiben ohne Projektzuordnung erhalten. Bei mehr als drei zusammengeführten Tagesprioritäten zeigt die App deterministisch höchstens drei an; die übrigen Markierungen bleiben im Verlauf erhalten.
- Ein späterer Abgleich kann neue gleichzeitige Änderungen finden. „Synchronisiert“ bestätigt den abgeschlossenen Abgleich und erfolgreiche Uploads, keine permanente Echtzeitverbindung. Bei Fehlern erscheint kein neuer erfolgreicher Zeitstempel. Lokale Änderungen bleiben ausstehend. Nach Verbindungsabbrüchen dürfen Pakete erneut übertragen werden; ihre Änderungs-IDs machen die Zusammenführung idempotent.
- Der erste verbundene Google-Account wird für diesen Gerätespeicher festgehalten. Bei einem anderen Konto bricht die App vor dem Datentransfer ab. Für einen bewussten Kontowechsel ein getrenntes Browserprofil verwenden; nicht ungeprüft lokale Daten löschen.

## Daten, Backups und Grenzen

Der private `appDataFolder` ist in der normalen Drive-Oberfläche unsichtbar und von anderen Drive-Apps getrennt. Die App schreibt nicht in deinen sichtbaren Ordner „AssistentOS“. ChatGPT kann diese ausgeblendeten App-Daten daher nicht automatisch lesen. JSON-Exporte können bei Bedarf im sichtbaren Drive-Ordner gesichert werden.

Normale JSON-Backups sichern den aktuellen sichtbaren Datenbestand. **Vollständigen Sync-Verlauf sichern** exportiert zusätzlich Konfliktwerte, Löschmarkierungen und Gerätemetadaten, jedoch keine OAuth-Tokens. Der normale Import verwendet den sichtbaren Datenbestand einer Backup-Datei als bewusste lokale Ersetzung; er importiert keine fremden Kontobindungen oder Sync-Metadaten. Eine historische Konfliktfassung kann daraus bei Bedarf gezielt wiederhergestellt werden, nicht automatisch durch Import sämtlicher alter Operationen.

Die Historie wird nicht automatisch gelöscht oder verdichtet. Obergrenzen: 100.000 Änderungen, 10.000 Drive-Pakete, höchstens 10 MB je Paket und 5 MB für den normalen Backup-Import. Vor Erreichen dieser Grenzen ist eine gezielte Archivierung nötig; die App stoppt statt Daten still abzuschneiden. Daten sind durch Google-Kontoberechtigungen geschützt, aber nicht zusätzlich Ende-zu-Ende-verschlüsselt. Google-Zugriff in den Kontoeinstellungen entfernen löscht nicht automatisch lokale Einträge; „Trennen“ stoppt den künftigen Abgleich auf diesem Gerät und behält alle Daten. Bei einem bereits laufenden Abgleich ist Trennen deaktiviert.

## Abnahmetest nach Freigabe

1. Auf PC und iPhone sichern, verbinden und den ersten vollständigen Abgleich abwarten.
2. Am PC eine neue Aufgabe erstellen, synchronisieren und auf dem iPhone abgleichen. Dort erledigen und zurück zum PC abgleichen. Auch Projekt, Meilenstein und Wissenslink testen.
3. Beide Geräte offline bearbeiten: verschiedene Felder derselben Aufgabe ändern. Wieder online auf beiden abgleichen; beide Änderungen müssen erhalten bleiben. Danach dasselbe Feld unterschiedlich ändern und die angebotene Konfliktauswahl testen. Eine Löschung vom anderen Gerät darf nicht durch einen veralteten Stand zurückkommen.

## Offizielle Referenzen

- [Privater Drive-Appbereich und Berechtigungsumfang](https://developers.google.com/workspace/drive/api/guides/appdata)
- [Google-Tokenmodell und erneute Autorisierung](https://developers.google.com/identity/oauth2/web/guides/use-token-model)
- [OAuth-Client-ID erstellen](https://developers.google.com/workspace/guides/create-credentials)
- [Aktuelle Kontingente und Kosten](https://developers.google.com/workspace/drive/api/guides/limits)
