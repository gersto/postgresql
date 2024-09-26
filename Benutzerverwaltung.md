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
print(f500.head(3))
```

```python
print(f500.head(3))
```

```python
print(f500.head(3))
```

```python
print(f500.head(3))
```
