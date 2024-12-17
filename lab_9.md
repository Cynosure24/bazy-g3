# Zadania lab 9
# Zadanie 1
```sql
1. Z tabeli pracownik wyświetl nazwiska posortowane alfabetycznie od A do Z.

SELECT nazwisko FROM pracownik ORDER BY nazwisko ASC;
```
```sql
2. Z tabeli pracownik wyświetl imie, nazwisko, pensję dla pracowników urodzonych po roku 1979.

SELECT imie, nazwisko, pensja FROM pracownik WHERE YEAR(data_urodzenia) > 1979;
```
```sql
3. Z tabeli pracownik wyświetl wszystkie informacje dla pracowników z pensją pomiędzy 3500 a 5000 (z włączeniem tych wartości).

SELECT * FROM pracownik WHERE pensja BETWEEN 3500 AND 5000;
```
```sql
4. Z tabeli stan_magazynowy wyświetl towary, których ilość jest większa niż 10.

SELECT towar FROM stan_magazynowy WHERE ilosc > 10;
```
```sql
5. Z tabeli towar wyświetl wszystkie towary, których nazwa zaczyna się od A, B lub C.

SELECT * FROM towar WHERE nazwa_towaru LIKE 'A%' OR nazwa_towaru LIKE 'B%' OR nazwa_towaru LIKE 'C%';
```
```sql
6. Z tabeli klient wyświetl wszystkich klientów indywidualnych (nie firmy).

SELECT * FROM klient WHERE typ_klienta = 'indywidualny';
```
```sql
7. Z tabeli zamowienie wyświetl 10 najnowszych zamówień.

SELECT * FROM zamowienie ORDER BY data_zamowienia DESC LIMIT 10;
```
```sql
 8. Z tabeli pracownik wyświetl 5 najmniej zarabiających pracowników.

SELECT * FROM pracownik ORDER BY pensja ASC LIMIT 5;
```
```sql
9. Z tabeli towar wyświetl 10 najdroższych towarów, których nazwa nie zawiera litery 'a'.

SELECT * FROM towar WHERE nazwa_towaru NOT LIKE '%a%' ORDER BY cena_zakupu DESC LIMIT 10;
```
```sql
10. Z tabeli towar wyświetl towar, których jednostka miary to 'szt', posortuj po nazwie (ad A do Z) następnie po cenie zakupu malejąco.

SELECT * FROM towar WHERE jednostka_miary = 'szt' ORDER BY nazwa_towaru ASC, cena_zakupu DESC;
```
```sql
11. Stwórz nową tabelę o nazwie towary_powyzej_100, do której wstaw towary, których cena jest większa równa 100. Użyj CREATE ... SELECT.

CREATE TABLE towary_powyzej_100 AS
SELECT * FROM towar WHERE cena_zakupu >= 100;
```
```sql
12. Stwórz nową tabelę o nazwie pracownik_50_plus na podstawie tabeli pracownik z wykorzystaniem LIKE. Wstaw do tej tabeli wszystkie rekordy z tabeli pracownik gdzie wiek pracownika jest większy równy 50 lat.

CREATE TABLE pracownik_50_plus AS
SELECT * FROM pracownik WHERE YEAR(CURDATE()) - YEAR(data_urodzenia) >= 50;
```


# Zadanie 1.2
```sql
1. Wyświetl imie, nazwisko i nazwę działu każdego pracownika.

SELECT p.imie, p.nazwisko, d.nazwa_dzialu
FROM pracownik p
JOIN dzial d ON p.dzial_id = d.dzial_id;
```
```sql
2. Wyświetl nazwę towaru, nazwę kategorii oraz ilość towaru i posortuj dane po kolumnie ilość malejąco.

SELECT t.nazwa_towaru, k.nazwa_kategorii, sm.ilosc
FROM stan_magazynowy sm
JOIN towar t ON sm.towar_id = t.towar_id
JOIN kategoria k ON t.kategoria_id = k.kategoria_id
ORDER BY sm.ilosc DESC;
```
```sql
3. Wyświetl wszystkie anulowane zamówienia.

SELECT * FROM zamowienie WHERE status = 'anulowane';
```
```sql
4. Wyświetl wszystkich klientów, których adres podstawowy znajduje się w miejscowości Olsztyn.

SELECT * FROM klient WHERE adres LIKE '%Olsztyn%';
```
```sql
5. Wyświetl wszystkie nazwy jednostek miary, które nie zostały nigdy wykorzystane w tabeli stan_magazynowy.

SELECT DISTINCT jednostka_miary
FROM towar
WHERE jednostka_miary NOT IN (SELECT DISTINCT jednostka_miary FROM stan_magazynowy);
```
```sql
6. Wyświetl numer zamówienia, nazwę towaru, ilosc i cenę dla zamówień złożonych w 2018 roku.

SELECT z.nr_zamowienia, t.nazwa_towaru, zt.ilosc, t.cena_zakupu
FROM zamowienie z
JOIN zamowienie_towar zt ON z.nr_zamowienia = zt.nr_zamowienia
JOIN towar t ON zt.towar_id = t.towar_id
WHERE YEAR(z.data_zamowienia) = 2018;
```
```sql
7. Stwórz nową tabelę o nazwie towary_full_info, w której znajdą się kolumny nazwa_towaru, cena_zakupu, kategoria(tylko nazwa), ilosc , jednostka miary(nazwa).

CREATE TABLE towary_full_info AS
SELECT t.nazwa_towaru, t.cena_zakupu, k.nazwa_kategorii, sm.ilosc, t.jednostka_miary
FROM towar t
JOIN kategoria k ON t.kategoria_id = k.kategoria_id
JOIN stan_magazynowy sm ON t.towar_id = sm.towar_id;
```
```sql
8. Wyświetl pozycje zamówień dla 5 najstarszych zamówień.

SELECT * FROM zamowienie_towar WHERE nr_zamowienia IN (
    SELECT nr_zamowienia FROM zamowienie ORDER BY data_zamowienia ASC LIMIT 5
);
```
```sql
9. Wyświetl wszystkie zamówienia, które mają status inny niż zrealizowane.

SELECT * FROM zamowienie WHERE status != 'zrealizowane';
```
```sql
10. Wyświetl wszystkie adresy, których kod pocztowy został niepoprawnie zapisany (czyli niezgodnie ze wzorcem LL-LLL, gdzie L to dowolna cyfra od 0 do 9).

SELECT adres
FROM klient
WHERE adres NOT REGEXP '^[0-9]{2}-[0-9]{3}$';
```
