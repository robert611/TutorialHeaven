# Dekorator

### Cel

Dekorator to strukturalny wzorzec projektowy pozwalający dodawać nowe obowiązki obiektom poprzez umieszczanie tych obiektów w specjalnych obiektach opakowujących, które zawierają odpowiednie zachowania.

To jest opis ze strony refactoring.guru.
Reszta opisu pochodzi z książki o programowaniu obiektowym Matta Zandstry.

### Problem

Załóżmy, że tworzymy grę planszową, gra składa się z wielu pól, rodzajów pól może być wiele i mogą mieć swoje podtypy.
Użyjemy przykładu pola "równina". Podtypy tego pola jak też wszystkich innych pól w grze to: "Złocone pole",
"Diamentowe pole", "Zanieczyszczone pole", "Splądrowane pole".

Pozytywne podtypy to:

- Złocone pole
- Diamentowe pole

Negatywne podtypy to:

- Zanieczyszczone pole
- Splądrowane pole

Każde pole ma swoją wartość w walucie gry, pole "równina" ma domyślną wartość 2.

Podtypy powodują zmianę wartości pola albo ją zwiększają w przypadku pozytywnych podtypów,
albo ją zmniejszają w przypadku negatywnych podtypów.

- Złocone pole = +2
- Diamentowe pole = +2
- Zanieczyszczone pole = -2
- Splądrowane pole = -4

Teraz warto zauważyć, że możliwa jest każda kombinacja podtypów pól. Przykładowe istniejące pola to:

- Złocona równina
- Diamentowa złocona równina
- Zanieczyszczona diamentowa złocona równina
- Splądrowana zanieczyszczone diamentowa złocona równina
- Zanieczyszczona równina

W naszym programie chcielibyśmy, żeby każde pole miało swój własny obiekt:

```php
// Tile oznacza kafelek, płytkę, w naszej grze reprezentuje "pole"
// Plains oznacza równinę

abstract class Tile
{
    abstract public function getWealthFactor(): int;
}

class Plains extends Tile
{
    private $wealthFactor = 2;
    
    public function getWealthFactor() : int
    {
        return $this->wealthFactor;
    }
}
```

Widzimy przykład obiektu "równina", jeśli jednak teraz chcielibyśmy stworzyć kolejne obiekty dla kombinacji podtypów
pola "równina" szybko okaże się, że będzie ich aż 16, ponieważ mamy cztery podtypy i łączna ilość kombinacji to 4 x 4.
A to tylko dla pola "równina", co jeśli dodamy kolejne typy pola jak "góry", "morze", "jezioro" ? Wtedy ilość obiektów w
programie stałaby się zbyt duża, żeby móc się w nim poruszać. W tym przypadku dziedziczenie staje się zbyt nieelastyczne,
żeby zastosować je w praktyce.

### Rozwiązanie

Rozwiązaniem takiego problemu jest wzorzec "Dekorator". Ma on za zadanie wprowadzić elastyczność w budowaniu finałowego
obiektu o danym zachowaniu.

We wzorcu Dekorator nie tworzylibyśmy osobnego obiektu dla każdej możliwej kombinacji pól i ich podtypów. Zamiast tego
każde pole i podtyp będą miały po jednym obiekcie a później będziemy dynamicznie łączyć ich zachowania w programie.

W programie będą istniały następujący obiekty:

- Plains (ang: równina)
- Mountains (ang: góry)
- Lake (ang: jezioro)
- See (ang: morze)
- Złocone pole
- Diamentowe pole
- Zanieczyszczone pole
- Splądrowane pole

Tak będzie wyglądał kod (z użyciem jedynie typu "Równina" oraz podtypów "Diamentowe pole", oraz "Zanieczyszczone pole"):

```php
abstract class Tile
{
    abstract public function getWealthFactor(): int;
}

class Plains extends Tile
{
    private int $wealthFactor = 2;

    public function getWealthFactor(): int
    {
        return $this->wealthFactor;
    }
}

abstract class TileDecorator extends Tile
{
    protected Tile $tile;

    public function __construct(Tile $tile)
    {
        $this->tile = $tile;
    }
}

class DiamondDecorator extends TileDecorator
{
    public function getWealthFactor(): int
    {
        return $this->tile->getWealthFactor() + 2;
    }
}

class PollutionDecorator extends TileDecorator
{
    public function getWealthFactor(): int
    {
        return $this->tile->getWealthFactor() - 4;
    }
}
```

Jak widzimy poza abstrakcyjną klasą "Tile" oraz polem "Równina", stworzyliśmy klasę abstrakcyjną "TileDecorator" i jej
implementacje dla dwóch podtypów. Teraz tworzenie wariacji pola "Równina" będzie polegała na odpowiedniej kompozycji
obiektów.

```php
$tile = new PollutionDecorator(new DiamondDecorator(new Plains()));
print $tile->getWealthFactor(); // Wynik to 2 + 2 - 4 = 0
```

Każdy z dekoratorów dodaje swoją logikę do innego dekoratora bądź obiektu pola przekazanego w konstruktorze, dzięki temu,
możemy opakowywać kolejne dekoratory w nieskończoną ilość innych dekoratorów. Jednocześnie zachowując bardzo małą liczbę
obiektów w kodzie.

### Konsekwencje użycia wzorca Decorator

W sytuacji, gdy dany obiekt jest opakowany w wiele dekoratorów utrudnia to czytelność kodu, ponieważ trzeba schodzić w dół
hierarchii dekoratorów, żeby sprawdzić logikę, jaką nakłada każdy z nich.

Dekoratory muszą pilnować implementowania tego samego interfejsu co klasa pierwotna, którą opakowują, żeby zachować
czytelność kodu i nie wprowadzać niespodziewanego działania.

Trzeba pilnować kolejności wykonywania się dekoratorów, czasem przepływ programu od tego zależy. Na przykład
logowanie musi zostać przeprowadzone przed zebraniem statystyk.

Warto zauważyć, że niektórych sytuacjach liczba dekoratorów również może szybko wzrastać, tworząc masę klas.

### Klasy Final

Wzorzec Dekorator może zostać użyty do rozszerzenia klasy typu final. Mówimy tu przede wszystkim o klasach występujących
w zewnętrznych bibliotekach, których definicji nie możemy zmieniać. Jeśli chcemy je rozszerzyć, tworzymy instancję takiej
klasy i przekazujemy ją jako argument do klasy typu dekorator, które nałoży na pierwotny obiekt swoją własną logikę.

