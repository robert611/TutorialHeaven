### Czym jest having w sql?

Klauzula "HAVING" w sql służy do ścisłej współpracy z klauzulą "GROUP BY", jej zadaniem jest filtrowanie pogrupowanych 
zestawów danych. Choć warto zauważyć, że możliwe jest używanie "HAVING" bez "GROUP BY" w tym samym zapytaniu, jednak tylko
jeśli wynik zapytania będzie zawierał tylko jedną grupę rekordów.

Np:

```sql
SELECT user_id, COUNT(*) AS orders
FROM orders
WHERE user_id = 123
HAVING COUNT(*) >= 3;
```

Tutaj ze względu na użycie where i doprecyzowanie, że mają być zwracane rekordy tylko jednego użytkownika, tabela wynikowa
będzie zawierać jedną grupę rekordów, więc możemy użyć "HAVING" bez "GROUP BY", ale w praktyce nie wydaje się to zbyt użyteczne.

### Klauzula GROUP BY

Klauzula "GROUP BY" jest używana do dzielenia danych na grupy na podstawie wartości danych. To znaczy, jeśli w tabeli 
mamy kolumnę color, która przyjmuje cztery wartości: "zielony", "czerwony", "żółty", "biały", to grupując po tej kolumnie,
otrzymamy cztery grupy danych, ponieważ istnieją cztery różne kolory. 

### Funkcje agregujące podczas używania GROUP BY

Żeby nadać grupowanym wynikom sens, należy również definiować jakąś wartość, którą chcemy wyciągnąć z danej grupy. Ta
wartość może przybierać wiele postaci:

- łączną ilość rekordów w grupie
- sumę wartości danej kolumny w grupie
- średnią wartość danej kolumny w grupie
- maksymalną/minimalną wartość danej kolumny w grupie

To definiowania takiej wartości używamy funkcji agregujących w sql, są to:

- COUNT liczy rekordy
- SUM suma wartości
- AVG średnia
- MIN minimalna wartość
- MAX maksymalna wartość
- GROUP_CONCAT łączy wiele wartości w jeden string
- VAR_POP wariancja
- STDDEV_POP odchylenie standardowe
- BIT_AND(), BIT_OR(), BIT_XOR() agregacje bitowe

Przykłady użycia funkcji agregujących razem z "GROUP BY"

```sql
SELECT user_id, COUNT(*) AS total_orders
FROM orders
GROUP BY user_id;
```
```sql
SELECT user_id, SUM(amount) AS total_spent
FROM orders
GROUP BY user_id;
```

```sql
SELECT status, AVG(amount) AS avg_order_amount
FROM orders
GROUP BY status;
```

```sql
SELECT user_id, MIN(amount) AS smallest_order
FROM orders
GROUP BY user_id;
```

```sql
SELECT user_id, MAX(amount) AS largest_order
FROM orders
GROUP BY user_id;
```

```sql
SELECT user_id, GROUP_CONCAT(order_name) AS amounts
FROM orders
GROUP BY user_id;
```

### Praktyczne używanie HAVING na pogrupowanych rekordach

Na początku rozważmy kolejność poszczególnych klauzul w zapytaniach sql:

```sql
SELECT column_list
FROM table_name
WHERE where_conditions
GROUP BY column_list
HAVING having_conditions
ORDER BY order_expression
```

Jak widać klauzula "HAVING" jest zawsze umiejscowiona po klauzulach "WHERE" oraz "GROUP BY" ale przed "ORDER BY".

Klauzula "HAVING" przedstawia warunek kondycyjny dla grupy rekordów bądź wyniku ich agregacji.

```sql
SELECT department, SUM(salary)
FROM employee
GROUP BY department
HAVING SUM(salary) >= 50000
```

```sql
SELECT salesman_id, SUM(total_value) 
FROM sale
WHERE salesman_id != 3
GROUP BY salesman_id
HAVING SUM(total_value) > 40000;
```

```sql
SELECT salesman_id, SUM(total_value)
FROM sale
WHERE salesman_id != 3
GROUP BY salesman_id
HAVING SUM(total_value) > 15000 AND SUM(total_value) < 36000;
```

### Różnice pomiędzy HAVING I WHERE

Najbardziej fundamentalną różnicą jest to, że klauzula "WHERE" działa na pojedyńczych rekordach. Podczas gdy
klauzula "HAVING" operuje na grupach rekordów w momencie, gdy wykonana została już operacja "GROUP BY".

Ponadto HAVING jest używane tylko przy zapytaniach typu "SELECT", nie może być używane podczas operacji "DELETE" lub "UPDATE".
Za to klauzula "WHERE", może być używana również przy zapytaniach "DELETE" oraz "UPDATE".