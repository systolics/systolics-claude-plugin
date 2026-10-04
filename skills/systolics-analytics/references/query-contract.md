# Abfragen und Grenzen

Diese Referenz beschreibt den systolics-MCP-Vertrag. Das veröffentlichte Tool-Schema hat Vorrang. Ermittle verfügbare Modelle und Member zur Laufzeit.

## Cube-Abfrageformat

`query_cube` und `get_cube_query_sql` nehmen das Abfrageformat von Cube an. Alle Felder sind optional; eine Abfrage braucht mindestens eine Kennzahl, Dimension, Zeitdimension oder ein Segment.

- `measures`: vollständige Member-Namen der Kennzahlen, beispielsweise `Leistungen.Geldwert`. Keine feste Höchstzahl.
- `dimensions`: Dimensionen zum Gruppieren. Das Ergebnis enthält eine Zeile je Wertkombination.
- `timeDimensions`: Liste von Objekten mit `dimension` (vollständiger Name einer Zeitdimension) und optional `dateRange`, `granularity` oder `compareDateRange`.
  - `dateRange`: `["YYYY-MM-DD", "YYYY-MM-DD"]`, ein einzelner Tag `["YYYY-MM-DD"]`, `this_quarter`, `quarter_to_date` oder ein relativer Zeitraum von Cube wie `"last 30 days"`, `"last month"` oder `"this year"`. Ohne `dateRange` umfasst die Abfrage alles, was das Modell enthält.
  - `granularity`: `day`, `week`, `month`, `quarter`, `year` (auch `hour`, `minute`, `second`) oder eine im Modell definierte eigene Granularität. Ergibt eine Zeitreihe mit einer Zeile je Periode. Ohne Granularität gibt es eine Zeile für den gesamten Zeitraum.
  - `compareDateRange`: mehrere Zeiträume derselben Zeitdimension anstelle von `dateRange`, etwa `[["2026-04-01", "2026-06-30"], ["2026-07-01", "2026-09-30"]]`. Das Ergebnis enthält dann ein Teilergebnis je Zeitraum.
- `filters`: Bedingungen `{ "member", "operator", "values" }` auf Dimensionen oder Kennzahlen. Operatoren: `equals`, `notEquals`, `contains`, `notContains`, `startsWith`, `notStartsWith`, `endsWith`, `notEndsWith`, `gt`, `gte`, `lt`, `lte`, `set`, `notSet`, `inDateRange`, `notInDateRange`, `beforeDate`, `beforeOrOnDate`, `afterDate`, `afterOrOnDate`, `measureFilter`. Mit `{ "and": [...] }` und `{ "or": [...] }` lassen sich Bedingungen verschachteln. `values` sind Zeichenketten, Zahlen oder Wahrheitswerte; `set` und `notSet` brauchen keine Werte.
- `segments`: vollständige Namen im Modell definierter Segmente, siehe `get_cube_details`.
- `order`: `{ "Leistungen.Geldwert": "desc" }` oder `[["Leistungen.Geldwert", "desc"]]`. Eine Zeitdimension kann mit Granularität sortiert werden, etwa `Leistungen.LeistungDatum.month`.
- `limit` und `offset`: Zeilenzahl und Startposition für Ranglisten und zum Blättern. `limit: 0` liefert keine Zeilen, etwa zusammen mit `total: true` für eine reine Zählung.
- `ungrouped: true`: einzelne Zeilen des Modells statt Aggregation.
- `total: true`: liefert zusätzlich die Gesamtzahl der Ergebniszeilen.
- `timezone`: IANA-Zeitzone, Standard `Europe/Berlin`.
- `responseFormat: "compact"`: liefert `data` als `{ members, dataset }`, die Member-Namen einmal und danach je Zeile eine Werteliste. Spart bei vielen Zeilen Umfang.
- `renewQuery: true`: rechnet neu, statt den Cube-Cache zu verwenden. Nur einsetzen, wenn ein frischer Stand ausdrücklich nötig ist.

Member mehrerer über Joins verbundener Cubes dürfen in einer Abfrage kombiniert werden. Ein Feld `cube` ist nicht nötig; der Cube steckt im Member-Namen. Die Plattform prüft jeden genannten Member gegen den sichtbaren Katalog und meldet unbekannte Namen mit Fehlercode zurück (`UNKNOWN_MEASURE`, `UNKNOWN_DIMENSION`, `UNKNOWN_TIME_DIMENSION`, `UNKNOWN_SEGMENT`, `UNKNOWN_FILTER_MEMBER`, `UNKNOWN_ORDER_MEMBER`). Ersetze den Namen dann durch einen Namen aus `get_measures`, `get_dimensions` oder `get_cube_details`; rate keine Varianten.

Weitere Schlüssel des Cube-Formats werden unverändert an Cube weitergegeben. Sie wählen weder Organisation noch Endpunkt aus; der Kontext kommt ausschließlich aus der Anmeldung. Erfinde keine Parameter wie `groupBy` oder `year_to_date`.

## Beispiele

Gesamtwert eines Quartals:

```json
{
  "measures": ["Leistungen.Geldwert"],
  "timeDimensions": [{ "dimension": "Leistungen.LeistungDatum", "dateRange": ["2026-07-01", "2026-09-30"] }]
}
```

Monatsreihe, aufgeteilt nach einer Dimension:

```json
{
  "measures": ["Leistungen.Geldwert"],
  "dimensions": ["Leistungen.Fachgruppe"],
  "timeDimensions": [{ "dimension": "Leistungen.LeistungDatum", "granularity": "month", "dateRange": "this year" }],
  "order": [["Leistungen.LeistungDatum.month", "asc"]]
}
```

Die zehn größten Werte mit Filter:

```json
{
  "measures": ["Leistungen.Geldwert"],
  "dimensions": ["Leistungen.Fachgruppe"],
  "timeDimensions": [{ "dimension": "Leistungen.LeistungDatum", "dateRange": "quarter_to_date" }],
  "filters": [{ "member": "Leistungen.Art", "operator": "equals", "values": ["EBM"] }],
  "order": { "Leistungen.Geldwert": "desc" },
  "limit": 10
}
```

Die Member-Namen sind Beispiele. Verwende ausschließlich Namen aus dem aktiven Katalog.

## Ergebnis

Das Ergebnis enthält die ausgeführte `query` mit aufgelösten Datumsgrenzen, die Definitionen der verwendeten Kennzahlen und Dimensionen, `rowCount`, die Zeilen in `data` und `lastRefreshTime`. Mit `total: true` kommt `total` hinzu. Zeilenschlüssel sind die Member-Namen, bei Zeitreihen ergänzt um die Granularität (`Leistungen.LeistungDatum.month`). Dezimalwerte kommen unverändert als Zeichenkette.

Mit `compareDateRange` steht statt `data` die Liste `results` im Ergebnis: ein Teilergebnis je Zeitraum, in der Reihenfolge von `compareDateRange`, jeweils mit eigener `query` (mit den Grenzen dieses Zeitraums), `rowCount` und `data`.

## SQL einer Abfrage

`get_cube_query_sql` nimmt dieselben Parameter wie `query_cube` und führt nichts aus. Cube löst die Abfrage selbst in SQL gegen die Quelltabellen auf, ohne Voraggregationen. Das SQL zeigt also die aktuelle Modelldefinition, nicht den Datenstand einer Voraggregation. Das Ergebnis enthält `sql` als `{ "statement", "parameters" }`, bei `compareDateRange` eine Liste mit einem Eintrag je Zeitraum. Platzhalter folgen dem Dialekt: `$1, $2 …` (Postgres, `$n` ist `parameters[n-1]`) oder `?` (ClickHouse, der Reihe nach). Setze die Werte für die Erklärung gedanklich ein, statt Platzhalter ungedeutet auszugeben.

Die Plattform setzt kein eigenes Zeilenlimit. Ohne `limit` gilt die Vorgabe der Cube-API. Trägt das Ergebnis `possiblyTruncated: true`, hat die Zeilenzahl das angewandte Limit erreicht und das Ergebnis kann unvollständig sein. Blättere dann mit `order`, `limit` und `offset` weiter oder verdichte die Abfrage, bevor du Summen aus den Zeilen ableitest.

## Datenstrukturen

`get_data_entities` akzeptiert optional `query`, `limit` und `offset`. Das Limit beträgt standardmäßig 25 und höchstens 100. Verwende für weitere Seiten den zurückgegebenen `nextOffset`, solange weitere Einträge für die Frage benötigt werden.

`get_data_entity_details` benötigt `entity`: einen genauen Namen oder eine ID aus dem Katalog. Das Ergebnis beschreibt die Entität und ihre Spalten; es liefert keine einzelnen Datensätze. Falls die Berechtigung fehlt, erkläre den fehlenden Metadatenzugriff und rate nicht zu einem anderen Transportweg.
