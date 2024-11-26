# Zadania lab 3

## Zadanie 1
```sql
a. id_postaci kl. główny, liczba samozwiększająca się
b. nazwa - ciąg znaków max 40
c. rodzaj - typ wyliczeniowy (wiking, ptak, kobieta)
d. data_ur - typ daty
e. wiek - liczba nieujemna.


CREATE TABLE postac (
    id_postaci INT AUTO_INCREMENT PRIMARY KEY,  
    nazwa VARCHAR(40),                         
    rodzaj ENUM('wiking', 'ptak', 'kobieta'),
    data_ur DATE,                               
    wiek INT CHECK (wiek >= 0)                  
);

INSERT INTO postac (nazwa, rodzaj, data_ur, wiek)
VALUES 
('Bjorn', 'wiking', '1980-05-15', 44),      
('Drozd', 'ptak', '2015-03-10', 9),         
('Teściowa', 'kobieta', '1936-11-20', 88);

UPDATE postac
SET wiek = 88
WHERE nazwa = 'Teściowa';
```


## Zadanie 2
```sql
a. id_walizki - liczba samozwiększająca się, klucz główny
b. pojemnosc - liczba nieujemna
c. kolor - typ wyliczeniowy (różowy, czerwony, tęczowy, żółty)
d. id_wlasciciela - klucz obcy odwołujący się do tabeli postać, ustawione kaskadowe
usuwanie.

CREATE TABLE walizka (
    id_walizki INT AUTO_INCREMENT PRIMARY KEY,   
    pojemnosc INT CHECK (pojemnosc >= 0),      
    kolor ENUM('różowy', 'czerwony', 'tęczowy', 'żółty') DEFAULT 'różowy', 
    id_wlasciciela INT,            
    FOREIGN KEY (id_wlasciciela) REFERENCES postac(id_postaci) ON DELETE CASCADE  
);

INSERT INTO walizka (pojemnosc, kolor, id_wlasciciela)
VALUES 
(20, 'różowy', 1),  
(30, 'czerwony', 3);

SELECT * FROM walizka;
```

## Zadanie 3
```sql
a. adres_budynku - część klucza głównego
b. nazwa_izby  - część klucza głównego
c. metraz - liczba nieujemna
d. wlasciciel - klucz obcy do tabeli postać, ustaw null w razie usunięcia.

CREATE TABLE izba (
adres_budynku VARCHAR(50),
nazwa_izby VARCHAR(30) PRIMARY KEY,
metraz SMALLINT unsigned,
wlasciciel INT,
PRIMARY KEY (adres_budynku, nazwa_izby),
FOREIGN KEY (wlasciciel) REFERENCES postac(id_postaci)
);

DESC postac;

SHOW CREATE TABLE izba;

after table izba add kolor VARCHAR(30) 
after metraz;
```

## Zadanie 4
```sql
a. id_przetworu - klucz główny
b. rok_produkcji - typ roku, domyślnie 1654
c. id_wykonawcy - klucz obcy do tabeli postać
d. zawartosc - ciąg znaków
e. dodatek - ciąg znaków - domyślnie papryczka chilli
f. id_konsumenta - klucz obcy do tabeli postać

CREATE TABLE przetwory (
    id_przetworu INT PRIMARY KEY AUTO_INCREMENT,
    rok_produkcji YEAR DEFAULT 1654,
    id_wykonawcy INT,
    zawartosc VARCHAR(255),
    dodatek VARCHAR(100) DEFAULT 'papryczka chilli',
    id_konsumenta INT,
    FOREIGN KEY (id_wykonawcy) REFERENCES postac(id_postaci),
    FOREIGN KEY (id_konsumenta) REFERENCES postac(id_postaci)
);

INSERT INTO przetwory (rok_produkcji, id_wykonawcy, zawartosc, dodatek, id_konsumenta)
VALUES (1654, 1, 'bigos', 'papryczka chilli', 2);
```

## Zadanie 5
```sql

1. Wstaw 5 wikingów do tabeli postaci.

INSERT INTO postac (imie) 
VALUES 
('Bjorn'),
('Drozd'),
('Leif'),
('Knut'),
('Olaf');
```
```sql
2. Stwórz tabelę statek z polami:
a. nazwa statku - klucz główny
b. rodzaj statku - typ wyliczeniowy
c. data wodowania - typ daty
d. max_ladownosc - liczba dodatnia

CREATE TABLE statek (
    nazwa_statku VARCHAR(255) PRIMARY KEY,
    rodzaj_statku ENUM('wiking', 'handlowy', 'transportowy', 'rybacki') NOT NULL,
    data_wodowania DATE,
    max_ladownosc INT CHECK (max_ladownosc > 0)
);
```
```sql
3. Dodaj dwa statki do tabeli.

INSERT INTO statek (nazwa_statku, rodzaj_statku, data_wodowania, max_ladownosc)
VALUES
('Długi Drakkar', 'wiking', '830-06-01', 50)
('Złoty Wiatr', 'handlowy', '1020-05-15', 100);
```
```sql
4. Dodaj pola do tabeli postac:
a. funkcja - ciąg znaków.

ALTER TABLE postac
ADD COLUMN funkcja VARCHAR(255);
```
```sql
5. Zmień funkcję u Bjorna na kapitan.

UPDATE postac
SET funkcja = 'kapitan'
WHERE id_postaci = 1;
```
```sql
6. Dodaj klucz obcy w tabeli postać odwołujący się do statku.

ALTER TABLE postac
ADD COLUMN nazwa_statku VARCHAR(255),
ADD CONSTRAINT fk_nazwa_statku FOREIGN KEY (nazwa_statku) REFERENCES statek(nazwa_statku);
```
```sql
7. Powsadzaj wikingów oraz drozda na statki.

UPDATE postac
SET nazwa_statku = 'Dlugi Drakkar'
WHERE id_postaci IN (1, 2); -- Bjorn i Drozd
```
```sql
8. Usuń izbę spiżarnia z tabeli izba

UPDATE postac
SET nazwa_statku = 'Złoty Wiatr'
WHERE id_postaci IN (3, 4, 5); -- Leif, Knut, Olaf
```
```sql
9. Usuń tabelę izba.

ALTER TABLE izba
DROP COLUMN spiżarnia;
```
```sql
DROP TABLE izba;
```
