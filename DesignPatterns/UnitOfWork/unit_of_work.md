# Unit of Work

### Cel

Celem wzorca Unit of Work jest śledzenie obiektów (encji), które należy zmodyfikować, stworzyć lub usunąć
w bazie danych, a następnie w jednej operacji wykonać takie akcje na kilku obiektach razem, zamiast robić to pojedynczo
przy każdej zmianie w którejś z encji. Dobrym odpowiednikiem tu jest używany w doctrine entity manager i metody persist oraz flush.
Mogę dodać wiele nowych encji przy użyciu metody "persist" ale dopiero metoda "flush" je zapisuje. Tak samo
przy zmianach w istniejących encjach.

### Problem

Problemem jest mnogość wykonywania operacji do bazy danych, bez użycia tego wzorca, stosując najprostszą metodę, przy 
każdej zmienionej encji czy rekordzie w bazie danych trzeba będzie wykonywać zapytanie do bazy danych. Co będzie wiązało
się z dużą ilością operacji w kodzie.

### Rozwiązanie

Rozwiązaniem jest wzorzec unit of work, który ukryje wykonywanie zapytań do bazy w kodzie klienckim.

Podstawą jest stworzenie obiektu "ObjectWatcher", w nim notowane będą wszystkie zmiany w encjach, które będzie trzeba 
później zapisać w bazie danych. Obiekt ten będzie miał też metodę "performOperations", która wykona zmiany na wszystkich
zanotowanych obiektach. Więc w kodzie klienckim, możemy utworzyć i zupdatować kilka encji, po czym wykonamy metodę
"performOperations".

```php
<?php

declare(strict_types=1);

class ObjectWatcher
{
    private array $all = [];
    private array $dirty = [];
    private array $new = [];
    private array $delete = [];
    private static $instance = null;
    
    private function __construct()
    {
    }
    
    public static function getInstance(): self
    {
        if (is_null(self::$instance)) {
            self::$instance = new ObjectWatcher();
        }
        
        return self::$instance;
    }
    
    public static function addDelete(DomainObject $object): void
    {
        $instance = self::getInstance();
        $instance->delete[$self->globalKey($object)] = $object;
    }
    
    public static function addDirty(DomainObject $object): void
    {
        $instance = self::getInstance();
        if (!in_array($object, $instance->new, true)) {
            $instance->dirty[$instance->globalKey($object)] = $object;
        }
    }
    
    public static function addNew(DomainObject $object): void
    {
        $instance = self::getInstance();
        $instance->new[] = $object;
    }
    
    public static function addClean(DomainObject $object): void
    {
        $instance = self::getInstance();
        unset($instance->delete[$instance->globalKey($object)]);
        unset($instance->dirty[$instance->globalKey($object)]);
        $instance->new = array_filter(
            $instance->new,
            function ($a) use ($object) {
                return !($a === $obj);
            }
        );
    }
    
    public function performOperations(): void
    {
        foreach ($this->dirty as $key => $object) {
            $object->getFinder()->update($object);
        }
        foreach ($this->new as $key => $object) {
            $object->getFinder()->insert($object);
            print "wstawiam " . $obj->getName() . "\n";
        }
        $this->dirty = [];
        $this->new = [];
    }
}
```

```php
declare(strict_types=1);

abstract class DomainObject
{
    private $id;
    
    public function __construct(int $id)
    {
        $this->id = $id;
        if ($id < 0) {
            $this->markNew();
        }
    }
    
    abstract public function getFinder(): Mapper;
    
    public function getId(): int
    {
        return $this->id;
    }
    
    public function setId(int $id): void
    {
        $this->id = $id;
    }
    
    public function markNew(): void
    {
        ObjectWatcher::addNew($this);
    }
    
    public function markDeleted(): void
    {
        ObjectWatcher::addDelete($this);
    }
    
    public function markDirty(): void
    {
        ObjectWatcher::addDirty($this);
    }
    
    public function markClean(): void
    {
        ObjectWatcher::addClean($this);
    }
}
```

```php
<?php

declare(strict_types=1);

class Space extends DomainObject
{
    public function setVenue(Venue $venue)
    {
        $this->venue = $venue;
        $this->markDirty();
    }
    
    public function setName(string $name)
    {
        $this->name = $name;
        $this->markDirty();
    }
}
```

Pokazałem fragment niezbędnego kodu, czyli obiekt "ObjectWatcher", który trzyma informacje o zmianach we wszystkich encjach.
Obiekt DomainObject, który jest helperem używanym przez encje do automatycznego oznaczania, że ich dane zostały zmienione.
Ponadto w wysoko poziomowym kodzie moglibyśmy wywoływać metody dla nowych i usuwanych encji.

### Konsekwencje

Ten wzorzec jest bardzo użyteczny, ale kluczowe jest tu pamiętanie o odpowiednim oznaczania obiektów, gdy wystąpią w nich zmiany.
Przeoczenie tego w jakimś miejscu, na przykład w którymś setterze w środku klasy encji, może być później ciężkie do wykrycia.

### Transakcja przy wykonywaniu operacji

Dzięki temu, że wszystkie zmiany, które chcemy wykonać, są zgromadzone w jednym miejscu, możemy opakować zapytania do
bazy danych w transakcję i zapewnić im atomowość, albo wszystkie zostaną wykonane, albo żadne.
