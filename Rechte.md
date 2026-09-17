Gerne — hier ist ein **Skriptum zum PostgreSQL-Rechtesystem**, das sich gut zum Lernen, für Unterricht/Prüfung und als Nachschlagewerk eignet.

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

 # 27\. Sequenzen

 Sequenzen werden beispielsweise für automatisch erzeugte IDs verwendet:

```
CREATE TABLE kunden (
    id integer GENERATED BY DEFAULT AS IDENTITY,
    name text
);
```

 Bei älteren bzw. explizit verwendeten Sequenzen kann ein Benutzer zusätzlich Sequenzenrechte benötigen.

 Beispiel:

```
GRANT USAGE, SELECT
ON SEQUENCE kunden_id_seq
TO app_user;
```

 Bei modernen Identity-Spalten sollte man die konkrete PostgreSQL-Version und das verwendete Spalten-/Sequenzmodell berücksichtigen.

---

 # 28\. Funktionen und EXECUTE

 Auch Funktionen besitzen eigene Berechtigungen.

 Beispiel:

```
CREATE FUNCTION hallo()
RETURNS text
AS $$
BEGIN
    RETURN 'Hallo';
END;
$$ LANGUAGE plpgsql;
```

 Ein Benutzer kann das Ausführen einer Funktion benötigen:

```
GRANT EXECUTE
ON FUNCTION hallo()
TO max;
```

 Wichtig: Das Recht, eine Funktion auszuführen, ist ein eigenes Privileg.

---

 # 29\. Row-Level Security (RLS)

 PostgreSQL bietet mit **Row-Level Security** eine besonders feingranulare Zugriffskontrolle.

 Ohne RLS bedeutet:

```
SELECT *
FROM kunden;
```

 Hat ein Benutzer `SELECT`, kann er grundsätzlich die sichtbaren Zeilen der Tabelle lesen.

 Mit RLS kann dagegen festgelegt werden:

 > Dieser Benutzer darf nur bestimmte Zeilen sehen.

 Beispiel:

```
ALTER TABLE kunden ENABLE ROW LEVEL SECURITY;
```

 Danach kann eine Policy definiert werden:

```
CREATE POLICY kunden_policy
ON kunden
FOR SELECT
USING (benutzer = current_user);
```

 Damit kann der Zugriff auf Zeilen von der aktuellen Rolle abhängig gemacht werden.

 RLS ist besonders interessant für:

 - Mandantensysteme
- Benutzer-spezifische Daten
- Abteilungen
- SaaS-Anwendungen

---

 # 30\. GRANT vs. RLS

 Diese beiden Konzepte sollte man unterscheiden.

 ### GRANT

 Beantwortet:

 > Darf die Rolle grundsätzlich auf dieses Objekt zugreifen?

 Beispiel:

```
GRANT SELECT ON kunden TO max;
```

 ### RLS

 Beantwortet:

 > Welche Zeilen darf die Rolle innerhalb dieses Objekts sehen oder verändern?

 Vereinfacht:

```
GRANT
  ↓
Darf max die Tabelle verwenden?
  ↓
RLS
  ↓
Welche Zeilen darf max verwenden?
```

---

 # 31\. Typisches Berechtigungskonzept

 Eine gute Struktur ist die Trennung von:

```
Benutzer
   ↓
Gruppen-/Anwendungsrollen
   ↓
Objektprivilegien
```

 Beispiel:

```
max
 │
 └── Mitglied von → buchhaltung
                         │
                         ├── SELECT kunden
                         ├── INSERT rechnungen
                         └── UPDATE rechnungen
```

 Dadurch müssen Rechte nicht für jeden Benutzer einzeln vergeben werden.

---

 # 32\. Prinzip der geringsten Rechte

 Ein wichtiges Sicherheitsprinzip ist **Least Privilege** bzw. das Prinzip der geringsten Rechte.

 Ein Benutzer sollte nur die Rechte erhalten, die er tatsächlich benötigt.

 Schlecht:

```
GRANT ALL PRIVILEGES
ON ALL TABLES IN SCHEMA public
TO app_user;
```

 Besser:

```
GRANT SELECT, INSERT, UPDATE
ON kunden
TO app_user;
```

 Wenn `DELETE` nicht benötigt wird, sollte es auch nicht vergeben werden.

---

 # 33\. Typischer Aufbau in einem Projekt

 Ein mögliches Rollenmodell:

```
                    PostgreSQL
                         │
             ┌───────────┴───────────┐
             │                       │
        readonly                 app_role
             │                       │
        SELECT only          SELECT/INSERT/UPDATE
             │                       │
        ┌────┴────┐             Anwendung
        │         │
       max      anna
```

 Die Benutzer erhalten Mitgliedschaften:

```
GRANT readonly TO max;
GRANT readonly TO anna;
```

 Die Berechtigungen werden an der Rolle vergeben:

```
GRANT CONNECT
ON DATABASE firma
TO readonly;

GRANT USAGE
ON SCHEMA public
TO readonly;

GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO readonly;
```

 Dadurch bleibt die Verwaltung übersichtlich.

---

 # 34\. Häufige Fehler

 ## Fehler 1: Nur SELECT vergeben

```
GRANT SELECT ON kunden TO max;
```

 kann unzureichend sein, wenn der Zugriff auf das Schema fehlt.

 Gegebenenfalls:

```
GRANT USAGE ON SCHEMA public TO max;
GRANT SELECT ON kunden TO max;
```

---

 ## Fehler 2: Rechte an Benutzer statt Rollen vergeben

 Bei vielen Benutzern führt das schnell zu einer unübersichtlichen Konfiguration.

 Besser:

```
CREATE ROLE readonly;

GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO readonly;

GRANT readonly TO max;
GRANT readonly TO anna;
GRANT readonly TO peter;
```

---

 ## Fehler 3: DEFAULT PRIVILEGES vergessen

 Man vergibt:

```
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO readonly;
```

 und erstellt später eine neue Tabelle.

 Die neue Tabelle erhält dadurch nicht automatisch dieselben Rechte.

 Dafür:

```
ALTER DEFAULT PRIVILEGES
IN SCHEMA public
GRANT SELECT ON TABLES TO readonly;
```

 Dabei ist wichtig: Default Privileges beziehen sich auf Objekte, die von der betreffenden Rolle erstellt werden.

---

 ## Fehler 4: PUBLIC übersehen

 Wenn Rechte an `PUBLIC` vergeben wurden, können sie für alle Rollen gelten.

 Kontrolle beispielsweise mit:

```
\dp
```

---

 ## Fehler 5: Superuser für Anwendungen verwenden

 Eine Anwendung sollte normalerweise nicht mit einem Superuser laufen.

 Besser:

```
Anwendung
    ↓
app_user
    ↓
nur benötigte Rechte
```

 statt:

```
Anwendung
    ↓
SUPERUSER
    ↓
fast vollständige Kontrolle
```

---

 # 35\. Wichtigste SQL-Befehle

 ### Rolle erstellen

```
CREATE ROLE name;
```

 ### Rolle mit Login erstellen

```
CREATE ROLE name
LOGIN
PASSWORD 'passwort';
```

 ### Rolle ändern

```
ALTER ROLE name LOGIN;
```

 ### Rolle löschen

```
DROP ROLE name;
```

 ### Mitgliedschaft vergeben

```
GRANT gruppe TO benutzer;
```

 ### Mitgliedschaft entfernen

```
REVOKE gruppe FROM benutzer;
```

 ### Tabellenrecht vergeben

```
GRANT SELECT ON tabelle TO benutzer;
```

 ### Tabellenrecht entfernen

```
REVOKE SELECT ON tabelle FROM benutzer;
```

 ### Schema verwenden

```
GRANT USAGE ON SCHEMA public TO benutzer;
```

 ### Datenbankverbindung erlauben

```
GRANT CONNECT ON DATABASE firma TO benutzer;
```

 ### Alle Tabellen

```
GRANT SELECT
ON ALL TABLES IN SCHEMA public
TO benutzer;
```

 ### Zukünftige Tabellen

```
ALTER DEFAULT PRIVILEGES
IN SCHEMA public
GRANT SELECT ON TABLES TO benutzer;
```

 ### Rollen anzeigen

```
\du
```

 ### Tabellenrechte anzeigen

```
\dp
```

---

 # 36\. Merkschema

 Für PostgreSQL-Rechte kann man sich folgende Ebenen merken:

```
              ROLLE
                │
                ▼
           Mitgliedschaft
                │
                ▼
             DATENBANK
                │
             CONNECT
                │
                ▼
              SCHEMA
                │
              USAGE
                │
                ▼
             OBJEKT
                │
          SELECT / INSERT
          UPDATE / DELETE
                │
                ▼
               RLS
                │
                ▼
        einzelne Zeilen
```

 Nicht jede Anwendung benötigt alle Ebenen explizit, aber dieses Modell hilft beim Verständnis.

---

 # 37\. Prüfungswissen – kurz zusammengefasst

 Für eine Prüfung sollte man insbesondere folgende Begriffe beherrschen:

 **Role**

 - Benutzer und Gruppen werden über Rollen umgesetzt.
- Rollen können Mitglied anderer Rollen sein.

 **Owner**

 - Besitzer eines Objekts.
- Hat besondere Kontrolle über das Objekt.

 **GRANT**

 - Vergibt Privilegien.

 **REVOKE**

 - Entzieht Privilegien.

 **Privilege**

 - Konkrete Berechtigung wie `SELECT`, `INSERT`, `UPDATE` oder `DELETE`.

 **PUBLIC**

 - Alle Rollen.

 **WITH GRANT OPTION**

 - Erlaubt das Weitergeben eines Privilegs.

 **Schema**

 - Namensraum für Datenbankobjekte.
- `USAGE` erlaubt die Verwendung des Schemas; `CREATE` erlaubt das Erstellen von Objekten.

 **CONNECT**

 - Erlaubt eine Verbindung zu einer Datenbank.

 **DEFAULT PRIVILEGES**

 - Legen Rechte für zukünftig von einer Rolle erstellte Objekte fest.

 **SUPERUSER**

 - Besitzt weitreichende Sonderrechte und sollte sparsam eingesetzt werden.

 **RLS**

 - Ermöglicht eine Zugriffskontrolle auf Zeilenebene.

---

 # 38\. Übungsaufgabe

 Gegeben ist eine Datenbank:

```
firma
```

 mit dem Schema:

```
public
```

 und den Tabellen:

```
kunden
bestellungen
mitarbeiter
```

 Erstelle eine Rolle:

```
buchhaltung
```

 und einen Benutzer:

```
max
```

 Der Benutzer `max` soll:

 - sich anmelden können,
- auf die Datenbank `firma` zugreifen können,
- Tabellen im Schema `public` verwenden können,
- `kunden` lesen können,
- `bestellungen` lesen und ändern können,
- `mitarbeiter` nicht lesen können.

 Eine mögliche Lösung:

```
CREATE ROLE buchhaltung;

CREATE ROLE max
LOGIN
PASSWORD 'geheim';

GRANT buchhaltung TO max;

GRANT CONNECT
ON DATABASE firma
TO buchhaltung;

GRANT USAGE
ON SCHEMA public
TO buchhaltung;

GRANT SELECT
ON kunden
TO buchhaltung;

GRANT SELECT, UPDATE
ON bestellungen
TO buchhaltung;
```

 Für `mitarbeiter` wird kein `SELECT`-Recht vergeben.

 Wichtig: In einer realen Umgebung sollte zusätzlich geprüft werden, ob über `PUBLIC`, andere Rollenmitgliedschaften oder sonstige Privilegien bereits Zugriff besteht.

---

 # 39\. Lernfragen

 1. Was ist eine PostgreSQL-Rolle?
2. Was ist der Unterschied zwischen `CREATE USER` und `CREATE ROLE`?
3. Was bedeutet `LOGIN`?
4. Was ist ein Owner?
5. Was macht `GRANT`?
6. Was macht `REVOKE`?
7. Was bedeutet `PUBLIC`?
8. Was ist `WITH GRANT OPTION`?
9. Was ist der Unterschied zwischen `USAGE` und `CREATE` bei einem Schema?
10. Was macht `CONNECT`?
11. Was sind Default Privileges?
12. Warum sind Rollen für Gruppen sinnvoll?
13. Was macht `SET ROLE`?
14. Was ist ein Superuser?
15. Was ist Row-Level Security?
16. Was bedeutet das Prinzip der geringsten Rechte?
17. Warum sollte eine Anwendung möglichst nicht als Superuser laufen?
18. Warum können `GRANT`-Rechte auf einer Tabelle allein nicht immer ausreichen?
19. Wie kann man in `psql` Rollen anzeigen?
20. Wie kann man in `psql` Tabellenprivilegien anzeigen?

---

 # 40\. Wichtigster Merksatz

 > **Rollen bestimmen, wer etwas darf; Privileges bestimmen, was auf welchem Objekt erlaubt ist; RLS kann zusätzlich bestimmen, welche Zeilen sichtbar oder veränderbar sind.**

 Das zentrale Zusammenspiel lautet:

```
ROLE
  │
  ├── Mitgliedschaften
  │
  ▼
PRIVILEGES
  │
  ├── CONNECT
  ├── USAGE
  ├── SELECT
  ├── INSERT
  ├── UPDATE
  └── DELETE
  │
  ▼
DATENBANKOBJEKTE
  │
  ▼
optional: ROW-LEVEL SECURITY
```

 Wenn du möchtest, kann ich daraus auch noch eine **kompakte 2–3-seitige Lernunterlage mit Beispielen und Prüfungsfragen samt Lösungen** machen.
