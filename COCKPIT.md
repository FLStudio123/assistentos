# AssistentOS v1.2 – verbundene Quellen

## Was die App kann

- Todoist-Aufgaben und Projekte lesen; Aufgaben anlegen, Titel/Priorität/Fälligkeit ändern, in ein anderes Projekt verschieben und erledigen; Projekte anlegen und umbenennen. Alle Änderungen gehen direkt an Todoist. Originale werden nicht in Drive dupliziert. Unteraufgaben, Abschnitte und Etiketten werden angezeigt; zusätzliche Verwaltung und gelöschte/erledigte Aufgaben öffnest du in Todoist.
- Google-Kalender als Agenda für die nächsten 30 Tage anzeigen. Kalender auswählen, Termine im Original öffnen. Ausschließlich Lesezugriff.
- Private native Google-Tabellen mit Finanzen und Verträgen anzeigen, einschließlich sichtbarer Tabellenblätter. Originale bleiben unverändert. Anzeige formatierter Werte, jeweils 200 Zeilen, weitere Blöcke auf Abruf, Spalten A bis AZ. Keine automatisch erfundenen Summen, Zahlungstermine oder Währungsumrechnungen. Horizontales Scrollen auf dem iPhone.
- Lokale bisherige App-Aufgaben/Projekte bleiben separat erhalten; sie werden nicht automatisch nach Todoist übertragen. Der obere Hinzufügen-Button schreibt bei verbundener Todoist-Sitzung direkt zu Todoist. Meilensteine bleiben Teil der lokalen Projektverwaltung.
- Die optionale Drive-App-Datensynchronisierung gleicht weiterhin lokale Aufgaben, Projekte, Meilensteine und Wissenslinks zwischen Geräten ab. Sie ist unabhängig vom sichtbaren Drive-Projektordner. Siehe GOOGLE-DRIVE-SYNC.md.

## Todoist verbinden – auf jedem Gerät

1. Todoist → Einstellungen → Integrationen → Entwickler → API-Token. Falls der Eintrag in der mobilen App fehlt, Todoist im Browser öffnen.
2. AssistentOS → Einstellungen → Dein verbundenes Cockpit → Token direkt ins Passwortfeld einfügen → Todoist verbinden. Den Token niemals in einen Chat, eine öffentliche Datei oder das GitHub-Repository kopieren.
3. Die Aufgaben- und Projektseiten zeigen die Originaldaten. P1 ist die höchste Priorität. Die Todoist-API verwendet dafür intern den Wert 4.

Der Token bleibt ausschließlich im Arbeitsspeicher, nicht im App-Backup, Drive oder lokalen Gerätespeicher. Nach Schließen/Neuladen der App erneut verbinden. Der persönliche Token erlaubt Zugriff auf dein Todoist-Konto; bei Verlust in Todoist zurücksetzen. Keine serverseitige Speicherung oder kostenpflichtige Automatisierung nötig.

## Google vorbereiten

Dass die Cloud-Konsole Zwei-Faktor-Authentifizierung verlangt, ist eine Vorgabe von Google. Passwörter und Bestätigungscodes gibst du selbst bei Google ein; AssistentOS benötigt sie nicht.

1. Im eigenen Google-Cloud-Projekt die **Google Calendar API**, **Google Sheets API** und für lokale App-Synchronisierung die **Google Drive API** aktivieren. Für diese Integration keine kostenpflichtigen Cloud-Dienste buchen und kein Upgrade starten.
2. Google Auth Platform / Branding: App-Name AssistentOS und eigene Kontaktadresse eintragen. Extern, zunächst Testmodus, gewünschte eigene Google-Konten als Testnutzer ergänzen. Kalender und Tabellen können unterschiedliche Konto-Freigaben besitzen; das angemeldete Konto muss Zugriff auf alle gewünschten Quellen haben. Kalender/Tabellen keinesfalls öffentlich machen.
3. OAuth-Client vom Typ Webanwendung erstellen. Autorisierter JavaScript-Ursprung: `https://flstudio123.github.io` (ohne App-Pfad). Lokale Vorschau nur bei Bedarf zusätzlich `http://127.0.0.1:4174`. Für den Browser-Tokenfluss keine Client-Secrets in die App einbauen.
4. Unter Datenzugriff die benötigten Bereiche zulassen:
   - `https://www.googleapis.com/auth/calendar.readonly`
   - `https://www.googleapis.com/auth/spreadsheets.readonly`
   - separat für Drive-Synchronisierung `https://www.googleapis.com/auth/drive.appdata`
5. Die öffentliche Client-ID in den App-Einstellungen eintragen. Ein bereits dort gespeicherter Drive-Client kann wiederverwendet werden. Die öffentliche ID ist kein Passwort.
6. Die privaten Quelladressen zu Finanzen und Verträgen eingeben, **Quelladressen speichern**. Sie werden ausschließlich auf dem aktuellen Gerät gespeichert.
7. **Google-Anmeldung vorbereiten**, dann **Google lesend verbinden**. Bei Google das richtige Konto wählen und die beiden Lesefreigaben selbst bestätigen. Ein Testprojekt kann eine Google-Test-/Ungeprüft-Meldung zeigen; Identität und angeforderte Rechte prüfen. Keine Sicherheitswarnungen automatisch umgehen.

Google-Leserechte gelten für Kalender bzw. Tabellen des angemeldeten Kontos; die Tabellenberechtigung ist nicht auf die zwei eingetragenen Dateien beschränkt. AssistentOS ruft nur eingetragene Tabellen und den gewählten Kalender ab. Zugangstoken und geladene private Daten bleiben ausschließlich im Arbeitsspeicher. Lokale Quelladressen sind kein Bestandteil des bisherigen JSON-Backups und müssen pro Gerät eingerichtet werden.

## Alltag und Grenzen

Bei sichtbarer geöffneter App wird jede Minute aktualisiert, außerdem beim Wiederöffnen einer sichtbaren Sitzung oder nach Rückkehr der Verbindung. Änderungen werden direkt gespeichert; ein ausgeschalteter PC ist kein Hindernis. Externe Daten benötigen Internet. Bei geschlossener iPhone-App gibt es keinen verlässlichen Hintergrundabgleich. Google-Token laufen ab; die Oberfläche meldet das und fordert eine neue Verbindung an. Ohne Verbindung zeigt das Cockpit keine erfundenen Ersatzdaten.

Eine fehlgeschlagene Schreibanfrage kann trotzdem bereits bei Todoist angekommen sein. Die App wiederholt sie nicht automatisch: zunächst aktualisieren und den tatsächlichen Stand prüfen. Vor Änderungen an vorhandenen Aufgaben werden die betroffenen Felder erneut gelesen. Erkannte zwischenzeitliche Änderungen blockieren das Speichern; der Zeitraum zwischen Prüfung und Schreibanfrage lässt sich ohne serverseitige Versionsbedingung nicht vollständig absichern.

Zum Trennen: Einstellungen → Verbindungen trennen. Das entfernt die Cockpit-Token und geladenen Inhalte aus dieser Sitzung, widerruft aber keine Google-Kontofreigabe. Eine Freigabe kann zusätzlich im Google-Konto entfernt werden. Drive-Synchronisierung hat einen eigenen Trennen-Button.

## Prüfung

Automatisierte Tests prüfen unter anderem Todoist-Prioritäten, Folgeseiten, wiederkehrende Aufgaben, Konfliktprüfung, Kalenderdaten, Ablauf von Google-Sitzungen, Tabellenbereiche und das Vermeiden lokaler Speicherung von Token/Finanzwerten. Der echte Zugriff mit den persönlichen Konten und ein Abgleich zwischen PC und iPhone müssen nach der Anmeldung separat bestätigt werden. Ein erfolgreicher Programmtest ersetzt diese Prüfung nicht.

Offizielle technische Referenzen:
- https://developer.todoist.com/api/v1/
- https://github.com/Doist/todoist-cli/blob/main/CODEBASE.md (Prioritätsabbildung)
- https://developers.google.com/workspace/calendar/api/quickstart/js
- https://developers.google.com/workspace/sheets/api/scopes
- https://developers.google.com/identity/oauth2/web/guides/use-token-model
