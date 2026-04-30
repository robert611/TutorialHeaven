# Observer

### Cel

Ortogonalność - Ortogonalność promuje możliwość ponownego wykorzystywania komponentów przez łatwość ich włączenia do
nowych systemów bez konieczności specjalnego przystosowania ich w tym celu.

Celem wzorca Observer jest zapewnienie klasom w systemie większej niezależności od innych klas/komponentów,
tak by później taką klasę można było przenieść i zastosować w zupełnie innym projekcie. Co nie byłoby możliwe, jeśli ta
będzie mocno zależna od kontekstu, w którym obecnie się znajduje.

### Problem

Załóżmy, że mamy w projekcie klasę odpowiedzialną za logowanie użytkownika. Taka klasa będzie przetwarzać podany login 
i hasło, a następnie decydować czy użytkownik zostanie zalogowany. Wraz z rozwojem projektu zaczną powstawać coraz to nowsze
wymagania, które będę powodować rozbudowę tej klasy. Np.: logowanie adresów ip użytkowników, żeby wiedzieć jak użytkownicy
dzielą się na kraje. Następnie logowanie nieudanych prób logowania ze względu na bezpieczeństwo, i tak dalej. 
To sprawi, że klasa ta będzie mieć coraz więcej zależności i żeby ją zastosować w innych projektach będzie trzeba ją z tych zależności
okroić.

### Rozwiązanie

Rozwiązaniem takiego problemu jest wzorzec Observer, dzięki któremu wciąż będziemy mogli reagować na wydarzenie zalogowania,
które ma miejsce w tej klasie, ale jednocześnie żadna z zależności nie będzie implementowana bezpośrednio w klasie logowania.

```php
interface Observable
{
    public function attach(Observer $observer);
    public function detach(Observer $observer);
    public function notify();
}
```

```php
class Login implements Observable
{
    private $observers = [];
    
    public const LOGIN_USER_UNKNOWN = 1;
    public const LOGIN_WRONG_PASS = 2;
    public const LOGIN_ACCESS = 3;
    
    public function handleLogin(): bool
    {
        $isValid = false;
        
        // do something here
        
        $this->notify();
        
        return $isValid;
    }
    
    public function attach(Observer $observer): void
    {
        $this->observers[] = $observer;
    }
    
    public function detach(Observer $observer): void
    {
        $this->observers = array_filter(
            $this->observers,
            function ($element) use ($observer) {
                return ($element !== $observer);
            }
        );
    }
    
    public function notify(): void
    {
        foreach ($this->observers as $obs) {
            $obs->update($this);
        }
    }
}
```

```php
interface Observer
{
    public function update(Observable $observable);
}
```

```php
class LoginAnalitycs implements Observer
{
    public function update(Observable $observable)
    {
        // do something
    }
}
```

```php
$login = new Login();
$loginAnalytics = new LoginAnalytics();
$login->attach($loginAnalytics);
```

Jak widzimy w kodzie powyżej implementacja wzorca Observer polega na podziale klas na te obserwowane oraz klasy obserwujące.
Klasy obserwowane mają w sobie trzy metody, attach, detach, notify. Do zarejestrowania ich obserwatorów, usunięcia obserwatorów
oraz poinformowania obserwatorów o jakimś wydarzeniu. Takie podejście sprawia, że w klasie Login implementujemy interfejs
Observable i pozwalamy z kodu klienckiego rejestrować klasy obserwujące. Następnie, po próbie logowania powiadamiamy 
wszystkie obserwatory.

Jest tutaj jeszcze jedna kwestia, przekazywania odpowiednich informacji z obiektu, który jest obserwowany do jego wszystkich
obserwatorów. W przykładzie powyżej jako argument przekazywany jest cały obiekt login. Ponadto w klasie 
LoginAnalytics::update() spodziewamy się obiektu typu "Observable" ale taki typ może mieć więcej klas niż tylko klasa login.

W książce Matta Zandstry rekomendowane jest między innymi podejście z tworzeniem bardziej specyficznych klas obserwatorów.

```php
abstract class LoginObserver implements Observer
{
    private $login;
    
    public function __construct(Login $login)
    {
        $this->login = $login;
        $login->attach($this);
    }
    
    public function update(Observable $observable);
    {
        if ($observable === $this->login) {
            $this->doUpdate($observable);
        }
    }
    
    abstract public function doUpdate(Login $login);
}
```

```php
class SecurityMonitor extends LoginObserver
{
    public function doUpdate(Login $login)
    {
        $status = $login->getStatus();
        
        // do something more
    }
}
```

```php
class GeneralLogger extends LoginObserver
{
    public function doUpdate(Login $login)
    {
        $status = $login->getStatus();
        
        // do something more
    }
}
```

```php
class PartnershipTool extends LoginObserver
{
    public function doUpdate(Login $login)
    {
        $status = $login->getStatus();
        
        // do something more
    }
}
```

```php
$login = new Login();
new SecurityMonitor($login);
new GeneralLogger($login);
new PartnershipTool($login);
```

### Interfejsy pomocnicze dostarczane przez SPL (Standard PHP Library)

SPL oferuje wbudowany mechanizm obserwacji przy pomocy dwóch interfejsów, SplObserver, SplSubject.

```php
interface SplObserver
{
    /* Methods */
    public update(SplSubject $subject): void
}
```

```php
interface SplSubject
{
    /* Methods */
    public attach(SplObserver $observer): void
    public detach(SplObserver $observer): void
    public notify(): void
}
```

Oraz klasę SplObjectStorage, która ma wbudowane wiele użytecznych funkcji do zarządzania tablicą obiektów, więc możemy
jej używać do przechowywania i zarządzania obiektami obserwującymi. Przydatne metody to na przykład
- attach
- detach
- count
- contains


### Czy wzorzec observer jest używany w eventach Symfony?

Tak, EventListeners oraz EventSubscribers pełnią funkcję obserwatorów. Podczas gdy event dispatcher jest pośrednikiem lub
mediatorem.

```text
Take an example from the HttpKernel component. Once a Response object has been created, it may be useful to allow other elements in the system to modify it (e.g. add some cache headers) before it's actually used. To make this possible, the Symfony kernel dispatches an event - kernel.response. Here's how it works:

- A listener (PHP object) tells a central dispatcher object that it wants to listen to the kernel.response event;
- At some point, the Symfony kernel tells the dispatcher object to dispatch the kernel.response event, passing with it an Event object that has access to the Response object;
- The dispatcher notifies (i.e. calls a method on) all listeners of the kernel.response event, allowing each of them to make modifications to the Response object.
```

```php
use Symfony\Component\EventDispatcher\EventDispatcher;

$dispatcher = new EventDispatcher();

$listener = new AcmeListener();
$dispatcher->addListener('acme.foo.action', [$listener, 'onFooAction']);
```
Jak to się dzieje, że symfony wie, które EventListeners i EventSubscribers wywołać po wystąpieniu danego eventu?

EventListener, jeśli klasa ma atrybut lub konfigurację:

```php
#[AsEventListener(event: KernelEvents::REQUEST)]
class MyListener
{
    public function __invoke(RequestEvent $event) {}
}
```
Symfony automatycznie doda jej tag "kernel.event_listener". Ten tag można też zdefiniować samemu w services.yaml.

EventSubscriber otrzyma tag "kernel.event_subscriber" jeśli implementuje interfejs EventSubscriberInterface.

Te tagi to metadane, które zostaną odczytane przez specjalną klasę 
"Symfony\Component\EventDispatcher\DependencyInjection\RegisterListenersPass".

Aplikacja symfony, gdy się uruchamia buduje kontener DependencyInjection, podczas jego kompilowania klasa RegisterListenersPass
wyszukuje wszystkie serwisy z tagami i wykonuje następujący kod:

```php
$dispatcher->addListener('kernel.request', [$service, 'onRequest'], 0);
```

W przypadku EventSubscriber używa się jego metody getSubscribedEvents i wykonuje to polecenie dla wszystkich elementów 
z tablicy.
