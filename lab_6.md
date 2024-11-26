# Zadania lab 6
# Zadanie 1
```sql
1. wyświetl średnią wagę wszystkich wikingów
2. Wyświetl średnią wagę oraz liczbę kreatur dla każdego rodzaju
3. wyświetl średni wiek dla każdego rodzaju kreatury

-- 1
SELECT AVG(waga) AS srednia_waga
FROM wikingowie;

-- 2
SELECT rodzaj, AVG(waga) AS srednia_waga, COUNT(*) AS liczba_kreatur
FROM kreatury
GROUP BY rodzaj;

-- 3
SELECT rodzaj, AVG(wiek) AS sredni_wiek
FROM kreatury
GROUP BY rodzaj;
```

# Zadanie 2
```sql
1. Dla każdego rodzaju zasobu wyświetlić sumę wag tego zasobu.
2. Dla każdej nazwy zasobu wyświetlić średnią wagę, jeśli ilość jest równa co najmniej 4 oraz jeśli ta suma wag jest większa od 10.
3. Wyświetlić ile jest różnych nazw dla każdego rodzaju zasobu, jeśli minimalna liczba zasobu jest większa od 1.

-- 1
SELECT rodzaj, SUM(waga) AS suma_wag
FROM zasoby
GROUP BY rodzaj;


-- 2
SELECT nazwa, AVG(waga) AS srednia_waga
FROM zasoby
GROUP BY nazwa
HAVING COUNT(*) >= 4 AND SUM(waga) > 10;


-- 3
SELECT rodzaj, COUNT(DISTINCT nazwa) AS liczba_roznych_nazw
FROM zasoby
GROUP BY rodzaj
HAVING COUNT(*) > 1;
```

# Zadanie 3
```sql
1. Wyświetlić dla każdej kreatury ilości zasobów jakie niesie.
2. Wyświetlić dla każdej kreatury nazwy zasobów jakie posiada.
3. Wyświetlić kreatury, które nie posiadają żadnego ekwipunku.

-- 1
SELECT k.kreatura_id, COUNT(e.zasob_id) AS liczba_zasobow
FROM kreatury k
LEFT JOIN ekwipunek e ON k.kreatura_id = e.kreatura_id
GROUP BY k.kreatura_id;


-- 2
SELECT k.kreatura_id, GROUP_CONCAT(z.nazwa) AS zasoby
FROM kreatury k
LEFT JOIN ekwipunek e ON k.kreatura_id = e.kreatura_id
LEFT JOIN zasoby z ON e.zasob_id = z.zasob_id
GROUP BY k.kreatura_id;


-- 3
SELECT k.kreatura_id
FROM kreatury k
LEFT JOIN ekwipunek e ON k.kreatura_id = e.kreatura_id
WHERE e.zasob_id IS NULL;
```

# Zadanie 4
```sql
1. Wyświetlić nazwy wikingów, którzy urodzili się w latach 70-tych XVII wieku oraz nazwy zasobów, które posiadają (użyj natural joina jeśli się da).
2. Wyświetlić nazwy 5 najmłodszych kreatur, które w ekwipunku posiadają jedzenie.
3. Wypisz obok siebie nazwy kreatur, których numer idKreatury różni się o 5 (np. Bjorn - Astrid, Brutal - Ibra itd.).

-- 1
SELECT w.imie, z.nazwa
FROM wikingowie w
NATURAL JOIN ekwipunek e
NATURAL JOIN zasoby z
WHERE YEAR(w.data_urodzenia) BETWEEN 1670 AND 1679;


-- 2
SELECT k.imie, z.nazwa
FROM kreatury k
JOIN ekwipunek e ON k.id_kreatury = e.id_kreatury
JOIN zasoby z ON e.id_zasobu = z.id_zasobu
WHERE z.nazwa = 'jedzenie'
ORDER BY k.data_urodzenia DESC
LIMIT 5;


-- 3
SELECT k1.imie AS kreatura_1, k2.imie AS kreatura_2
FROM kreatury k1
JOIN kreatury k2 ON ABS(k1.id_kreatury - k2.id_kreatury) = 5
WHERE k1.id_kreatury < k2.id_kreatury;
```

# Zadanie 5
```sql
1. Dla każdego rodzaju kreatury wyświetlić średnią wagę zasobów, jaką posiadają w ekwipunku, jeśli kreatura nie jest małpą ani wężem i ilość ekwipunku jest poniżej 30.
2. Dla każdego rodzaju kreatury wyświetlić nazwę, datę urodzenia i rodzaj najmłodszej i najstarszej kreatury.

-- 1
SELECT k.rodzaj, AVG(z.waga) AS srednia_waga
FROM kreatury k
JOIN ekwipunek e ON k.id_kreatury = e.id_kreatury
JOIN zasoby z ON e.id_zasobu = z.id_zasobu
GROUP BY k.rodzaj
HAVING k.rodzaj NOT IN ('małpa', 'wąż')
   AND COUNT(e.id_zasobu) < 30;


-- 2
SELECT k.rodzaj,
       (SELECT k1.imie FROM kreatury k1 WHERE k1.rodzaj = k.rodzaj ORDER BY k1.data_urodzenia DESC LIMIT 1) AS najmłodsza,
       (SELECT k2.imie FROM kreatury k2 WHERE k2.rodzaj = k.rodzaj ORDER BY k2.data_urodzenia ASC LIMIT 1) AS najstarsza,
       (SELECT k3.data_urodzenia FROM kreatury k3 WHERE k3.rodzaj = k.rodzaj ORDER BY k3.data_urodzenia DESC LIMIT 1) AS data_najmłodszej,
       (SELECT k4.data_urodzenia FROM kreatury k4 WHERE k4.rodzaj = k.rodzaj ORDER BY k4.data_urodzenia ASC LIMIT 1) AS data_najstarszej
FROM kreatury k
GROUP BY k.rodzaj;
```

