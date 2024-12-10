# Zadania lab 8
# Zadanie 1
```sql
1. Napisz wyzwalacz, który przed wstawieniem lub modyfikacją tabeli kreatura sprawdzi czy waga jest większa od zera, a jeżeli nie jest to ustaw wagę na 0.

--- 1
DELIMITER $$

CREATE TRIGGER przed_wstawieniem_lub_modyfikacja_kreatura
BEFORE INSERT OR UPDATE ON kreatura
FOR EACH ROW
BEGIN
    IF NEW.waga <= 0 THEN
        SET NEW.waga = 0;
    END IF;
END $$

DELIMITER ;
```
# Zadanie 2
```sql
1. Stwórz tabelę archiwum_wypraw z polami id_wyprawy, nazwa, data_rozpoczecia, data_zakonczenia, kierownik (varchar), do której będą wstawiane rekordy po usunięciu z tabeli wyprawa.
Do kolumny kierownik wstawiane jest nazwa kreatury na podstawie usuwanego id_kreatury.

--- 1
CREATE TABLE archiwum_wypraw (
    id_wyprawy INT,
    nazwa VARCHAR(255),
    data_rozpoczecia DATE,
    data_zakonczenia DATE,
    kierownik VARCHAR(255)
);

DELIMITER $$

CREATE TRIGGER przed_usunieciem_wyprawa
BEFORE DELETE ON wyprawa
FOR EACH ROW
BEGIN
    INSERT INTO archiwum_wypraw (id_wyprawy, nazwa, data_rozpoczecia, data_zakonczenia, kierownik)
    SELECT OLD.id_wyprawy, OLD.nazwa, OLD.data_rozpoczecia, OLD.data_zakonczenia,
           (SELECT nazwa FROM kreatura WHERE id_kreatury = OLD.id_kreatury)
    FROM DUAL;
END $$

DELIMITER ;
```

# Zadanie 3
```sql
1. Napisz procedurę o nazwie "eliksir_sily", która będzie podnosiła wartość pola udzwig z tabeli kreatura o 20% na podstawie id_kreatury przekazywanego jako parametr.
2. Napisz funkcję, która będzie pobierała tekst i zwracała go z wielkich liter.

--- 1
DELIMITER $$

CREATE PROCEDURE eliksir_sily(IN kreatura_id INT)
BEGIN
    UPDATE kreatura
    SET udzwig = udzwig * 1.20
    WHERE id_kreatury = kreatura_id;
END $$

DELIMITER ;

--- 2
DELIMITER $$

CREATE FUNCTION zamien_na_wielkie(tekst VARCHAR(255)) 
RETURNS VARCHAR(255)
DETERMINISTIC
BEGIN
    RETURN UPPER(tekst);
END $$

DELIMITER ;
```
# Zadanie 4
```sql
1. Stwórz tabelę "system_alarmowy" z polami id_alarmu, wiadomosc.
2. Dodaj wyzwalacz, który będzie sprawdzał czy w tabeli wyprawy pojawiła się misja, w której bierze udział teściowa oraz czy jednym z sektorów misji jest domek dziadka.
Jeżeli powyższy warunek zaistnieje wyzwalacz wstawi rekord do tabeli "system_alarmowy" z treścią "Teściowa nadchodzi !!!".

--- 1
CREATE TABLE system_alarmowy (
    id_alarmu INT AUTO_INCREMENT PRIMART KEY,
    wiadomosc VARCHAR(225)
);

--- 2

DELIMITER $$

CREATE TRIGGER alarm_trigger
AFTER INSERT ON wyprawy
FOR EACH ROW
BEGIN
    IF EXISTS (
        SELECT 1
        FROM wyprawy
        WHERE misja_id = NEW.misja_id
        AND (uczestnik = 'tesciowa' OR sektor = 'domek dziadka')
    ) THEN
    INSERT INTO system_alarmowy (wiadomosc)
    VALUES ('Tesciowe nadchodzi !!!');
    END IF;
END $$

DELIMITER ;
```
# Zadanie 5
```sql
1. Napisz procedurę mającą jako parametry wyjściowe średnią, sumę i maks udźwigu z tabeli kreatura.
2. Napisz procedurę, która jako parametr wejściowy przyjmuje id_wyprawy a na wyjściu numer sektora (np. A1, B3 itd.)
oraz nazwę sektora posortowaną według kolejności w jakiej występują w danej wyprawie.

--- 1
DELIMITER $$

CREATE PROCEDURE oblicz_udzwig(
    OUT srednia FLOAT,
    OUT suma FLOAT,
    OUT max_udzwig FLOAT
)
BEGIN
    SELECT
        AVG(udzwig),
        SUM(udzwig),
        MAX(udzwig)
    INTO srednia, suma, max_udzwig
    FROM kreatura;
END $$

DELIMITER ;

--- 2
DELIMITER $$

CREATE PROCEDURE wyprawa_sektory(
    IN misja_id INT
)
BEGIN
    DECLARE done INT DEFAULT 0;
    DECLARE sektor_numer VARCHAR(255);
    DECLARE sektor_nazwa VARCHAR(255);
    DECLARE cur CURSOR FOR
        SELECT sektor_numer, sektor_nazwa
        FROM wyprawy
        WHERE misja_id = misja_id
        ORDER BY kolejnosc_sektora;

    OPEN cur;

    DECLARE result VARCHAR(1000) DEFAULT '';

    read_loop: LOOP
        FETCH cur INTO sektor_numer, sektor_nazwa;
        IF done THEN
            LEAVE read_loop;
        END IF;

        SET result = CONCAT(result, sektor_numer, ' - ', sektor_nazwa, ', ');
    END LOOP;

    CLOSE cur;

    SELECT TRIM(TRAILING ', ' FROM result) AS sektor_wyprawy;
END $$

DELIMITER ;
```
