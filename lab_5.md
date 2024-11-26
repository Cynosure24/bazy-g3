# Zadania lab 5
# Zadanie 1
```sql
 1 Skopiuj tabele 'kreatura','zasob','ekwipunek' z bazy 'wikingowie' do swojej bazy.
 2 Wypisz wszystkie rekordy z tabeli 'zasob'.
 3 Wypisz wszystkie rekordy z tabeli 'zasob' gdzie typ to jedzenie.
 4 Wypisz 'idZasobu', 'ilosc', dla kreatur o id 1,3,5.

-- 1
CREATE TABLE moja_baza.kreatura AS SELECT * FROM wikingowie.kreatura;

-- 2
CREATE TABLE moja_baza.zasob AS SELECT * FROM wikingowie.zasob;

-- 3
CREATE TABLE moja_baza.ekwipunek AS SELECT * FROM wikingowie.ekwipunek;

-- 4
SELECT * FROM moja_baza.zasob;
SELECT * FROM moja_baza.zasob WHERE typ = 'jedzenie';
SELECT idZasobu, ilosc
FROM moja_baza.zasob
WHERE idKreatury IN (1, 3, 5);
```

## Zadanie 2
```sql
1 Wyświetl kreatury, które nie są wiedźmą i dźwigają co najmniej 50kg.
2 Wyświetl zasoby, które ważą pomiędzy 2 a 5 kg.
3 Wyświetl kreatury, których nazwa zawiera 'or' i które dźwigają między 30kg a 70kg.

-- 1
SELECT *
FROM moja_baza.kreatura
WHERE typ != 'wiedźma'
AND waga >= 50;

-- 2
SELECT *
FROM moja_baza.zasob
WHERE waga BETWEEN 2 AND 5;

-- 3
SELECT *
FROM moja_baza.kreatura
WHERE nazwa LIKE '%or%'
AND waga BETWEEN 30 AND 70;
```
## Zadanie 3
```sql
 1 Wyświetl zasoby, które zostały pozyskane w miesiącach lipcu i sierpniu.
 2 Wyświetl zasoby, które mają zdefiniowany rodzaj od najlżejszego do najcięższego.
 3 Wyświetl 5 najstarszych kreatur.

-- 1
SELECT *
FROM moja_baza.zasob
WHERE MONTH(data_pozyskania) IN (7, 8);

-- 2
SELECT *
FROM moja_baza.zasob
WHERE rodzaj IS NOT NULL
ORDER BY waga ASC;

-- 3 
SELECT *
FROM moja_baza.kreatura
ORDER BY data_urodzenia ASC
LIMIT 5;
```

## Zadanie 4
```sql
 1 Wyświetl unikalne rodzaje zasobów.
 2 Wyświetl jako jedną kolumnę nazwę i rodzaj kreatury (w postaci: nazwa - rodzaj), gdzie rodzaj rozpoczyna się od 'wi'.
 3 Wyświetl zasoby z całkowitą wagą danego zasobu (ilość * waga) dla zasobów pozyskanych w latach 2000-2007.
    
-- 1
SELECT DISTINCT rodzaj
FROM moja_baza.zasob;

-- 2 
SELECT CONCAT(nazwa, ' - ', rodzaj) AS nazwa_rodzaj
FROM moja_baza.kreatura
WHERE rodzaj LIKE 'wi%';

-- 3 
SELECT nazwa, rodzaj, (ilość * waga) AS całkowita_waga
FROM moja_baza.zasob
WHERE YEAR(data_pozyskania) BETWEEN 2000 AND 2007;
```

## Zadanie 5
```sql
 1 Zakładając, że każdy rodzaj jedzenia to 30% odpadu, wyświetl masę właściwego jedzenia (netto) oraz wagę odpadków.
 2 Wyświetl zasoby, które nie mają rodzaju.
 3 Wyświetl wszystkie unikalne rodzaje zasobów, których nazwa zaczyna się od 'Ba' lub kończy się na 'os'. Dane posortuj alfabetycznie.

-- 1
SELECT 
    nazwa,
    waga * 0.7 AS waga_netto,   
    waga * 0.3 AS waga_odpadki
FROM moja_baza.zasob
WHERE typ = 'jedzenie';

-- 2 
SELECT *
FROM moja_baza.zasob
WHERE rodzaj IS NULL;

-- 3
SELECT DISTINCT rodzaj
FROM moja_baza.zasob
WHERE nazwa LIKE 'Ba%' OR nazwa LIKE '%os'
ORDER BY rodzaj;
```
