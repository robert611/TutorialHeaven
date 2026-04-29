# Singleton

### Cel

Singleton jest kreacyjnym wzorcem projektowym, który pozwala zapewnić istnienie wyłącznie jednej instancji danej klasy.
Ponadto daje globalny punkt dostępowy do tejże instancji.

To jest opis ze strony refactoring.guru
Reszta opisu pochodzi z książki o programowaniu obiektowym Matta Zandstry.

### Problem

Często w naszych projektach potrzebujemy miejsc gdzie można przechowywać dane konfiguracyjne albo inne informacje, które
będą wspólne dla całego kodu w projekcie. Takimi danymi mogą być informacje potrzebne do nawiązania połączenia z bazą danych,
informacje przechowywane w plikach .env itp.

Cechą takich informacji jest to, że muszą być one identyczne dla całego kodu w projekcie, nie może dojść do sytuacji w której
jeden plik otrzymuje inne dane do logowania w bazie danych niż drugi plik, spowodowałoby to błędy i chaos w systemie.

Jednym z rozwiązań takiego problemu mogą obiekty wzorca projektowego "Singleton". Charakteryzują się one tym,
że w systemie w danym momencie może być utworzona tylko jedna instancja takiego obiektu. Ponadto będzie on dostępny 
każdej klasie w projekcie i nie będzie przekazywany jako argument pomiędzy klasami, to spowodowałoby masę zależności 
nawet w klasach, które nie potrzebują obiektu Singleton, ale muszą go przekazać gdzieś indziej.

Alternatywą dla klas Singleton mogą być między innymi zmienne globalne, one również są dostępne w całym systemie. Jednak
mają ten minus, że można je nadpisać, przez co może dojść do sytuacji, w których poszczególne fragmenty kodu operują na 
zmiennych globalnych o różnych wartościach. Dalej zmienne globalne utrudniają czytelność kodu i mogą prowadzić do 
konfliktu nazw w systemie.

### Rozwiązanie

Implementacja klasy typu singleton jest bardzo prosta, musimy ustawić jej konstruktor jako prywatny a następnie stworzyć
w jej środku statyczną metodę do tworzenia instancji obiektu, która będzie sprawdzała czy jakaś instancja już nie istnieje,
jeśli tak to, zamiast tworzyć nową instancję, zwróci tą istniejąca.

```php
class Preferences
{
    private $props = [];
    private static Preferences $instance;
    
    private function __construct() 
    {
    }
    
    public static function getInstance()
    {
        if (empty(self::$instance)) {
            self::$instance = new Preferences():
        }
        return self::$instance;
    }
    
    public function setProperty(string $key, string $val)
    {
        $this->props[$key] = $val;
    }
    
    public function getProperty(string $key)
    {
        return $this->props[$key];
    }
}

$preferences = Preferences::getInstance();
$preferences->setProperty('imię', 'Matt');
unset($pref);
$preferences2 = Preferences::getInstance();
print $preferences2->getProperty("imię"); // Matt, wartość nie zostanie utracona
```

### Konsekwencje

Jako, że obiekt Singleton jest dostępny z każdego miejsca w systemie łatwo przy jego pomocy stworzyć dużo zależności. 
Jednak dziś przy odpowiednim typowaniu każdy IDE pozwoli na ich łatwą identyfikację.
