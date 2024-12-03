# Zadania lab 7
# Zadanie 1
```sql
1. Przekopiuj jeszcze raz z bazy wikingowie rekordy z tabeli kreatura, przekopiuj dodatkowo tabele: uczestnicy, etapy_wyprawy, sektor, wyprawa, wraz z danymi.
2. Wypisz nazwy kreatur, które nie uczestniczyły w żadnej wyprawie.
3. Dla każdej wyprawy wypisać jej nazwę oraz sumę ilości ekwipunku, jaka została zabrana przez uczestników tej wyprawy.

-- 1 
INSERT INTO kreatura
SELECT * FROM wikingowie.kreatura;

INSERT INTO uczestnicy
SELECT * FROM wikingowie.uczestnicy;

INSERT INTO etapy_wyprawy
SELECT * FROM wikingowie.etapy_wyprawy;

INSERT INTO sektor
SELECT * FROM wikingowie.sektor;

INSERT INTO wyprawa
SELECT * FROM wikingowie.wyprawa;

-- 2
SELECT k.nazwa_kreatury
FROM kreatura k
LEFT JOIN uczestnicy u ON k.id_kreatury = u.id_kreatury
WHERE u.id_kreatury IS NULL;

-- 3
SELECT w.nazwa_wyprawy, SUM(e.ilosc_ekwipunku) AS suma_ekwipunku
FROM wyprawa w
JOIN uczestnicy u ON w.id_wyprawy = u.id_wyprawy
JOIN ekwipunek e ON u.id_uczestnika = e.id_uczestnika
GROUP BY w.nazwa_wyprawy;
```

# Zadanie 2
```sql
1. Dla każdej wyprawy wypisz jej nazwę, liczbę uczestników, oraz nazwy tych uczestników w jednej linii.
2. Wypisz kolejne etaty wszystkich wypraw wraz z nazwami sektorów, sortując najpierw według daty początku wyprawy, a następnie według kolejności występowania etapów. W każdym etapie określ nazwę kierownika danej wyprawy.

-- 1
SELECT w.nazwa_wyprawy,
       COUNT(u.id_uczestnika) AS liczba_uczestnikow,
       GROUP_CONCAT(u.nazwa_uczestnika SEPARATOR ', ') AS uczestnicy
FROM wyprawa w
JOIN uczestnicy u ON w.id_wyprawy = u.id_wyprawy
GROUP BY w.id_wyprawy;

-- 2
SELECT w.nazwa_wyprawy,
       e.numer_etapu,
       e.nazwa_etapu,
       s.nazwa_sektora,
       k.nazwa_uczestnika AS kierownik
FROM wyprawa w
JOIN etapy_wyprawy e ON w.id_wyprawy = e.id_wyprawy
JOIN sektor s ON e.id_sektora = s.id_sektora
JOIN uczestnicy u ON e.id_kierownika = u.id_uczestnika
JOIN kreatura k ON u.id_kreatury = k.id_kreatury
ORDER BY w.data_poczatku, e.numer_etapu;
```

# Zadanie 3
```sql
1. Wypisać ile razy dany sektor był odwiedzany podczas wszystkich wypraw (nazwa sektora, ilość odwiedzin). Jeśli nie był odwiedzony ani razu, wpisz zero.
2. W zależności od ilości wypraw, w jakich brała udział dana kreatura wypisz: nazwa kreatury, 'brał udział w wyprawie' - gdy liczba > 0, 'nie brał udziału w wyprawie', gdy równa zero.

-- 1
SELECT s.nazwa_sektora,
       IFNULL(COUNT(e.id_etapu), 0) AS liczba_odwiedzin
FROM sektor s
LEFT JOIN etapy_wyprawy e ON s.id_sektora = e.id_sektora
GROUP BY s.id_sektora;

-- 2
SELECT k.nazwa_kreatury,
       IFNULL(COUNT(u.id_wyprawy), 0) AS liczba_wypraw,
       IFNULL(CASE 
                 WHEN COUNT(u.id_wyprawy) > 0 THEN 'brał udział w wyprawie'
                 ELSE 'nie brał udziału w wyprawie'
               END, 'nie brał udziału w wyprawie') AS status
FROM kreatura k
LEFT JOIN uczestnicy u ON k.id_kreatury = u.id_kreatury
GROUP BY k.id_kreatury;
```

# Zadanie 4
```sql
1. Dla każdej wyprawy wypisz jej nazwę oraz sumę liczby znaków, które zostały użyte przy pisaniu dziennika, jeśli ta liczba znaków jest mniejsza od 400.
2. Dla każdej wyprawy podaj średnią wagę zasobów, jakie były niesione przez uczestników tej wyprawy.

-- 1
SELECT w.nazwa_wyprawy,
       SUM(LENGTH(d.dziennik)) AS suma_znakow
FROM wyprawa w
JOIN dziennik d ON w.id_wyprawy = d.id_wyprawy
GROUP BY w.id_wyprawy
HAVING SUM(LENGTH(d.dziennik)) < 400;

-- 2
SELECT w.nazwa_wyprawy,
       AVG(e.waga * e.ilosc) AS srednia_waga
FROM wyprawa w
JOIN uczestnicy u ON w.id_wyprawy = u.id_wyprawy
JOIN ekwipunek e ON u.id_uczestnika = e.id_uczestnika
GROUP BY w.id_wyprawy;
```

# Zadanie 5
```sql
1. Wypisać nazwę kreatury oraz ile miała dni (wiek w dniach) w momencie rozpoczęcia wyprawy, dla wypraw, które przechodziły przez chatkę dziadka.

-- 1
SELECT k.nazwa_kreatury,
       DATEDIFF(w.data_poczatku, k.data_urodzenia) AS wiek_w_dniach
FROM kreatura k
JOIN uczestnicy u ON k.id_kreatury = u.id_kreatury
JOIN wyprawa w ON u.id_wyprawy = w.id_wyprawy
JOIN etapy_wyprawy e ON w.id_wyprawy = e.id_wyprawy
JOIN sektor s ON e.id_sektora = s.id_sektora
WHERE s.nazwa_sektora = 'Chatka Dziadka'; 
```
