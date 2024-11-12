# Zadania lab 4

## Zadanie 1
```sql
a. uśmierć dwóch najstarszych wikingów, ale to nie może być Bjorn (DELETE z WHERE)
b. usuń klucz główny z tabeli postac - może być potrzebnych kilka komend (np. odłączenie kluczy obcych)

#a
DELETE FROM postac
WHERE id IN (
    SELECT id FROM postac
    WHERE nazwa != 'Bjorn'
    ORDER BY wiek DESC
    LIMIT 2
);

ALTER TABLE inna_tabela DROP CONSTRAINT fk_postac_id;

#b
ALTER TABLE postac DROP CONSTRAINT pk_postac_id;

ALTER TABLE postac DROP PRIMARY KEY;
```

## Zadanie 2
```sql
a. do tabeli postac dodaj pole pesel składające się z 11 znaków i ustaw to pole na klucz główny
b. w tabeli postac zmień pole rodzaj, tak, aby możliwe było dodanie syreny
c. wstaw do tabeli postac syrenę o nazwie Gertruda Nieszczera

#a
ALTER TABLE postac
ADD COLUMN pesel CHAR(11);

ALTER TABLE postac
ADD PRIMARY KEY (pesel);

#b
ALTER TABLE postac
MODIFY COLUMN rodzaj ENUM('wiking', 'syrena');

#c
INSERT INTO postac (nazwa, rodzaj)
VALUES ('Gertruda Nieszczera', 'syrena');
```

## Zadanie 3
```sql
a. Wszystkie postacie, które mają w swojej nazwie 'a', wsadź na statek Bjorna
b. Zmniejsz ładowność wszystkim statkom o 30%, których data wodowania była w XX wieku
c. ustaw warunek sprawdzający czy wiek postaci nie jest większy od 1000


#a
UPDATE postac
SET statek = 'Bjorn'
WHERE nazwa LIKE '%a%';

#b
UPDATE statek
SET ladownosc = ladownosc * 0.7 
WHERE YEAR(data_wodowania) BETWEEN 1900 AND 1999;

#c
ALTER TABLE postac
ADD CONSTRAINT chk_wiek CHECK (wiek <= 1000);
```

## Zadanie 4
```sql
a. Do postaci dodaj węża Loko, tylko nie wsadzaj go na statek
b. Stwórz nową tabelę na podstawie tabeli postac (dokładnie takie same pola), nazwij ją marynarz - dodaj do tej tabeli wszystkie postacie które mają zdefiniowany statek
c. dostosuj odpowiednio klucze główne i obce

#a
INSERT INTO postac (nazwa, rodzaj)
VALUES ('Loko', 'wąż');

#b
CREATE TABLE marynarz AS
SELECT * FROM postac
WHERE statek IS NOT NULL;

#c
ALTER TABLE marynarz
ADD PRIMARY KEY (id);

ALTER TABLE marynarz
ADD CONSTRAINT fk_marynarz_statek FOREIGN KEY (statek) REFERENCES statek(id);
```
## Zadanie 5
```sql
a. wysadź wszystkich ze statku
b. uśmierć jednego wikinga
c. zniszcz wszystkie statki
d. usuń tabelę statek
e. stwórz tabelę zwierz z polami id - klucz główny z autoinkrementacją, nazwa - ciąg znaków, wiek - liczba
f. przekopiuj z tabeli postac wszystkie zwierzaki

#a
UPDATE postac
SET statek = NULL

#b
DELETE FROM postac
WHERE rodzaj = 'wiking'
LIMIT 1;

#c
DELETE FROM statek;

#d
DROP TABLE statek;

#e
CREATE TABLE zwierz (
id INT AUTO_INCREMENT PRIMARY KEY,
nazwa VARCHAR(255),
WIEK INT
);

#f
INSERT INTO zwierz (nazwa, wiek)
SELECT nazwa, wiek FROM postac
WHERE rodzaj = 'zwierzak';
```
