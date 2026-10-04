# Tools und Berechtigungen

Der systolics-MCP veröffentlicht die folgenden technischen Namen mit deutschen Anzeigetiteln. Host-Präfixe können abweichen. Alle zehn Tools sind lesend; die tatsächliche Ausführung hängt vom authentifizierten Kontext ab.

| Technischer Name | Anzeige | Verwendung |
| --- | --- | --- |
| `get_organization_information` | Organisationsinformationen abrufen | Aktive Organisation und Rechte prüfen |
| `get_user_information` | Benutzerinformationen abrufen | Benutzeridentität prüfen, wenn benötigt |
| `get_cubes` | Analysemodelle auflisten | Modelle entdecken |
| `get_cube_details` | Analysemodell anzeigen | Aktive Modelldefinition erklären |
| `get_measures` | Kennzahlen auflisten | Kennzahlen und Aggregation prüfen |
| `get_dimensions` | Dimensionen auflisten | Gruppierungen, Zeitdimensionen und Filter bestimmen |
| `query_cube` | Cube-Daten abfragen | Kennzahlen, Aufteilungen, Zeitreihen und Ranglisten im Cube-Abfrageformat lesen |
| `get_cube_query_sql` | Abfrage-SQL anzeigen | SQL der gleichen Abfrage gegen die Quelltabellen erzeugen |
| `get_data_entities` | Datenstrukturen auflisten | Tabellen und Views entdecken |
| `get_data_entity_details` | Datenstruktur anzeigen | Spalten und Definition einer Entität erklären |

## Freigaben

Die OAuth-Freigabe „Cube-Daten abfragen“ (`cubeDataRead`) gewährt bei passenden Kontorechten `analytics-cube-read`. Die sechs Cube-Tools akzeptieren diese Leseberechtigung oder die bestehende Berechtigung `analytics-cube-write`.

„Datenstrukturen einsehen“ (`metaDataRead`) gewährt `data-dwh-meta-read`. Die beiden Datenstruktur-Tools akzeptieren diese Leseberechtigung oder die bestehende Berechtigung `data-dwh-meta-write`. Für diesen Workflow genügen Lesefreigaben.

Die beiden Kontext-Tools benötigen den angemeldeten Kontext, aber keine dieser zusätzlichen Tool-Leseberechtigungen. Gruppenbezeichnungen sind Auswahloptionen im Login; die Rechte im Token werden vom Auth-Service bestimmt. Ein Skill oder ein Argument im Tool-Aufruf kann keine Rechte hinzufügen.

Nach Änderung der Freigaben kann eine erneute Autorisierung der Verbindung erforderlich sein. Ein altes Refresh-Token erhält neue Freigaben nicht automatisch.
