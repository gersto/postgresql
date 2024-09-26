## Postgresql Schema

### Schema erstellen und autorisieren

```python
CREATE SCHEMA AUTHORIZATION vertrieb

CREATE TABLE Land(
  landkuerzel VARCHAR(3) PRIMARY KEY,
  landname VARCHAR(150)
)

CREATE TABLE Kunde_Firma(
  id SERIAL PRIMARY KEY,
  firmenname VARCHAR(100),
  strasse VARCHAR(100),
  postleitzahl VARCHAR(6),
  ort VARCHAR(100),
  idLand VARCHAR(3) REFERENCES Land(landkuerzel),
  aufgenommenAm TIMESTAMP,
  darfBestellen BOOLEAN
);
```

- Der Befehl CREATE SCHEMA AUTHORIZATION vertrieb erstellt ein Schema vertrieb, welches für die Rolle vertrieb autorisiert wird
- Das Schema und der Besitzer des Schemas werden gleichzeitig definiert
- Hinweis: Die Erstellung des Schemas endet mit dem ersten Semikolon. In dem Beispiel auf der vorherigen Seite werden die Tabelle Land und Kunde_Firma automatisiert dem Schema vertrieb zugeordnet

#### Besitzer von Objekten

- Jeder, der ein Objekt in einer Datenbank mit CREATE anlegt, ist Besitzer des Objekts
- Jeder, der für ein Schema autorisiert ist, hat alle Rechte für alle Objekte in dem Schema
- Der Administrator und der Besitzer eines Objekts haben automatisch auf das Objekt Zugriff. Allen anderen Benutzer muss der Administrator die entsprechenden Rechte geben

#### Zugriffsrechte für alle Tabellen im Schema

```python
GRANT SELECT ON ALL TABLES IN SCHEMA vertrieb TO buchhaltung;
```
- Dem Befehl GRANT folgt eine Beschreibung der Zugriffsrechte. SELECT erlaubt einen lesenden Zugriff auf Tabellen
- Der Befehl ON folgt das Schlüsselwort ALL TABLES IN SCHEMA. Die Zugriffsrechte werden für alle Tabellen in einem bestimmten Schema vergeben
- Dem Schlüsselwort TO folgen die Namen der Rollen, für die Zugriffsrechte gelten sollen

#### Zugriffsrechte für Tabellen im Schema

```python
GRANT UPDATE ON vertrieb.Kunde_Firma TO buchhaltung;
```
- Der Befehl GRANT vergibt Zugriffsrechte für Tabellen
- Das Zugriffsrecht UPDATE erlaubt eine Änderung der Datensätze in der Tabelle Kunde_Firma in dem Schema vertrieb
- Dem Schlüsselwort TO folgen die Namen der Rollen, für die Zugriffsrechte gelten sollen

#### Zugriffsrechte für Datenfelder in Tabellen

```python
GRANT UPDATE(darfbestellen) ON vertrieb.Kunde_Firma TO buchhaltung;
```

- Der Befehl GRANT vergibt Zugriffsrechte für Tabellen
- Das Zugriffsrecht UPDATE erlaubt eine Änderung des Datenfeldes darfbestellen in der Tabelle Kunde_Firma in dem Schema vertrieb
- Dem Schlüsselwort TO folgen die Namen der Rollen, für die Zugriffsrechte gelten sollen

#### Entziehung von allen Zugriffsrechten

```python
REVOKE ALL ON vertrieb.bestellung FROM buchhaltung;
```

#### Entziehung von bestimmten Zugriffsrechten

```python
REVOKE SELECT ON vertrieb.Land FROM buchhaltung;
```
