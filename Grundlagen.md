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
\du+
```

#### Rollen Attribute
```sql
CREATE ROLE role_name;
# CREATE ROLE hugo;
```

- Login Role
  ```sql
  CREATE ROLE alice LOGIN PASSWORD 'securePass1';
  CEATE user alice;
  ```
- Superuser Role
  ```sql
  CREATE ROLE john SUPERUSER LOGIN PASSWORD 'securePass1';
  ```
- Role with create databases
  ```sql
  CREATE ROLE dba CREATEDB LOGIN PASSWORD 'Abcd1234';
  ```

### GRANT

```sql
GRANT privilege_list | ALL ON table_name TO role_name;
```

privilege_list:
- SELECT
- INSERT
- UPDATE
- DELETE
- TRUNCATE

Um auf die Tabelle candidates die INSERT, UPDATE und SELECT Rechte an die Rolle joe zu vergeben ist folgender Befehl notwendig:
```sql
GRANT INSERT, UPDATE, DELETE ON candidates TO joe;
```

- Grant all privileges auf alle Tabellen in einem Schema
  ```sql
  GRANT ALL ON ALL TABLES IN SCHEMA "public" TO joe;
  ```

### Gruppen Rollen
```sql
CREATE ROLE group_role_name;
GRANT group_role_name to user_role;
```


### Backup & Restore


## Links

- [https://www.postgresqltutorial.com/](https://www.postgresqltutorial.com/)
- [https://www.prisma.io/dataguide/postgresql](https://www.prisma.io/dataguide/postgresql)
