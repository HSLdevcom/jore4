# Data modeling guidelines

1. Avoid forks of Jore4
2. Reduce exceptional, weird and legacy concepts from Jore3 and public transport tradition
3. Apply Transmodel names
4. Separate internal schemas from published schemas
5. Consider best practices

## Reasoning and consequences

### 1. Avoid forks of Jore4

For Jore4 to be a viable choice elsewhere, it has to have a core system that can be used as such.
If any other country absolutely needs to fork our work before it's useful to them, we have lost a tremendous amount of collaboration potential.

Other organizations probably need to add their own importers or exporters.
Yet we should aim to keep the SQL schema, GraphQL API and UI reusable as such.

Needing to change the SQL data definition language (DDL) requires forking the SQL migrator.
In contrast, needing to change the SQL data manipulation language (DML) suggests creating a new importer or exporter.
So try to avoid localized DDL.

This hints at the following approaches:

- As there cannot be a grid reference system that works accurately enough everywhere, store GIS objects in EPSG:4326 (WGS 84).
  - The natural datatype for that is `geography`.
  - When in need of `geometry` functions, use `_ST_BestSRID` to find a suitable cartesian coordinate system for the duration of the calculation.
- Add specific languages via DML.
  - E.g. no `lang_fi` columns but rather two columns: string and its language/locale.
  - Each language deserves its own indexes with its own collation.
- Use `timestamptz` in DDL.
- Put timezones in DML. Use the form `Europe/Helsinki`, not `+03:00`.
  - PostgreSQL carries the tz database timezone names in `pg_timezone_names`.
- Avoid adding Finnish calendar events or holidays into DDL.
  - Maybe this can help: <https://holidata.net/fi-FI/2021.csv>
- Weekdays from Monday to Sunday are global in the Gregorian calendar.
  - "Fri-Sun" is not.
     Maybe we can avoid it in DDL?

### 2. Reduce exceptional, weird and legacy concepts from Jore3 and public transport tradition

A great threat from Jore3 is a muddled data model.
Let's remove all exceptional legacy hidden variants we can.

Normalize heavily.
That way we stay vigilant of what is essential and what is nice-to-have.

Denormalize when the need is shown.

Use views for a friendly API, e.g. to offer the whole route shape denormalized from individual infrastructure links.
If we have performance woes, consider materializing the views.
Materialized views can be especially sensible for triggered imports if each materialization gets repeated use.

If you smell conceptual overlap, you're probably right and refactoring is needed.

### 3. Apply Transmodel names

<https://www.transmodel-cen.eu/model/index.htm> helps in finding names for tables and columns.

### 4. Separate internal schemas from published schemas

If it says `internal`, don't expose it in the GraphQL API.

Do not expose both a view with denormalized helpers and its base table.

### 5. Consider best practices

Follow these: <https://wiki.postgresql.org/wiki/Don%27t_Do_This>

`jsonb` creates a shadow schema within the SQL schema.
A `jsonb` column can have `CHECK CONSTRAINT`s but it is a lot of work and defeats some of the benefits of `jsonb`.

Avoid `jsonb` unless:

1. you are constantly changing your schema radically in the early exploration phase or
1. you need to avoid dozens of columns filled mostly with `NULL`s.
