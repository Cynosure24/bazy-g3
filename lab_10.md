# Zadania lab 10
# 1. Funkcje, agregacja i grupowanie.
# Zadanie 1
```sql
Wyświetl imię i nazwisko każdego pracownika i jego rok urodzenia.

SELECT imie AS First_Name, nazwisko AS Last_Name, YEAR(data_urodzenia) AS Birth_Year
FROM pracownicy;
```

# Zadanie 2
```sql
Wyświetl imię i nazwisko pracowników oraz ich wiek w latach (bez uwzględniania miesiąca i dnia urodzenia).

SELECT imie AS First_Name, nazwisko AS Last_Name, YEAR(CURDATE()) - YEAR(data_urodzenia) AS Age
FROM pracownicy;
```

# Zadanie 3
```sql
Wyświetl nazwę działu i liczbę pracowników przypisanych do każdego z nich.

SELECT dzial AS Department_Name, COUNT(*) AS Employee_Count
FROM pracownicy
GROUP BY dzial;
```

# Zadanie 4
```sql
Wyświetl nazwę kategorii oraz liczbę produktów w każdej z nich.

SELECT kategoria AS Category_Name, COUNT(*) AS Product_Count
FROM produkty
GROUP BY kategoria;
```

# Zadanie 5
```sql
Wyświetl nazwę kategorii i w kolejnej kolumnie listę wszystkich produktów należącej do każdej z nich.

SELECT kategoria AS Category_Name, GROUP_CONCAT(produkt SEPARATOR ', ') AS Product_List
FROM produkty
GROUP BY kategoria;
```

# Zadanie 6
```sql
Wyświetl średnie zarobki pracowników z zaokrągleniem do 2 miejsc po przecinku (funkcja round).

SELECT ROUND(AVG(pensja), 2) AS Average_Salary
FROM pracownicy;
```

# Zadanie 7
```sql
Wyświetl średnie zarobki pracowników, którzy pracują co najmniej od 5 lat.

SELECT ROUND(AVG(pensja), 2) AS Average_Salary_5_Years
FROM pracownicy
WHERE YEAR(CURDATE()) - YEAR(data_zatrudnienia) >= 5;
```

# Zadanie 8
```sql
Wyświetl 10 najczęściej sprzedawanych produktów *.
zapisz 2 rozwiązania:
1. Produkty najczęściej pojawiające się w zamówieniach zrealizowanych.
2. Najwięsza sumaryczna ilość danego prosuktu ze wszystkich zrealizowanych zamówień.

1. 
SELECT produkt AS Product_Name, COUNT(*) AS Appearance_Count
FROM zamowienia
WHERE status = 'zrealizowane'
GROUP BY produkt
ORDER BY COUNT(*) DESC
LIMIT 10;

2.
SELECT produkt AS Product_Name, SUM(ilosc) AS Total_Quantity
FROM zamowienia
WHERE status = 'zrealizowane'
GROUP BY produkt
ORDER BY SUM(ilosc) DESC
LIMIT 10;
```

# Zadanie 9
```sql
Wyświetl numer zamówienia, jego wartość (suma wartości wszystkich jego pozycji) zarejestrowanych w pierwszym kwartale 2017 roku.

SELECT nr_zamowienia AS Order_Number, SUM(wartosc) AS Total_Value
FROM zamowienia
WHERE data_zamowienia BETWEEN '2017-01-01' AND '2017-03-31'
GROUP BY nr_zamowienia;
```

# Zadanie 10
```sql
Wyświetl imie, nazwisko i sumę wartości zamówień, które dany pracownik dodał. Posortuj malejąco po sumie.

SELECT imie AS First_Name, nazwisko AS Last_Name, SUM(wartosc) AS Total_Order_Value
FROM pracownicy
JOIN zamowienia ON pracownicy.id = zamowienia.id_pracownika
GROUP BY pracownicy.id
ORDER BY SUM(wartosc) DESC;
```

# Zadanie 11
```sql
Wyświetl nazwę działu i minimalną, maksymalną i średnią wartość pensji w każdym dziale.

SELECT dzial AS Department_Name, MIN(pensja) AS Min_Salary, MAX(pensja) AS Max_Salary, ROUND(AVG(pensja), 2) AS Avg_Salary
FROM pracownicy
GROUP BY dzial;
```

# Zadanie 12
```sql
Wyświetl pełną nazwę klienta, wartość zamówienia dla 10 najwyższych wartości zamówienia.

SELECT CONCAT(imie, ' ', nazwisko) AS Full_Name, MAX(wartosc) AS Order_Value
FROM klienci
JOIN zamowienia ON klienci.id = zamowienia.id_klienta
GROUP BY klienci.id
ORDER BY MAX(wartosc) DESC
LIMIT 10;
```

# Zadanie 13
```sql
Wyświetl wartość przychodu dla każdego roku. Dane posortuj malejąco według sumy wartości zamówień.

SELECT YEAR(data_zamowienia) AS Year, SUM(wartosc) AS Revenue
FROM zamowienia
GROUP BY YEAR(data_zamowienia)
ORDER BY SUM(wartosc) DESC;
```

# Zadanie 14
```sql
Wyświetl sumę wartości wszystkich anulowanych zamówień.

SELECT SUM(wartosc) AS Canceled_Order_Value
FROM zamowienia
WHERE status = 'anulowane';
```

# Zadanie 15
```sql
Wyświetl liczbę zamówień i wartość zamówień dla każdej miejscowości z podstawowego adresu klienta.

SELECT miasto AS City, COUNT(*) AS Order_Count, SUM(wartosc) AS Total_Value
FROM zamowienia
JOIN klienci ON zamowienia.id_klienta = klienci.id
GROUP BY miasto;
```

# Zadanie 16
```sql
Wyświetl dotychczasowy dochód firmy biorąc pod uwagę tylko zamówienia zrealizowane.

SELECT SUM(wartosc) AS Total_Revenue
FROM zamowienia
WHERE status = 'zrealizowane';
```

# Zadanie 17
```sql
Policz i wyświetl dochód (przychód z zamówień - cena zakupu towaru) w każdym roku działalności firmy.

SELECT YEAR(data_zamowienia) AS Year, SUM(wartosc - koszt_zakupu) AS Profit
FROM zamowienia
GROUP BY YEAR(data_zamowienia);
```

# Zadanie 18
```sql
Wyświetl wartość aktualnego stanu magazynowego z podziałem na kategorię produktów.

SELECT kategoria AS Category_Name, SUM(ilosc * cena_jednostkowa) AS Inventory_Value
FROM produkty
GROUP BY kategoria;
```

# Zadanie 19
```sql
Przygotuj zapytanie, które wyświetli dane w poniższej postaci (policz ilu pracowników urodziło się w danym miesiącu - uwaga na porządek sortowania).

SELECT MONTHNAME(data_urodzenia) AS Month, COUNT(*) AS Employee_Count
FROM pracownicy
GROUP BY MONTH(data_urodzenia)
ORDER BY MONTH(data_urodzenia);
```

# Zadanie 20
```sql
Wyświetl imię i nazwisko pracownika i koszt jaki poniósł pracodawca od momentu jego zatrudnienia.
* Nie aż tak trudne jak poszukać odpowiedniej funkcji operującej na datach.

SELECT imie AS First_Name, nazwisko AS Last_Name, DATEDIFF(CURDATE(), data_zatrudnienia) * pensja AS Total_Cost
FROM pracownicy;
```
