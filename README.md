# systolics

![systolics](assets/icon.png)

Das systolics-Plugin verbindet Claude mit den anonymisierten Analysedaten Ihrer Organisation. Der enthaltene Analytics-Skill unterstützt die Auswahl passender Modelle und Kennzahlen, nachvollziehbare Periodenvergleiche und die Erklärung von Berechnungen und Datenstrukturen. Die Kategorie ist Data & Analytics. Die Analysedaten sind bereits in systolics anonymisiert, bevor der MCP-Connector darauf zugreift.

## Voraussetzungen

Die Nutzung setzt einen bestehenden systolics-Lizenzvertrag voraus, der Sie selbst oder Ihre Nutzung als berechtigter Nutzer Ihrer Organisation umfasst. Zusätzlich benötigen Sie ein systolics-Konto mit Zugang zur gewünschten Organisation und den jeweiligen Leserechten. Welche Kennzahlen verfügbar sind, hängt von den aktiven Analysemodellen und Ihren Berechtigungen ab. Der Connector bietet zehn lesende Tools.

Das Plugin steht unter einer [proprietären Lizenz](LICENSE). Maßgeblich sind der bestehende Lizenzvertrag und die darin wirksam einbezogenen [systolics-AGB](https://systolics.de/agb). Die Installation des Plugins begründet keinen eigenen Anspruch auf Nutzung der systolics-Dienste.

## In Claude verbinden

1. Fügen Sie das Plugin in Claude hinzu. Für einen lokalen Test öffnen Sie **Customize → Plugins → Add → Upload plugin** und laden das ZIP des Plugin-Ordners hoch.
2. Öffnen Sie den Tab **Connectors** des Plugins und verbinden Sie **systolics**.
3. Melden Sie sich auf `auth.services.systolics.de` mit Ihrem systolics-Konto an und wählen Sie Ihre Organisation.
4. Wählen Sie **Cube-Daten abfragen** für Analytics und **Datenstrukturen einsehen** für Tabellen-, Spalten- und View-Metadaten. Bestätigen Sie den Zugriff für Claude.
5. Kehren Sie zu Claude zurück und beginnen Sie mit einer Frage zu Ihren Daten. Bei Team- und Enterprise-Konten kann zunächst ein Owner den Connector für die Organisation hinzufügen müssen.

Der verwendete MCP-Endpunkt ist `https://mcp.services.systolics.de/mcp`. Falls Sie den systolics-Connector bereits separat verwenden, nutzen Sie dieselbe URL. Zugangsdaten werden ausschließlich im Anmeldefenster eingegeben.

In Claude Code lässt sich der entpackte Plugin-Ordner mit `claude --plugin-dir <Pfad-zum-Plugin>` laden. Mit `/mcp` prüfen Sie den Verbindungsstatus. Der Skill heißt `/systolics:systolics-analytics`.

## Beispielanfragen

- „Welche Analysemodelle und Kennzahlen stehen mir zur Verfügung?“
- „Welche Zeitdimension passt für diese Kennzahl zu meiner Frage?“
- „Vergleiche die ausgewählte Kennzahl für das zweite und dritte Quartal 2026.“
- „Wie wird die Kennzahl berechnet? Zeige auch das SQL der Abfrage.“
- „Welche Datenstrukturen gibt es und welche Spalten enthält diese Tabelle?“

Claude verwendet den aktiven Modellkatalog und klärt wesentliche Mehrdeutigkeiten vor der Abfrage. Antworten sollen Ergebnis, Einheit, Zeitraum und relevante Filter erkennen lassen. Der Skill ist eine Arbeitsanleitung; die tatsächlich verfügbaren Tools und Ihre Berechtigungen bestimmen den möglichen Zugriff.

## Funktionen und Grenzen

Die Tools liefern Organisations- und Benutzerkontext, Analysemodelle, Kennzahlen, Dimensionen, aggregierte Ergebnisse, Modelldefinitionen, erzeugtes Abfrage-SQL und Datenstruktur-Metadaten. Eine Datenabfrage verwendet ein Modell, ein bis fünf Kennzahlen, eine Zeitdimension und einen Zeitraum. Die Zeitzone ist Europe/Berlin.

Die Tools ändern keine Geschäftsdaten. Freie SQL-Ausführung, beliebige Gruppierungen und das Abrufen einzelner Rohdatensätze gehören nicht zum Funktionsumfang. Die Datenstruktur-Tools lesen Beschreibungen und Spalteninformationen. Verfügbare Metadaten können SQL-Ausdrücke und View-Definitionen enthalten.

## Daten und Zugriff

Das Plugin enthält Anweisungen und die Verbindungskonfiguration zum systolics-MCP. Bei Tool-Aufrufen übermittelt Claude die benötigten Argumente, beispielsweise Kennzahlen, Datumsgrenzen und Filterwerte, an den systolics-Dienst. Dieser gibt die autorisierten Ergebnisse an Claude zurück. Die Anmeldung erfolgt über den systolics-Auth-Service mit OAuth.

Zusätzlich zu anonymisierten Analysedaten können die Kontext-Tools Organisationsinformationen sowie Name und E-Mail des angemeldeten Benutzers zurückgeben. Der Analytics-Skill fragt Benutzerinformationen nur ab, wenn die konkrete Aufgabe sie benötigt. Die Plugin-Dateien enthalten keine eigene lokale Datenspeicherung und keine zusätzlichen Analyse- oder Telemetrieprogramme. Für die Verarbeitung in den verbundenen Diensten gelten deren jeweilige Bedingungen und Datenschutzhinweise.

## Hilfe

Bei fehlenden Tools verbinden Sie systolics im Tab **Connectors** erneut. Bei fehlender Datenstruktur-Berechtigung autorisieren Sie die Verbindung neu und wählen **Datenstrukturen einsehen**. Fehlt diese Auswahl oder ist sie nicht freigegeben, prüfen Sie Ihre Organisation und Kontoberechtigungen mit dem zuständigen Administrator.

Anbieter: [systolics GmbH](https://systolics.de/impressum/). Datenschutz: [systolics Datenschutzhinweise](https://systolics.de/dse). Kontakt: [hi@systolics.de](mailto:hi@systolics.de).

Einrichtung und Plattformfunktionen: [Anthropic Plugin-Dokumentation](https://claude.com/docs/plugins/build).
