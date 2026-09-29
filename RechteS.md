
 # PostgreSQL-Rechtesystem

 ## 1\. Einführung

 Das PostgreSQL-Rechtesystem regelt, **welche Benutzer bzw. Rollen welche Aktionen auf welchen Datenbankobjekten durchführen dürfen**.

 Typische Fragen, die das Rechtesystem beantwortet:

 - Wer darf sich mit der Datenbank verbinden?
- Wer darf Tabellen lesen?
- Wer darf Daten einfügen, ändern oder löschen?
- Wer darf Tabellen erstellen oder verändern?
- Wer darf Rechte an andere Benutzer weitergeben?
- Welche Rechte hat ein Benutzer indirekt über eine Gruppe?

 Das PostgreSQL-Rechtesystem basiert hauptsächlich auf dem Konzept der **Rollen (Roles)** und **Berechtigungen (Privileges)**.

---

 # 2\. Rollen

 In PostgreSQL gibt es Benutzer und Gruppen nicht als grundsätzlich getrennte Konzepte. Beide werden durch **Roles** dargestellt.

 Eine Rolle kann:

 - sich anmelden,
- Datenbankobjekte besitzen,
- Berechtigungen besitzen,
- Mitglied anderer Rollen sein,
- anderen Rollen Rechte gewähren.

 Eine Rolle mit Login-Fähigkeit entspricht praktisch einem Benutzer.

 ### Rolle erstellen

```
CREATE ROLE max;
```

 Eine Rolle mit Anmeldemöglichkeit:

```
CREATE ROLE max LOGIN PASSWORD 'geheim';
```

 Alternativ:

```
CREATE USER max PASSWORD 'geheim';
```

 `CREATE USER` ist dabei im Wesentlichen eine vereinfachte Form von `CREATE ROLE ... LOGIN`.

---

 # 3\. Login-Recht

 Eine Rolle benötigt das Attribut `LOGIN`, wenn sie sich an PostgreSQL anmelden soll.

```
CREATE ROLE max LOGIN PASSWORD 'geheim';
```

 Bei einer bestehenden Rolle:

```
ALTER ROLE max LOGIN;
```

 Login wieder deaktivieren:

```
ALTER ROLE max NOLOGIN;
```

 Beispiel:

```
CREATE ROLE app_user LOGIN PASSWORD 'abc123';
```

 `app_user` kann sich nun als Datenbankbenutzer anmelden.

---

 # 4\. Rollen für Gruppen verwenden

 Rollen können auch als **Gruppenrollen** verwendet werden.

 Beispielsweise:

```
CREATE ROLE buchhaltung;
CREATE ROLE entwicklung;
```

 Anschließend kann man Benutzer den Gruppenrollen zuordnen:

```
GRANT buchhaltung TO max;
```

 Damit wird `max` Mitglied der Rolle `buchhaltung`.

 Eine typische Struktur ist:

```
Benutzer
   │
   ├── max
   ├── anna
   └── peter
        │
        ▼
     Rollen
   ┌─────────────┐
   │ buchhaltung │
   │ entwicklung │
   └─────────────┘
```

 Der Vorteil: Berechtigungen werden nicht für jeden Benutzer einzeln vergeben.

---

 # 5\. Ownership – Besitzer von Objekten

 Jedes PostgreSQL-Objekt hat grundsätzlich einen **Besitzer (Owner)**.

 Beispielsweise:

```
CREATE TABLE kunden (
    id integer,
    name text
);
```

 Die Rolle, die die Tabelle erstellt, wird normalerweise deren Besitzer.

 Der Besitzer hat besondere Rechte auf dem Objekt.

 Beispiel:

```
ALTER TABLE kunden OWNER TO admin;
```

 Der Besitzer kann unter anderem die Berechtigungen des Objekts verwalten.

 Wichtig:

 **Besitzer und Benutzer mit bestimmten Privilegien sind nicht dasselbe.**

 Ein Benutzer kann beispielsweise `SELECT` auf eine Tabelle besitzen, ohne deren Besitzer zu sein.

---

 # 6\. Privileges

 Privileges sind konkrete Berechtigungen auf Datenbankobjekten.

 Bei Tabellen sind besonders wichtig:

 | Privilege | Bedeutung |
| --- | --- |
| `SELECT` | Daten lesen |
| `INSERT` | Daten einfügen |
| `UPDATE` | Daten ändern |
| `DELETE` | Daten löschen |
| `TRUNCATE` | Tabelle leeren |
| `REFERENCES` | Fremdschlüsselreferenzen erstellen |
| `TRIGGER` | Trigger auf Tabelle erstellen |

Beispiel:

```
GRANT SELECT ON kunden TO max;
```

 `max` darf nun die Tabelle `kunden` lesen.

---

 # 7\. GRANT

 Mit `GRANT` werden Berechtigungen vergeben.

 Allgemeine Form:

```
GRANT privilege
ON objekt
TO rolle;
```

 Beispiel:

```
GRANT SELECT ON kunden TO max;
```

 Mehrere Rechte:

```
GRANT SELECT, INSERT, UPDATE
ON kunden
TO max;
```

 Damit darf `max`:

 - lesen,
- neue Datensätze einfügen,
- bestehende Datensätze ändern.

---

 # 8\. Alle Tabellen einer Datenbank

 Bei vielen Tabellen kann es sinnvoll sein, Berechtigungen für alle Tabellen eines Schemas zu vergeben.

```
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO max;
```

 Mehrere Rechte:

```
GRANT SELECT, INSERT, UPDATE, DELETE
ON ALL TABLES IN SCHEMA public
TO max;
```

---

 # 9\. DEFAULT PRIVILEGES

 Ein wichtiger Unterschied:

```
GRANT SELECT ON ALL TABLES IN SCHEMA public TO max;
```

 gilt für **bereits vorhandene Tabellen**.

 Für künftig erstellte Tabellen können Default-Rechte festgelegt werden:

```
ALTER DEFAULT PRIVILEGES
IN SCHEMA public
GRANT SELECT ON TABLES TO max;
```

 Damit erhält `max` automatisch `SELECT` auf Tabellen, die künftig von der betreffenden Rolle erstellt werden.

 Das ist besonders wichtig bei Anwendungen und automatisierten Deployments.

---

 # 10\. REVOKE

 Mit `REVOKE` werden Rechte wieder entzogen.

```
REVOKE SELECT
ON kunden
FROM max;
```

 Mehrere Rechte:

```
REVOKE SELECT, INSERT, UPDATE
ON kunden
FROM max;
```

 Alle Tabellen im Schema:

```
REVOKE SELECT
ON ALL TABLES IN SCHEMA public
FROM max;
```

---

 # 11\. ALL PRIVILEGES

 Mit `ALL PRIVILEGES` können alle verfügbaren Privilegien eines Objekttyps vergeben werden.

```
GRANT ALL PRIVILEGES
ON kunden
TO max;
```

 Entsprechend:

```
REVOKE ALL PRIVILEGES
ON kunden
FROM max;
```

 In produktiven Umgebungen sollte man möglichst nur die tatsächlich benötigten Rechte vergeben.

---

 # 12\. Datenbankrechte

 Auch Datenbanken selbst besitzen Berechtigungen.

 Wichtige Datenbankprivilegien sind:

 - `CONNECT`
- `CREATE`
- `TEMPORARY`

 Beispiel:

```
GRANT CONNECT
ON DATABASE firma
TO max;
```

 Damit darf `max` Verbindungen zur Datenbank `firma` herstellen.

 `CREATE` erlaubt das Erstellen bestimmter Objekte innerhalb der Datenbank.

 Beispiel:

```
GRANT CREATE
ON DATABASE firma
TO entwickler;
```

---

 # 13\. Schema-Rechte

 PostgreSQL verwendet Schemas zur Strukturierung von Objekten.

 Beispiel:

```
Datenbank
│
├── public
│   ├── kunden
│   └── bestellungen
│
└── intern
    ├── gehalt
    └── mitarbeiter
```

 Für Schemas sind insbesondere wichtig:

 - `USAGE`
- `CREATE`

 ### USAGE

```
GRANT USAGE
ON SCHEMA public
TO max;
```

 `USAGE` erlaubt der Rolle, Objekte im Schema über deren Namen zu verwenden, sofern sie auch die entsprechenden Rechte auf den Objekten besitzt.

 ### CREATE

```
GRANT CREATE
ON SCHEMA public
TO entwickler;
```

 Damit darf die Rolle Objekte im Schema erstellen.

---

 # 14\. Warum USAGE auf einem Schema wichtig ist

 Angenommen:

```
GRANT SELECT ON public.kunden TO max;
```

 Allein dieses Recht reicht nicht immer aus, wenn `max` keinen Zugriff auf das Schema hat.

 Daher beispielsweise:

```
GRANT USAGE ON SCHEMA public TO max;
GRANT SELECT ON public.kunden TO max;
```

 Das Prinzip lautet:

```
Datenbank
   │
   │ CONNECT
   ▼
Schema
   │
   │ USAGE
   ▼
Tabelle
   │
   │ SELECT
   ▼
Daten
```

 Die benötigten Rechte müssen auf den jeweiligen Ebenen vorhanden sein.

---

 # 15\. Rollenmitgliedschaften

 Eine Rolle kann Mitglied einer anderen Rolle sein.

```
GRANT entwickler TO max;
```

 Damit kann `max` die Privilegien der Rolle `entwickler` erhalten, abhängig davon, wie die Rollenmitgliedschaft und die Vererbung (`INHERIT`) konfiguriert sind.

 Mitgliedschaft entfernen:

```
REVOKE entwickler FROM max;
```

 Beispiel:

```
max
 │
 ├── Mitglied von → entwickler
 │
 └── erhält dadurch deren Berechtigungen
```

---

 # 16\. INHERIT

 Rollen können das Attribut `INHERIT` besitzen.

 Beispiel:

```
ALTER ROLE max INHERIT;
```

 Bei vererbbaren Rollen können Privilegien einer Mitgliedsrolle grundsätzlich direkt verfügbar sein.

 Es gibt auch:

```
ALTER ROLE max NOINHERIT;
```

 Dann werden die Privilegien von Mitgliedsrollen nicht automatisch verwendet.

 Eine Rolle kann unter bestimmten Voraussetzungen mit `SET ROLE` in eine andere Rolle wechseln.

---

 # 17\. SET ROLE

 Beispiel:

```
SET ROLE entwickler;
```

 Danach arbeitet die Sitzung mit der Rolle `entwickler` als aktueller Rolle.

 Zurück:

```
RESET ROLE;
```

 Das ist insbesondere bei administrativen Rollen interessant.

 Beispiel:

```
Benutzer: max
       │
       ├── darf Rolle "entwickler" übernehmen
       │
       ▼
SET ROLE entwickler
```

 Damit können Rechte bewusst aktiviert werden, anstatt sie ständig direkt zu verwenden.

---

 # 18\. WITH GRANT OPTION

 Normalerweise darf ein Benutzer ein erhaltenes Privileg nicht automatisch an andere weitergeben.

 Beispiel:

```
GRANT SELECT
ON kunden
TO max;
```

 `max` darf lesen, aber nicht automatisch anderen Benutzern `SELECT` gewähren.

 Mit:

```
GRANT SELECT
ON kunden
TO max
WITH GRANT OPTION;
```

 darf `max` das `SELECT`-Recht weitergeben.

 Beispielsweise:

```
GRANT SELECT
ON kunden
TO anna;
```

---

 # 19\. REVOKE und CASCADE

 Wenn Berechtigungen weitergegeben wurden, kann das Entfernen eines Privilegs Auswirkungen auf weitere Berechtigungen haben.

 Beispiel:

```
admin
  │
  │ SELECT + GRANT OPTION
  ▼
max
  │
  │ SELECT
  ▼
anna
```

 Wird das Recht von `max` entfernt, kann dadurch auch Annas abgeleitetes Recht betroffen sein.

 Mit `CASCADE` kann das explizit angegeben werden:

```
REVOKE SELECT
ON kunden
FROM max
CASCADE;
```

---

 # 20\. PUBLIC

 `PUBLIC` bezeichnet in PostgreSQL **alle Rollen**.

 Beispiel:

```
GRANT SELECT
ON kunden
TO PUBLIC;
```

 Damit erhält grundsätzlich jede Rolle das `SELECT`-Recht auf `kunden`.

 Entzug:

```
REVOKE SELECT
ON kunden
FROM PUBLIC;
```

 `PUBLIC` sollte bei Sicherheitskonzepten immer berücksichtigt werden.

 Denn ein Benutzer kann ein Recht besitzen, obwohl es nicht explizit an seinen Benutzernamen vergeben wurde.

---

 # 21\. Privilegien überprüfen

 Mit `information_schema` können Rechte abgefragt werden.

 Beispiel:

```
SELECT *
FROM information_schema.role_table_grants;
```

 Für eine bestimmte Rolle:

```
SELECT *
FROM information_schema.role_table_grants
WHERE grantee = 'max';
```

 Auch PostgreSQL-eigene Kataloge und `psql`-Befehle sind dafür nützlich.

---

 # 22\. psql-Befehle zur Rechtekontrolle

 In `psql`:

```
\du
```

 zeigt Rollen und deren Eigenschaften.

 Beispielsweise:

```
\du
```

 zeigt unter anderem:

 - Rollenname
- Superuser
- Create-DB
- Create-Role
- Replication
- Bypass-RLS

 Für Tabellen:

```
\dp
```

 bzw.:

```
\z
```

 Damit können Tabellenprivilegien angezeigt werden.

---

 # 23\. Superuser

 Ein Superuser besitzt weitreichende Rechte und umgeht viele normale Berechtigungsprüfungen.

 Eine Rolle kann beispielsweise als Superuser erstellt werden:

```
CREATE ROLE admin
LOGIN
SUPERUSER
PASSWORD 'geheim';
```

 Oder:

```
ALTER ROLE admin SUPERUSER;
```

 Superuser sollten sparsam eingesetzt werden.

 Für normale Anwendungen sollte nach Möglichkeit ein eingeschränkter Benutzer verwendet werden.

 Beispiel:

```
PostgreSQL
│
├── postgres / Administrator
│     └── administrative Aufgaben
│
├── app_user
│     └── Rechte für Anwendung
│
└── readonly_user
      └── nur Leserechte
```

---

 # 24\. Rollenattribute

 Neben Objektprivilegien besitzen Rollen verschiedene Attribute.

 Wichtige Beispiele:

 | Attribut | Bedeutung |
| --- | --- |
| `LOGIN` | Rolle darf sich anmelden |
| `SUPERUSER` | Rolle ist Superuser |
| `CREATEDB` | Rolle darf Datenbanken erstellen |
| `CREATEROLE` | Rolle darf Rollen erstellen/verwalten |
| `REPLICATION` | Replikationsverbindungen möglich |
| `BYPASSRLS` | Row-Level-Security kann umgangen werden |
| `INHERIT` | Privilegien von Mitgliedsrollen können geerbt werden |

Beispiel:

```
CREATE ROLE entwickler
LOGIN
CREATEDB
PASSWORD 'geheim';
```

---

 # 25\. Beispiel: Read-Only-Benutzer

 Angenommen, es gibt:

```
CREATE TABLE kunden (
    id integer,
    name text
);
```

 Wir wollen einen Benutzer erstellen, der nur lesen darf.

 ### Schritt 1: Rolle erstellen

```
CREATE ROLE readonly_user
LOGIN
PASSWORD 'geheim';
```

 ### Schritt 2: Datenbankzugriff erlauben

```
GRANT CONNECT
ON DATABASE firma
TO readonly_user;
```

 ### Schritt 3: Schemazugriff erlauben

```
GRANT USAGE
ON SCHEMA public
TO readonly_user;
```

 ### Schritt 4: Leserecht vergeben

```
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO readonly_user;
```

 Damit kann der Benutzer Tabellen lesen, aber beispielsweise nicht:

```
INSERT
UPDATE
DELETE
```

---

 # 26\. Beispiel: Anwendungsbenutzer

 Eine Anwendung benötigt häufig mehr Rechte als ein Read-Only-Benutzer.

 Beispielsweise:

```
CREATE ROLE app_user
LOGIN
PASSWORD 'geheim';
```

 Dann:

```
GRANT CONNECT
ON DATABASE firma
TO app_user;

GRANT USAGE
ON SCHEMA public
TO app_user;

GRANT SELECT, INSERT, UPDATE, DELETE
ON ALL TABLES IN SCHEMA public
TO app_user;
```

 Falls die Anwendung auch Sequenzen verwendet, können entsprechende Rechte notwendig sein:

```
GRANT USAGE, SELECT
ON ALL SEQUENCES IN SCHEMA public
TO app_user;
```

---
