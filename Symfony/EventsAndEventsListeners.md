### Czym jest event w Symfony?

Event (zdarzenie) służy do informowania innych klas o zmianach, jakie wystąpiły w klasie zgłaszającej event. Mechanizm
ten pozwala na komunikacje pomiędzy różnymi klasami w elastyczny sposób, gdzie żadna z klas nie musi wiedzieć
o istnieniu drugiej. Jedynie informuje o wystąpieniu danego zdarzenia, nie mając w sobie żadnych informacji, kto będzie
na nie reagował.

Symfony implementuje wzorce projektowe Mediator i Observer tworząc komponent EventDispatcher używany do rozgłaszania
wystąpienia zdarzenia.

Każdy event musi mieć swoją unikalną nazwę.

### Zastosowanie w Symfony

Symfony w trakcie przetwarzania requestu na wielu etapach rzuca eventy, które pozwalają nam na modyfikację całego
procesu HTTP zapytanie -> odpowiedź. Z możliwości tej korzystają również wewnętrzne biblioteki symfony. Dobrym przykładem
jest `symfony/security-bundle`, który blokuje przychodzące zapytania na wczesnym etapie, jeśli nie posiadają wymaganego
uwierzytelnienia.

Przykłady wbudowanych w Symfony eventów:

1. kernel.request (Ten event jest wysyłany, zanim ustalony zostanie controller. Można przy jego pomocy dodać informacje
   do requestu albo zwrócić odpowiedź i nie dopuścić do wykonania się controllera)
2. kernel.controller (Ten event jest wysyłany po ustaleniu controllera, ale przed jego wykonaniem)
3. kernel.controller_arguments (Ten event jest wysyłany na chwilę przed wykonaniem controllera, używa się go do modyfikacji
   argumentów, która zostaną do niego przekazane)
4. kernel.view (Ten event jest wysyłany po wykonaniu się controllera, ale tylko w przypadku gdy nie zwraca on symfonowego
   obiektu odpowiedzi)
5. kernel.response (Ten event jest wysyłany po zwróceniu przez controller obiektu odpowiedzi, można ją wtedy zmodyfikować)
6. kernel.finish_request (Ten event jest wysyłany po evencie kernel.response)
7. kernel.terminate (Ten event jest wysyłany po tym, gdy odpowiedź została już wysłana do odbiorcy)
8. kernel.exception (Ten event jest wysyłany w momencie wystąpienia błędu podczas przetwarzania requestu HTTP)

### Własne eventy w Symfony

W Symfony możemy tworzyć własne eventy wewnątrz naszej aplikacji. Choć szczególnie przydatne jest, to gdy tworzymy
bibliotekę, z której będą korzystać inni, daje to im możliwość rozszerzenia działania naszej biblioteki i dostosowania
jej do swoich potrzeb.

```php
namespace Acme\Store\Event;

use Acme\Store\Order;
use Symfony\Contracts\EventDispatcher\Event;

/**
 * This event will be dispatched each time an order
 * is placed in the system.
 */
final class OrderPlacedEvent extends Event
{
    public function __construct(private Order $order) {}

    public function getOrder(): Order
    {
        return $this->order;
    }
}
```

Wysłanie eventu

```php
use Acme\Store\Event\OrderPlacedEvent;
use Acme\Store\Order;

// the order is somehow created or retrieved
$order = new Order();
// ...

// creates the OrderPlacedEvent and dispatches it
$event = new OrderPlacedEvent($order);
$dispatcher->dispatch($event);
```

### Czym jest EventListener?

Najbardziej powszechnym sposobem nasłuchiwania eventu jest stworzenie event listenera.

```php
namespace App\EventListener;

use Symfony\Component\EventDispatcher\Attribute\AsEventListener;

#[AsEventListener]
final class MyListener
{
    public function __invoke(CustomEvent $event): void
    {
        // ...
    }
}
```

### Czym jest EventSubscriber?

Kolejnym sposobem nasłuchiwania eventów poza EventListenerem jest EventSubscriber. EventSubscriber to klasa php, która
implementuje interfejs EventSubscriberInterface oraz zawiera statyczną metodę getSubscribedEvents, metoda ta zwraca tablicę
informującą, jakich eventów nasłuchuje EventSubscriber.

```php
namespace Acme\Store\Event;

use Acme\Store\Event\OrderPlacedEvent;
use Symfony\Component\EventDispatcher\EventSubscriberInterface;
use Symfony\Component\HttpKernel\Event\ResponseEvent;
use Symfony\Component\HttpKernel\KernelEvents;

class StoreSubscriber implements EventSubscriberInterface
{
    public static function getSubscribedEvents(): array
    {
        return [
            KernelEvents::RESPONSE => [
                ['onKernelResponsePre', 10],
                ['onKernelResponsePost', -10],
            ],
            OrderPlacedEvent::class => 'onPlacedOrder',
        ];
    }

    public function onKernelResponsePre(ResponseEvent $event): void
    {
        // ...
    }

    public function onKernelResponsePost(ResponseEvent $event): void
    {
        // ...
    }

    public function onPlacedOrder(OrderPlacedEvent $event): void
    {
        $order = $event->getOrder();
        // ...
    }
}
```

### Jaka jest różnica pomiędzy EventListener a EventSubscriber?

W praktyce różnica pomiędzy oboma jest dość subtelna. Przyjęło się, że EventListener jest "lżejszy" i powinien nasłuchiwać
tylko jednego eventu, choć możliwe jest, żeby nasłuchiwał wiele eventów naraz. Ponadto zależnie od konfiguracji, jeśli
znajduje się ona w pliku services.yaml, a nie jest zrobiona poprzez atrybut w samej klasie, informacja jakiego eventu nasłuchuje
EventListener będzie po za jego plikiem.

Podczas gdy EventSubscriber ma zadeklarowane w swojej klasie, jakich dokładnie eventów nasłuchuje, bardziej powszechne jest,
gdy nasłuchuje na więcej niż jeden event. Nie musi mieć dodatkowej konfiguracji poza implementowaniem odpowiedniego interfejsu.