# SQL Cheat Sheet

## Datentypen

- **INT** (integer4, int)
- **SMALLINT** (integer2)
- **REAL** (wie float)
- **DECIMAL(p,q)** und **NUMERIC**(p,q) mit jeweils p Stellen gesamt und davon q Nachkommastellen
- **CHAR(n)** für Strings fester Länge n
- **VARCHAR(n)** für Strings variabler Länge bis zur max. Länge n
- **BIT(n)** oder **BIT VARYING(n)** analog für Bitfolgen
- **DATE, TIME/TIMESTAMP** für Datum-, Zeit- & kombinierte Datums-Zeit-Angaben
- **BLOB** für sehr große binäre Dateien
- **TEXT** für sehr große Strings

## CREATE

```sql
CREATE TABLE Mitarbeiter
(
    personalnummer INT,
    vorname VARCHAR(20),
    nachname VARCHAR(20),
    geburtsdatum DATE
    gehalt DECIMAL
);
```

## INSERT 

```sql
INSERT INTO Mitarbeiter
VALUES (1,'Gordon', 'Freeman','2018-01-10', 100.0);
```

## DROP TABLE 

- RESTRICT: Tabelle wird nur gelöscht, wenn sie von keiner anderen referenziert wird

```sql
DROP TABLE Mitarbeiter RESTRICT;
```

- CASCADE: Gesamte Tabelle, sowie FK-Constraints der referenzierenden Tabelle(n) werden gelöscht

```sql
DROP TABLE Mitarbeiter CASCADE;
```

## ALTER TABLE

```sql
ALTER TABLE Mitarbeiter ADD Kinder INT;
ALTER TABLE Mitarbeiter DROP Kinder;
```

## CONSTRAINTS

Statische Integritätsbed. auf Tabellen (bzgl. Attributen):

- **NOT NULL**: Verbot von Nullwerten
- **DEFAULT**: Standard-Werte
- **PRIMARY KEY**: Eindeutigkeit
- **FOREIGN KEY**: Fremdschlüssel
- **CHECK**: Nachbedingung

```sql
CREATE TABLE VERLAG
(
    VerlagsID   INT,
    Verlagsname VARCHAR(15)
    CONSTRAINT c_VerlagPK PRIMARY KEY (Verlagsname)
);

CREATE TABLE Buch
(
    ISBN    CHAR(10) NOT NULL,
    Auflage SMALLINT DEFAULT 1,
    Jahr    INT NOT NULL,
    Verlag  VARCHAR(15) 
    CONSTRAINT c_BuchPK PRIMARY KEY (ISBN, Auflage),
    CONSTRAINT c_checkAuflage CHECK (Auflage > 0),
    CONSTRAINT c_checkJahr (Jahr BETWEEN 1800 AND 2060)
);

ALTER TABLE Buch
    ADD CONSTRAINT c_BuchFK FOREIGN KEY(Verlag) REFERENCES Verlag(Verlagsname)
    ON DELETE RESTRICT ON UPDATE RESTRICT; /*Man kann also nichts an diesem FK ändern (RESTRICT)*/

ALTER TABLE Buch 
    DROP CONSTRAINT c_checkJahr;
```

## TRUNCATE

- Löscht Tabellen**inhalte**, nicht die Tabellenstruktur selbst!
- Berücksichtigt keine ON DELETE-Trigger, kann also nicht angewandt werden für Tabellen, die von anderen Tabellen referenziert werden

```sql
TRUNCATE TABLE Buch;
```

## SELECT

- Mit Hilfe des Select-Operators können Informationen aus der Datenbank ausgelesen werden.

- Das Ergebnis jeder SELECT-Operation ist wieder eine Tabelle (Relation).

- Ein SELECT-Ausdruck besteht neben dem SELECT-Operator aus mehreren Klauseln:
    FROM ..., WHERE ..., GROUP BY ..., HAVING ..., ORDER BY ... .

1. Selektieren alle Spalten einer Tabelle (DISTINCT)

- DISTINCT bewirkt, dass doppelte Zeilen aus der Ergebnis-tabelle eliminiert werden

```sql
SELECT DISTINCT * FROM Mitarbeiter;
```

2. Umbenennung der projizierten Spalten

```sql
SELECT Name as Username from Mitarbeiter;
```

3. SELECT mit arithmetischen Ausdrücken

- Als arithmetische Operatoren mit Klammern “(” bzw. “)” stehen zur Verfügung:
    +, –, *, / , power(<Attribut>,<potenz>).

- Hat einer der Operanden einen unbekannten Wert (= NULL), dann ist auch das Ergebnis der arithmetischen Operation unbekannt. 

```sql
SELECT per_nr AS PersonalNr,
gehalt AS "altes Gehalt",
gehalt * 1.05 AS "neues Gehalt"
FROM angestellter;
```

4. Skalare Ausdrücke

- numerischen Wertebereichen: etwa +, −, und /
- Strings: Operationen wie char_length (aktuelle Länge eines Strings), Konkatenation || und substring (Suchen einer Teilzeichenkette an bestimmten Positionen des Strings)
- Datumstypen und Zeitintervallen: Operationen wie current_date (aktuelles Datum),current_time (aktuelle Zeit), +, − und *

- Berechnung der Länge des Ersatzteilbezeichners.

```sql
SELECT e_nr, e-bez, length(e_bez) as clength
FROM eteil
```

5. Selektion auf Mengen

```sql
SELECT ab_datum, f_bez
FROM abflug
WHERE ab_datum IN ('13.05.01', '14.05.01');
```

## ORDER BY
```sql
SELECT vorname, nachname FROM Mitarbeiter ORDER BY nachname;
```

## UPDATE

```sql
UPDATE Mitarbeiter SET gehalt = 50.00 WHERE nachname = 'Freeman';
UPDATE Mitarbeiter SET gehalt = gehalt * 2 WHERE vorname = 'Gordon';
```

## DELETE

```sql
DELETE FROM Mitarbeiter WHERE personalnummer = 123; /*Löscht einzelne Zeile*/
DELETE FROM Mitarbeiter /*Löscht alle Datensätze, aber nicht die Tabelle*/
```

## JOIN
