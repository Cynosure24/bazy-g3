# Zadania lab 11
# Zadanie 1
```sql
CREATE TABLE student (
    id_studenta INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    imie VARCHAR(100) NOT NULL,
    nazwisko VARCHAR(100) NOT NULL,
    rok_studiow TYNYINT UNSIGNED,
    data_urodzenia DATE
);
```

# Zadanie 2
```sql
CREATE TABLE kierunek (
    id_kienunku INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    nazwa_kienunku VARCHAR(200) NOT NULL
);
```

# Zadanie 3
```sql
CREATE TABLE student_na_kierunku (
    student INT UNSIGNED,
    kierunek INT UNSIGNED,
    PRIMARY KEY (student, kierunek),
    FOREIGN KEY (student) REFERENCES student(id_studenta) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (kierunek) REFERENCES kierunek(id_kierunku) ON DELETE CASCADE ON UPDATE CASCADE
);
```

# Zadanie 4
```sql
CREATE TABLE przedmiot (
    id_przedmiotu INT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    nazwa_przedmiotu VARCHAR(200) NOT NULL,
    opis TEXT DEFAULT NULL
);
```
# Zadanie 5
```sql
CREATE TABLE kierunek_has_przedmioty (
    kierunek INT UNSIGNED,
    przedmiot INT UNSIGNED,
    obowiazkowy BOOLEAN DEFAULT TRUE,
    PRIMARY KEY (kierunek, przedmiot),
    FOREIGN KEY (kierunek) REFERENCES kierunek(id_kierunku) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (przedmiot) REFERENCES przedmiot(id_przedmiotu) ON DELETE CASCADE ON UPDATE CASCADE
);
```
# Zadanie 6
```sql
INSERT INTO student (imie, nazwisko, rok_studiow, data_urodzenia) VALUES
('Tadei', 'Kowalski', 1, '2000-01-15'),
('Johana', 'Nowak', 2, '1999-03-22'),
('Piotr', 'Wiśniewski', 3, '1998-07-11'),
('Izabela', 'Zielińska', 1, '2001-05-30');

INSERT INTO kierunek (nazwa_kierunku) VALUES
('Informatyka'),
('Matematyka');


INSERT INTO student_na_kierunku (student, kierunek) VALUES
(1, 1),
(2, 1),
(3, 2),
(4, 1);

INSERT INTO przedmiot (nazwa_przedmiotu, opis) VALUES
('Algebra', NULL),
('Analiza matematyczna', 'Zaawansowana analiza funkcji jednej zmiennej.'),
('Programowanie', 'Podstawy programowania w Pythonie.'),
('Bazy danych', 'Systemy zarządzania bazami danych.');


INSERT INTO kierunek_has_przedmioty (kierunek, przedmiot, obowiazkowy) VALUES
(1, 1, TRUE),
(1, 2, TRUE),
(1, 3, TRUE),
(2, 4, FALSE);
```
# Zadanie 7
```sql
ALTER TABLE przedmiot
MODIFY COLUMN opis TEXT DEFSULT 'Brak opisu';
```
# Zadanie 8
```sql
ALTER TABLE student ADD indeks INT UNSIGNED AUTO_INCREMENT UNIQUE;
ALTER TABLE student DROP PRIMARY KEY;
ALTER TABLE student ADD PRIMARY KEY (indeks);
```
# Zadanie 9
```sql
ALTER TABLE kierunek_has_przedmoity
ADD semestr VARCHAR(10) NOT NULL,
ADD rok_studiow TINYINT UNSIGNED NOT NULL;

UPDATE kierunek_has_przedmioty 
SET semestr = '2024Z', rok_studiow = 1 WHERE kierunek = 1 AND przedmiot = 1;

UPDATE kierunek_has_przedmioty 
SET semestr = '2024Z', rok_studiow = 1 WHERE kierunek = 1 AND przedmiot = 2;

UPDATE kierunek_has_przedmioty 
SET semestr = '2024L', rok_studiow = 1 WHERE kierunek = 1 AND przedmiot = 3;

UPDATE kierunek_has_przedmioty 
SET semestr = '2024L', rok_studiow = 2 WHERE kierunek = 2 AND przedmiot = 4;
```

# Zadanie 10
```sql
SELECT
    s.indeks AS indeks_studenta,
    s.imie,
    k.nazwa_kierunku,
    khp.semestr,
    p.nazwa_przedmiotu
FROM
    student s
JOIN
    student_na_kierunku snk ON s.id_studenta = snk.student
JOIN
    kierunek k ON snk.kierunek = k.id_kierunku
JOIN
    kierunek_has_przedmoity khp ON k.id_kierunku = khp.kierunek
JOIN
    przedmiot p ON khp.przedmiot = p.id_przedmiotu;
```
