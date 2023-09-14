# PostgreSQL

## Einführung

## Installation

### Windows

- [Download](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads) PostgreSQL installer for Windows
- Install PostgreSQL
- Verify the installation

### Linux

- [Download](https://www.postgresql.org/download/linux/)
- z.B. eine Ubuntu-Installation:<br>
[https://www.postgresqltutorial.com/postgresql-getting-started/install-postgresql-linux/](https://www.postgresqltutorial.com/postgresql-getting-started/install-postgresql-linux/)

## Datenbank Connection

- psql – ein terminalbasierendes Frontend zum PostgreSQL Datenbank Server
- pgAdmin – ein GUI Frontend zum PostgreSQL Datenbank Server
- Datagrip - ein Jetbrains Frontend zu fast allen Datenbank Servern
- phpPgAdmin - ein webbasierendes Frontend

Applikation-Connections:
- PHP
- Python
- Java
- ODBC or JDBC
- ...

### psql Commands

Informationen unter [https://www.postgresqltutorial.com/postgresql-administration/psql-commands/](https://www.postgresqltutorial.com/postgresql-administration/psql-commands/)

- \c ... zu einer anderen Datenbank wechseln
- \l ... Auflisten der Datenbanken
- \dt ... Auflisten der Tabellen
- \d ... Tabelle beschreiben
- \dn
- \df
- \dv
- \du ... alle User und deren Rollen auflisten
- \i ... ausführen eines SQL-Files
- \?

## Datenbank Administration

### Rollen & Privilegien

#### neue Rolle anlegen
```sql
CREATE ROLE role_name;
# CREATE ROLE hugo;
```

#### Rollen ansehen
```sql
SELECT rolname FROM pg_roles;
```
```sql
\du
```

#### Rollen Attribute
```sql
CREATE ROLE role_name;
# CREATE ROLE hugo;
```


### Backup & Restore


## Links

[https://www.postgresqltutorial.com/](https://www.postgresqltutorial.com/)
