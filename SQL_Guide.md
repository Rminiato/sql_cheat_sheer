# Guida SQL - Funzioni e Comandi Utili

Questa guida contiene una raccolta delle funzioni e dei comandi SQL più utilizzati, con sintassi, spiegazione ed esempi pratici.

---

## Indice
- [Selezione e Filtri di Base](#selezione-e-filtri-di-base)
- [Aggregazione Base](#aggregazione-base)
- [Funzioni Statistiche](#funzioni-statistiche)
- [Stringhe e Aggregazioni Speciali](#stringhe-e-aggregazioni-speciali)
- [Funzioni Matematiche](#funzioni-matematiche)
- [Funzioni Analitiche (Window Functions)](#funzioni-analitiche-window-functions)
- [Date e Timestamp](#date-e-timestamp)
- [Tipi di Dato e Concetti](#tipi-di-dato-e-concetti)
- [Operatori di Confronto](#operatori-di-confronto)
- [Query Avanzate](#query-avanzate)
- [DML: INSERT, UPDATE, DELETE](#dml-insert-update-delete)

---

## Selezione e Filtri di Base

### SELECT
Seleziona dati da una tabella.
```sql
SELECT nome, cognome FROM utenti;

#### DISTINCT
Restituisce solo valori unici per una o più colonne.
SELECT DISTINCT paese FROM utenti;
SELECT DISTINCT nome, cognome FROM utenti;
```

### WHERE
Filtra i dati in base a condizioni.
```sql
#### WHERE operatori comparazione (=, <, > , !=)
SELECT * FROM utenti WHERE eta > 18;

#### (NOT) IN
SELECT * FROM utenti WHERE first_name IN ('Rick','Matteo');

#### (NOT) LIKE
SELECT * FROM utenti WHERE first_name like 'Ric%' --- % caratteri multipli
SELECT * FROM utenti WHERE first_name like 'Ric_' --- _ carattere singolo;

#### (NOT) BETWEEN ... AND
SELECT * FROM utenti WHERE nascita BETWEEN '10-12-1996'  and '13-03-1999'--- date incluse

#### (NOT) IS NULL
SELECT * FROM utenti WHERE first_name IS NULL
```

### ORDER BY
Ordina i risultati.
```sql
SELECT * FROM utenti ORDER BY eta DESC;
```

### JOIN
Unisce dati da più tabelle.
```sql
SELECT utenti.nome, ordini.data 
FROM utenti 
JOIN ordini ON utenti.id = ordini.utente_id;
```

### GROUP BY
Raggruppa i risultati.
```sql
SELECT paese, COUNT(*) 
FROM utenti 
GROUP BY paese;
```

---

## Aggregazione Base di solito in coppia con GROUP BY

### COUNT()
Conta righe o valori non null.
```sql
SELECT COUNT(*) FROM utenti;
```

### SUM()
Somma valori numerici.
```sql
SELECT SUM(importo) FROM ordini;
```

### AVG()
Media aritmetica.
```sql
SELECT AVG(eta) FROM utenti;
```

### MIN() / MAX()
Valore minimo/massimo.
```sql
SELECT MIN(eta), MAX(eta) FROM utenti;
```

---

## Funzioni Statistiche

### VAR_POP() / VAR_SAMP()
Varianza (popolazione/campione).
```sql
SELECT VAR_POP(valore), VAR_SAMP(valore) FROM dati;
```

### STDEV() / STDEVP()
Deviazione standard (campione/popolazione).
```sql
SELECT STDEV(valore), STDEVP(valore) FROM dati;
```

### STDDEV_SAMP() / STDDEV_POP()
Deviazione standard (campione/popolazione).
```sql
SELECT STDDEV_SAMP(valore), STDDEV_POP(valore) FROM dati;
```

### PERCENTILE_CONT(p)
Percentile continuo.
```sql
SELECT PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY valore) FROM dati;
```

### PERCENTILE_DISC(p)
Percentile discreto.
```sql
SELECT PERCENTILE_DISC(0.5) WITHIN GROUP (ORDER BY valore) FROM dati;
```

### CORR(x, y)
Correlazione di Pearson tra due variabili.
```sql
SELECT CORR(x, y) FROM dati;
```

### COVAR_POP(x, y) / COVAR_SAMP(x, y)
Covarianza popolazione/campione.
```sql
SELECT COVAR_POP(x, y), COVAR_SAMP(x, y) FROM dati;
```

---

## Stringhe e Aggregazioni Speciali

### TRIM()
Rimuove spazi all’inizio e alla fine della stringa.
```sql
SELECT TRIM(nome) FROM utenti;
```

### SUBSTRING(col, start, length)
Estrae sottostringa.
```sql
SELECT SUBSTRING(nome, 1, 3) FROM utenti;
```

### LEFT(col, n)
Estrae primi n caratteri.
```sql
SELECT LEFT(nome, 3) FROM utenti;
```

### RIGHT(col, n)
Estrae ultimi n caratteri.
```sql
SELECT RIGHT(nome, 3) FROM utenti;
```

### REGEXP_REPLACE(col, pattern, replacement)
Sostituisce pattern regex nella stringa.
```sql
SELECT REGEXP_REPLACE(nome, 'a', 'b') FROM utenti;
```

### GROUP_CONCAT() / STRING_AGG()
Concatena valori in una stringa.
```sql
SELECT GROUP_CONCAT(nome) FROM utenti;
-- oppure (SQL Server/PostgreSQL)
SELECT STRING_AGG(nome, ', ') FROM utenti;
```

---

## Funzioni Matematiche

### ROUND(x, n)
Arrotonda a n decimali.
```sql
SELECT ROUND(prezzo, 2) FROM prodotti;
```

### CAST(col AS tipo) / CONVERT(tipo, col)
Converte valori da un tipo a un altro.
```sql
SELECT CAST(eta AS VARCHAR) FROM utenti;
SELECT CONVERT(VARCHAR, eta) FROM utenti; -- SQL Server
```

---

## Funzioni Analitiche (Window Functions)

### RANK()
Posizione con salti nei pareggi.
```sql
SELECT nome, RANK() OVER (ORDER BY eta DESC) FROM utenti;
```

### DENSE_RANK()
Posizione senza salti nei pareggi.
```sql
SELECT nome, DENSE_RANK() OVER (ORDER BY eta DESC) FROM utenti;
```

### ROW_NUMBER()
Numero progressivo delle righe.
```sql
SELECT nome, ROW_NUMBER() OVER (ORDER BY eta DESC) FROM utenti;
```

### NTILE(n)
Divide i dati in n gruppi.
```sql
SELECT nome, NTILE(4) OVER (ORDER BY eta DESC) FROM utenti;
```

### LAG(col)
Valore della riga precedente.
```sql
SELECT nome, LAG(eta) OVER (ORDER BY eta) FROM utenti;
```

### LEAD(col)
Valore della riga successiva.
```sql
SELECT nome, LEAD(eta) OVER (ORDER BY eta) FROM utenti;
```

---

## Date e Timestamp

### Troncamento Data/Ora

| Funzione                                   | Database        | Esempio                                    | Descrizione                         |
|--------------------------------------------|----------------|---------------------------------------------|-------------------------------------|
| DATE_TRUNC('hour', col)                    | PostgreSQL      | `SELECT DATE_TRUNC('hour', data) FROM eventi;` | Tronca la data all'ora              |
| DATE_FORMAT(col, '%Y-%m-%d %H:00:00')      | MySQL           | `SELECT DATE_FORMAT(data, '%Y-%m-%d %H:00:00') FROM eventi;` | Tronca e formatta la data           |
| DATEADD(hour, DATEDIFF(hour, 0, col), 0)   | SQL Server      | `SELECT DATEADD(hour, DATEDIFF(hour, 0, data), 0) FROM eventi;` | Tronca la data all'ora              |
| TRUNC(col, 'HH')                           | Oracle          | `SELECT TRUNC(data, 'HH') FROM eventi;`     | Tronca la data all'ora              |

---

### Manipolazione Date

- **DATEADD**: aggiunge un intervallo a una data  
  _SQL Server / PostgreSQL_
  ```sql
  SELECT DATEADD(day, 7, nascita) FROM utenti;
  ```

- **DATEDIFF**: calcola la differenza tra due date  
  _SQL Server / MySQL / PostgreSQL_
  ```sql
  SELECT DATEDIFF(day, nascita, CURRENT_DATE) FROM utenti;
  ```

---

### Data e Ora Corrente / Estrazione

- **NOW()**: restituisce la data e ora corrente  
  _MySQL / PostgreSQL_
  ```sql
  SELECT NOW();
  ```

- **CURRENT_DATE**: restituisce solo la data corrente  
  _MySQL / PostgreSQL / SQL Server_
  ```sql
  SELECT CURRENT_DATE;
  ```

- **EXTRACT**: estrae una parte della data  
  _PostgreSQL_
  ```sql
  SELECT EXTRACT(YEAR FROM nascita) FROM utenti;
  ```

---

> **Nota:**  
> Le funzioni di data possono variare tra i diversi database SQL. Consulta la documentazione specifica per adattare la sintassi.

---

## Tipi di Dato e Concetti

### Tipi di dato comuni

| Tipo        | Descrizione                                           | Esempio dichiarazione          |
|-------------|------------------------------------------------------|-------------------------------|
| INT         | Intero (numero senza decimali)                       | id INT                        |
| FLOAT       | Numero con decimali                                  | prezzo FLOAT                  |
| DECIMAL     | Numero decimale con precisione definita              | saldo DECIMAL(10,2)           |
| BOOLEAN     | Vero/Falso (true/false, 1/0)                         | attivo BOOLEAN                |
| VARCHAR     | Stringa a lunghezza variabile                        | nome VARCHAR(50)              |
| TEXT        | Testo lungo                                          | descrizione TEXT              |
| DATE        | Data (YYYY-MM-DD)                                    | nascita DATE                  |
| TIMESTAMP   | Data e ora (YYYY-MM-DD HH:MM:SS)                     | creato TIMESTAMP              |

#### Esempio:
```sql
CREATE TABLE utenti (
  id INT,
  saldo FLOAT,
  attivo BOOLEAN,
  nome VARCHAR(50),
  descrizione TEXT,
  nascita DATE,
  creato TIMESTAMP
);
```

---

## Operatori di Confronto

### BETWEEN ... AND ...
Seleziona valori in un intervallo inclusivo.
```sql
SELECT * FROM ordini WHERE importo BETWEEN 10 AND 100;
SELECT * FROM utenti WHERE nascita BETWEEN '2000-01-01' AND '2010-12-31';
```

---

## Query Avanzate

### WITH ... AS (...) + JOIN
CTE (subquery temporanea) usata come tabella e joinata con altre tabelle.
```sql
WITH utenti_maggiorenni AS (
  SELECT * FROM utenti WHERE eta >= 18
)
SELECT u.nome, o.importo
FROM utenti_maggiorenni u
JOIN ordini o ON u.id = o.utente_id;
```

### Subquery inline (SELECT ... FROM (SELECT ...))
Subquery incorporata direttamente nella query principale senza CTE.
```sql
SELECT nome
FROM (SELECT nome FROM utenti WHERE eta >= 18) AS maggiorenni;
```

### Subquery: correlate vs non correlate

**Subquery non correlata**  
La subquery viene eseguita una sola volta e il risultato viene utilizzato dalla query esterna.

```sql
SELECT nome
FROM utenti
WHERE eta > (SELECT AVG(eta) FROM utenti);
```

**Subquery correlata**  
La subquery viene eseguita per ogni riga della query esterna, usando dati della riga corrente.

```sql
SELECT nome
FROM utenti u
WHERE EXISTS (
  SELECT 1
  FROM ordini o
  WHERE o.utente_id = u.id AND o.importo > 100
);
```

**Sintesi:**  
- **Non correlata**: subquery indipendente, più semplice e generalmente più veloce.
- **Correlata**: subquery dipende dalla riga corrente della query esterna, utile per confronti riga-per-riga.

### UNION
Combina i risultati di due query, rimuove duplicati.
```sql
SELECT nome FROM utenti
UNION
SELECT nome FROM clienti;
```
---

## DML: INSERT, UPDATE, DELETE

### INSERT
Inserisce nuovi dati.
```sql
INSERT INTO utenti (nome, cognome, eta) VALUES ('Mario', 'Rossi', 30);
```

### UPDATE
Aggiorna dati esistenti.
```sql
UPDATE utenti SET eta = 31 WHERE nome = 'Mario';
```

### DELETE
Elimina dati.
```sql
DELETE FROM utenti WHERE nome = 'Mario';
```

---

## Note Aggiuntive

- Puoi ampliare questa guida aggiungendo altri esempi o funzioni SQL che usi frequentemente.
- Per ogni funzione, puoi inserire esempi reali presi dai tuoi progetti.

---

> **Modifica questo file direttamente dal web per personalizzare la tua guida!**
