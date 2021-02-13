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
## LIKE Clause

```sql
SELECT first_name, last_name
FROM customer
WHERE first_name LIKE 'Jenny%';
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

Varianten: 
- **(INNER) JOIN**: Returns records that have matching values in BOTH tables
- **LEFT (OUTER) JOIN**: Returns all records from the LEFT table and the matched records from the RIGHT table
- **RIGHT (OUTER) JOIN**: Returns all records from the RIGHT table and the matched records from the LEFT table 
- **FULL (OUTER) JOIN**: Returns all recrords when there is a match in either LEFT or RIGHT table

Beispiel:

```sql
CREATE TABLE basket_a
(
    a INT PRIMARY KEY,
    fruit_a VARCHAR(100) NOT NULL
);

CREATE TABLE basket_b
(
    b INT PRIMARY KEY,
    fruit_b VARCHAR(100) NOT NULL
);
```

basket_a hat:

- (1, 'Apple')
- (2, 'Orange')
- (3, 'Banana')
- (4, 'Mango')

basket_b hat:

- (1, 'Orange')
- (2, 'Apple')
- (3, 'Watermelon')
- (4, 'Pear')

### **INNER JOIN**:

```sql
SELECT a, fruit_a, b, fruit_b 
FROM basket_a 
INNER JOIN basket_b ON fruit_a = fruit_b;

/* Ausgabe:
   a    fruit_a    b    fruit_b
   ============================
   1    Apple      2    Apple 
   ____________________________ 
   2    Orange     1    Orange 
*/
```

```INNER JOIN``` vergleicht jedes ```fruit_a``` aus ```basket_a``` mit jedem ```fruit_b``` aus ```basket_b```. Wenn welche gleich sind, werden sie jeweils in einer neuen Zeile ausgegeben. 

### **LEFT JOIN**:

```sql
SELECT a, fruit_a, b, fruit_b 
FROM basket_a 
LEFT JOIN basket_b ON fruit_a = fruit_b;

/* Ausgabe:
   a    fruit_a    b    fruit_b
   ============================
   1    Apple      2    Apple 
   ____________________________ 
   2    Orange     1    Orange 
   ____________________________
   3    Banana    null  null
   ____________________________
   4    Mango     null  null
*/
```

```LEFT JOIN ``` vergleicht jedes ```fruit_a``` aus ```basket_a``` mit jedem ```fruit_b``` aus ```basket_b```. Wenn sie gleich sind werden sie wie bei ```INNER JOIN``` als eine Zeile ausgegeben. Jedoch wird auch eine neue Zeile gemacht, wenn sie NICHT gleich sind (siehe null Werte).

### RIGHT JOIN

```sql
SELECT a, fruit_a, b, fruit_b 
FROM basket_a 
RIGHT JOIN basket_b ON fruit_a = fruit_b;

/* Ausgabe:
   a    fruit_a    b    fruit_b
   ============================
   2    Orange     1   Orange 
   ____________________________ 
   1    Apple      2   Apple 
   ____________________________
   null  null      3   Watermelon
   ____________________________
   null  null      4   Pear
*/
```

```RIGHT JOIN ``` vergleicht jedes ```fruit_a``` aus ```basket_a``` mit jedem ```fruit_b``` aus ```basket_b```. Wenn sie gleich sind werden sie wie bei ```INNER JOIN``` als eine Zeile ausgegeben. Jedoch wird auch eine neue Zeile gemacht, wenn sie NICHT gleich sind (siehe null Werte).

### FULL OUTER JOIN

```sql
SELECT a, fruit_a, b, fruit_b
FROM basket_a
FULL OUTER JOIN basket_b
ON fruit_a = fruit_b;

/* Ausgabe:
   a    fruit_a    b    fruit_b
   ===============================
   1    Apple      2    Apple 
   _______________________________ 
   2    Orange     1    Orange 
   _______________________________
   3    Banana    null   null  
   _______________________________
   4    Mango     null   null
   _______________________________
   null  null      3    Watermelon
   _______________________________
   null  null      4    Pear
*/
```

```FULL OUTER JOIN ``` gibt alle Zeilen aus beiden Tabellen aus wenn sie gleich und ungleich sind. 

## Spaltenfunktionen

```sql
SELECT COUNT(*) AS anzahl_Mitarbeiter 
FROM Mitarbeiter;

SELECT COUNT(DISTINCT vorname) AS distinct_vorname
FROM Mitarbeiter;

SELECT SUM(gehalt) AS summe_gehalt
FROM Mitarbeiter;

SELECT AVG(gehalt) AS avg_gehalt
FROM Mitarbeiter;

SELECT MIN(gehalt) AS max_gehalt
FROM Mitarbeiter;

SELECT MAX(gehalt) AS min_gehalt
FROM Mitarbeiter;
```

## GROUP BY

Wenn man Spaltenfunktionen (z.B: SUM()) 

```sql
SELECT COUNT(a) AS anzahl FROM basket_a /*Funktioniert wie gewohnt*/
SELECT fruit_a, COUNT(a) AS anzahl FROM basket_a GROUP BY a; /*ohne GROUP BY würde dieses Statement nicht funktionieren*/
```

## HAVING

Folgendes ist wichtig für ```GROUP BY```:

- ```WHERE``` filtert auf **Zeilenebene VOR** dem Gruppieren

- ```HAVING``` filtert auf **Gruppenebene NACH** dem Gruppieren

Beispiel:

```sql
SELECT vorname, nachname, AVG(gehalt) AS average
FROM Mitarbeiter
WHERE geburtsdatum IN ('13.05.01','14.05.01') /*filtert alle Mitarbeiter mit Geburtstag am 13.5.01 und 14.05.01 VOR dem internen SORT und GROUP BY*/
GROUP BY nachname
HAVING AVG(gehalt) > 12000.0; /*filtert alle Nachnamen mit AVG(gehalt) > 12000 NACH dem internene SORT und GROUP BY*/
```
## Mengenoperationen

- **Vereinigung**: 
```sql
SELECT... FROM... WHERE...
UNION 
SELECT... FROM... WHERE...
```
- **Differenz**:
```sql
SELECT... FROM... WHERE...
EXCEPT 
SELECT... FROM... WHERE...
```
- **Durchschnitt**:
```sql
SELECT... FROM... WHERE...
INTERSECT 
SELECT... FROM... WHERE...
```

Beispiel:

```sql
CREATE TABLE top_rated_films
(
    title VARCHAR(50) NOT NULL,
    release_year INT
);

CREATE TABLE most_popular_films
(
    title VARCHAR(50) NOT NULL,
    release_year INT
);
```

top_rated_films:

- (Matrix, 1999)
- (IT, 2017)
- (Shrek, 2001)
- (The Godfather, 1972)

most_popular_films:
- (Shrek, 2001)
- (All eyez on Me, 2017)
- (The Godfather, 1972)


### **INTERSECT**
```sql
SELECT * FROM most_popular_films
INTERSECT
SELECT * FROM top_rated_films

/* Ausgabe:
    title           release_year
    ========================
    Shrek           2001
    ________________________
    The Godfather   1972
*/
```

### **UNION**
```sql
SELECT * FROM most_popular_films
UNION
SELECT * FROM top_rated_films

/* Ausgabe:
title           release_year
========================
Shrek           2001
________________________
The Godfather   1972
________________________
Matrix          1999
________________________
All Eyez on me  2017
________________________
IT              2017 
*/
```

### **EXCEPT**

```sql
SELECT * FROM most_popular_films
EXCEPT
SELECT * FROM top_rated_films

/* Ausgabe:
title           release_year
========================
All Eyez on me  2017
________________________
*/
```
