## Destructuring

Destrukturyzacja (ang.: Destructuring) to funkcjonalność, która została wprowadzona w ES6 (ECMAScript 2015 (ES2015)).

Przy jej pomocy możemy wydobywać dane z tablic lub obiektów używając bardziej zwięzłej składni. Oraz przy okazji
wydobywania tych danych, możemy od razu zmieniać im nazwę.

```js
const person = {
    head: {
        eyes: 'x',
        mouth: {
            teeth: 'x',
            tongue: 'x'
        }
    },
    body: {
        shoulders: 'x',
        chest: 'x',
        arms: 'x',
        hands: 'x',
        legs: 'x'
    }   
};

// Stary sposób na uzyskanie pola "head" z obiektu "person" to:
let head = person.head;

// Przy użyciu destrukturyzacji możemy to zrobić następująco:
let { head } = person;

// To samo tylko z jednoczesnym nadaniem innej nazwie polu "head"
let { head : myHead } = person;

// Teraz mogę wykonać następujące polecenie
console.log(myHead);
```

Przykład z tablicami:

```js
let numbers = ['2', '3', '7'];

// Stary sposób
let two = numbers[0];
let three = numbers[1];

// ES6 Destrukturyzacja
let [firstNumber, secondNumber, thirdNumber] = numbers;

console.log(firstNumber) // prints '2'
console.log(secondNumber) // prints '3'
console.log(thirdNumber) // prints '7'
```

Możliwe jest też używanie destrukturyzacji w odczycie argumentów funkcji:

```js
// Stary sposób
function getHeadAndBody(person)
{
    let headAndBody = {
        head: person.head,
        body: person.body,
    };

    return headAndBody;
}

// Użycie destrukturyzacji w odczycie argumentów
function getHeadAndBody({ head, body })
{
    // Zauważ, że tutaj tworzymy obiekt składający sie ze zmiennych head i body, to nie ma nic wspólnego z destrukturyzacją
    return { 
        head,
        body,
    };
}
```

Warto jednak zauważyć, że musimy być pewni, że obiekt, który zostanie przekazany do funkcji getHeadAndBody faktycznie będzie
miał w sobie pola "head" i "body". Można dać wartości domyślne:

```js
function getHeadAndBody({ head = '', body = '' }) {
    return { head, body }
}
```

