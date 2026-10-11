## 1. Single responsibility principle (Zasada pojedyńczej odpowiedzialności)

Ta zasada mówi, że każda klasa w projekcie powinna skupiać się na jednym zagadnieniu.

Jest to antyteza wielkich plików, składających się z tysięcy linii kodu o ogólnych nazwach. Np.: klasa `ProductManager`,
która będzie zajmowała się tworzeniem zasobu produktu, powiązanych zasobów jak `ProductCategory`, `ProductParameter`,
zawierać pomocnicze funkcje do budowy widoków związanych z zarządzaniem produktów oraz każdej wyobrażalnej metody 
jakkolwiek powiązanej z zarządzaniem produktami.

Poprawną implementacją jest precyzyjnie nazwana klasa o jasno zakreślonych granicach tego czym się zajmuje. Np.:
1. `ValidateProductDataService.php`
2. `ValidateProductCategoryDataService.php`
3. `ValidateProductParameterDataService.php`

Każda z wyżej wymienionych klas mogłaby przyjmować dane na podstawie których chcemy utworzyć jeden z zasobów, następnie
zwracać listę potencjalnych naruszeń poprawności danych i na tym kończy się odpowiedzialność tych klas. Logika odpowiedzialna
za utworzenie zasobu w bazie danych, wysłaniu powiadomień mailowych, propagacji wydarzeń w systemie bądź przygotowaniu
komunikatów błędów będzie znajdować się w osobnych klasach.

Nie znaczy to jednak, że klasa musi składać się z tylko jednej metody i zadania, mogą znajdować się w niej powiązane funkcjonalności
jeśli są skupione w okół jednego aktora (zasobu, wydarzenia). Np.: w porządku będzie umieścić wszystkie operacje crud
dla danej encji w tej samej klasie, jeśli logika nie jest na tyle zawiła, że wymaga klas pomocniczych.

Praktycznym skutkiem ubocznym wielki klas przy pracy w zespole są konflikty Git, nawet gdy dwie osoby pracują nad pozornie
innymi funkcjonalnościami, mogą edytować sekcje importów oraz konstruktor w tym samych klasach.
