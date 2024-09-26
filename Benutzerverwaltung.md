## Benutzerverwaltung

### User/Benutzer

#### Anlegen eines Benutzers

```python
CREATE USER BenuterA LOGIN PASSWORD 'benutzerA';

CREATE ROLE BenuterB LOGIN PASSWORD 'benutzerB';
```

#### Löschen eines Benutzers

```python
DROP USER BenuterA;
```

### Rollen

- Zusammenfassung von Benutzer zu Gruppen
- Rollen haben Rechte. Benutzer, denen einen Rolle zugeordnet wurden, übernehmen automatisch die Rechte der Rolle
- Ein Benutzer kann mehrere Rollen annehmen. Der Benutzer ist mehreren Benutzergruppen zugeordnet

#### Rolle anlegen

```python
CREATE ROLE admin SUPERUSER;
```
- Mit Hilfe des Befehls CREATE ROLE wird eine Benutzergruppe angelegt
- Mit Hilfe des Attributs SUPERUSER wird dieser Rolle Administrator-Rechte übertragen

#### Rolle „Darf Datenbank anlegen“

```python
CREATE ROLE datenbankAnlegen CREATEDB;
```

#### Rollen ohne Attribute

```python
CREATE ROLE vertrieb;
```

#### Löschen von Rollen

```python
DROP ROLE personal;
```

#### … in PostgreSQL anzeigen

```python
\du
```

- Mit Hilfe der Option \du werden alle Rollen und Benutzer angezeigt.

#### Rollen und deren Attribute ändern

```python
ALTER ROLE datenbankneu NOCREATEROLE;
```

#### Umbenennung von Rollen / Benutzern

```python
ALTER ROLE salesmanagement RENAME TO verkauf;
```

#### Zuweisung von Rollen

```python
GRANT buchhaltung TO meier, mueller;
```

- Dem Befehl GRANT folgt der Name der Rolle, der Benutzer zugewiesen werden sollen
- Dem Schlüsselwort TO folgen die Namen der Benutzer, die der Rolle zugewiesen werden. Die Namen der Benutzer werden durch ein Komma getrennt
- Hinweis: Die Benutzernamen müssen vor der Zuweisung angelegt werden

#### Zugriffsrechte vergeben

```python
GRANT ALL ON kunde_firma TO vertrieb;
```

- Dem Befehl GRANT folgt eine Beschreibung der Zugriffsrechte. ALL beschränkt den Zugriff nicht
- Der Befehl ON folgt der Name der Tabelle, für die die Zugriffsrechte vergeben werden sollen
- Dem Schlüsselwort TO folgen die Namen der Rollen, für die Zugriffsrechte gelten sollen

#### Auflistung von Zugriffsrechten

```python
GRANT SELECT, UPDATE ON kunde_firma TO buchhaltung;
```

- ALL. Alle Zugriffsrechte auf eine Tabelle
- SELECT. Leserechte. Ausführen von Auswahlabfragen
- Das Einfügen neuer Datensätze in einer Tabelle ist erlaubt
- UPDATE. Schreibrechte. Vorhandene Datensätze können geändert werden
- DELETE. Recht zum Löschen von Datensätzen
- Weitere Zugriffsrechte in PostgreSQL: https://www.postgresql.org/docs/9.0/static/sql-grant.html

#### Anzeige von Zugriffsrechten in PostgreSQL

```python
\dp
```

- Tabellen und die Zugriffsrechte auf die Tabellen werden aufgelistet.
