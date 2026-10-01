# Abfragen und Grenzen

Diese Referenz beschreibt den geprüften systolics-MCP-Vertrag. Das veröffentlichte Tool-Schema hat Vorrang. Ermittle verfügbare Modelle und Member zur Laufzeit.

## Aggregierte Abfragen

`query_cube` und `get_cube_query_sql` erwarten:

- `cube`: bestätigter Name aus dem aktiven Katalog.
- `measures`: ein bis fünf vollständige Member-Namen desselben Cubes.
- `timeDimension`: vollständiger Name einer Zeitdimension dieses Cubes.
- `dateRange`: zwei Grenzen im Format `YYYY-MM-DD`, `this_quarter` oder `quarter_to_date`.
- Optional `filters`: bis zu zehn Filter mit `member`, `operator` (`equals` oder `notEquals`) und `values` als Liste von Zeichenketten. Filterdimensionen müssen zum Cube gehören; maximal 20 Werte je Filter.

Die Abfrage liefert ein Gesamtaggregat. Freie Gruppierung, Sortierung, Rohdatenabfragen und beliebige SQL-Ausführung werden nicht unterstützt. Erfinde keine Parameter wie `granularity`, `groupBy` oder `year_to_date`.

Für wenige Monatswerte sind getrennte Abfragen mit expliziten Monatsgrenzen möglich. Für einen Distinct-Gesamtwert stelle eine zusätzliche Abfrage über den Gesamtzeitraum. Bei großen Zeitreihen oder beliebigen Ranglisten erkläre die Grenze; starte keine unbeschränkte Folge von Einzelabfragen.

Ein Cube ohne Zeitdimension kann mit diesem Vertrag nicht abgefragt werden. Verwende keine sachfremde Datumsdimension, nur um das Schema zu erfüllen. Cubeübergreifende Auswertungen benötigen ein geeignetes aktives Modell oder eine tatsächlich unterstützte Schnittstelle.

## Datenstrukturen

`get_data_entities` akzeptiert optional `query`, `limit` und `offset`. Das Limit beträgt standardmäßig 25 und höchstens 100. Verwende für weitere Seiten den zurückgegebenen `nextOffset`, solange weitere Einträge für die Frage benötigt werden.

`get_data_entity_details` benötigt `entity`: einen genauen Namen oder eine ID aus dem Katalog. Das Ergebnis beschreibt die Entität und ihre Spalten; es liefert keine einzelnen Datensätze. Falls die Berechtigung fehlt, erkläre den fehlenden Metadatenzugriff und rate nicht zu einem anderen Transportweg.
