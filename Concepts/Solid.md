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

## 2. Open/closed principle (Zasada otwarte-zamknięte)

Kod powinien być otwarty na rozszerzenia, ale zamknięty na modyfikację.

W tej zasadzie chodzi o zastąpienie rozbudowanych warunków kondycyjnych jak `if` oraz `switch` obiektami o tym samym interfejsie.
Wyobraźmy sobie metodę, która zajmuje się wyświetlaniem opisu pokemonów, w projekcie znajdują się dane o 10 pokemonach a
my chcemy dodać jedenastego. Metoda budując opis pokemona zawiera jego statystyki, wygląd, ataki, opis zachowania, 
miejsce występowania. Podczas dodawania każdego pokemona dodawany jest nowy warunek if porównywany do nazwy pokemona. 
Wraz z każdym nowym pokemonem musimy edytować tę metodę, tak jak inne metody zajmujące się wyliczaniem ataków pokemona, spotykaniem go w dziczy, łapaniem pokemona itd. Wszędzie tam trzeba dopisać nowy warunek kondycyjny.

Alternatywą jest oprzeć takie metody na obiektach o wspólnym typie, np.: `PokemonInterface`, interfejs będzie gwarantował, że podana w argumencie klasa zawiera wszystkie niezbędne dane. Przy dodawaniu nowego pokemona wystarczy będzie implementować interfejs a klasę będzie można przekazać do wszystkich metod opierający się na `PokemonInterface` bez żadnych zmian w ich ciele.

```php
public function presentPokemon(PokemonInterface $pokemon)
{
    $description = '';
    $description .= 'Nazwa pokemona: ' . $pokemon->getName();
    $description .= 'Typ pokemona: ' . $pokemon->getType();
    $description .= 'Zachowanie pokemona: ' . $pokemon->getBehaviour();
}
```

Nie znaczy to jednak, że istniejącego kodu nigdy nie wolno modyfikować. Celem tej zasady nie jest całkowite wyeliminowanie zmian, lecz ograniczenie konieczności modyfikowania stabilnego kodu za każdym razem, gdy chcemy rozszerzyć jego funkcjonalność. Jeżeli zmieniają się wymagania biznesowe albo dotychczasowa implementacja zawiera błędy, modyfikacja kodu może być jak najbardziej uzasadniona.

Open/Closed Principle nie oznacza, że do każdej klasy należy od razu dodawać interfejsy i osobne implementacje. Warto je stosować tam, gdzie istnieje realna potrzeba rozszerzania funkcjonalności. W przeciwnym razie można niepotrzebnie skomplikować prosty kod.
