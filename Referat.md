# Python-Datenbankpojekt

## Aufgabestellung
- Entwickeln Sie eine Datenbank entsprechend ihrer jeweiligen Datenbankanforderung (Datenbankbeispiele). Erörtern Sie auch die Anforderungen an diese Datenbank (siehe Beispieldatenbank) und entwickeln Sie ein entsprechendes ER-Modell.
Erzeugen Sie eine Datenbank- und Tabellenstruktur mit den notwendigen Rechten und füllen Sie diese Datenbank mit benötigten Testdaten. Verwenden Sie als Datenbankserver einen PostgreSQL-Server.
- Entwickeln Sie eine Python-RESTful-Schnittstelle zu dieser Datenbank und testen Sie diese mit ausgewählten Testdaten.
- Entwickeln Sie eine Python-GUI-Schnittstelle zur Ansicht der Daten der Datenbank.
- Der Datenbankserver, sowie der RESTful-Server sollten auf einem Linux-Server laufen.

## Auswahl des Projektthemas
Wir haben uns für das Projekt **Fahrradverleih** entschieden

## Voraussetzungen und Entwicklungsumgebung
- Als Linux-Umgebung (debain12) wurde eine virtuelle-Maschine mit der Virtuallisierungsumgebung VirtualBox auf meinen Laptop realisiert
- Auf diesem Linux-Server wurde ein PostgresSQL-Server installiert
  - apt install postgresql-14
  - folgende Änderungen für den remote-Zugriff von Windows auf die virtuelle Linux-Maschine sind zu machen
    - im file **/etc/postgresql/14/main/postgresql.conf**<br>
      *listen_addresses = '\*'*
    - im file **/etc/postgresql/14/main/pg_hba.conf**<br>
      *host    all             all             0.0.0.0/0               scram-sha-256*
      oder
      *host    all             all             0.0.0.0/0               md5*
- Python3-Installation auf dem Windows-Rechner

## Erörterung der Anforderungen
Ein Theater benötigt eine Datenbank zur Verwaltung des Personals und der Vorstellungen.
Es werden verschiedene Stücke in diesem Theater aufgeführt.
Für die Theaterstücke werden der Titel, der Autor, das Entstehungsjahr, sofern bekannt, das Datum der Uraufführung sowie eine kurze Beschreibung
der Handlung erfasst.Ein Stück gehört in eine oder mehrere Kategorien wie z.B. Tragödie oder Komödie.
Ein Stück wird in einer Spielzeit in mehreren Vorstellungen aufgeführt. Diese haben einen bestimmten Termin und einen Ort (normalerweise ein Saal des Theaters).
Es könnte aber auch Gastspiele an anderen Theatern oder z.B. Freiluft-Vorstellungen geben.
Jeder Saal hat einen Namen und eine bestimmte Anzahl an Zuschauerplätzen. Im Idealfall sind die Vorstellungen ausverkauft, aber nicht immer.
Es soll also auch die Anzahl der Zuschauer für jede Vorstellung erfasst werden. Falls die Vorstellung nicht in einem Saal des Theaters stattfindet,
soll der Ort mit erfasst werden. Also eine Adresse oder eine Beschreibung des Ortes, falls dieser keine Adresse hat.
An den Vorstellungen sind verschiedene Mitarbeiter des Theaters beteiligt. Mehrere Schauspieler spielen Rollen in der Aufführung. Techniker sind für Bühnenaufbau, Beleuchtung und Ton verantwortlich. Zu manchen Aufführungen gehört möglicherweise auch ein Orchester. In diesem Fall sind auch einige Musiker an der Aufführung beteiligt. Weitere Servicemitarbeiter verkaufen und kontrollieren die Eintrittskarten und arbeiten in der Garderobe des Theaters. Alle Mitarbeiter haben eine Personalnummer. Manche Mitarbeiter (z.B. die Techniker) sind langfristig am Theater angestellt und erhalten einen regelmäßigen monatlichen Lohn. Einige Schauspieler oder Musiker erhalten nur einen Vertrag für eine bestimmte Rolle oder ein bestimmtes Stück und erhalten dann ein Honorar für die gespielten Vorstellungen. Dieser Unterscheidung der Mitarbeiter muss in der Datenbank erfasst werden.

## Datenbankdesign

Die Datenbank besteht aus den folgenden Tabellen:
- Fahrrad()
- Radkategorie()
- Kunde()
- Verleih()

Zwischen den Tabellen bestehen folgende Beziehungen
- Bez1
- Bez2
- Bez3


