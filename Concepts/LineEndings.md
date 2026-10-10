# Znaki końca linii

LF, CR i CRLF to oznaczenia znaków końca linii (ang: line endings), określają w jaki sposób komputer rozpoznaje, 
że jedna linia tekstu się skończyła i zaczyna się następna.

### LF - line feed, znak `\n` - ASCI: 10

Przejście do następnej linii. Standardowo używany w Linuksie i macOS.

### CR - Carriage Return, znak `\r` - ASCII: 13

Historycznie oznacza powrót na początek bieżącej linii, bez przechodzenia do następnej. 
Pochodzi z czasów maszyn do pisania.

### CRLF - Carriage Return + Line Feed, znaki `\r\n` - ASCII: 13, 10

Połączenie obu znaków: powrót na początek linii i przejście do następnej. Standardowo używane w Windows.

### Przykłady tekstu przed sformatowaniem znaków końca linii

1. LF - Pierwsza linia\nDruga linia\nTrzecia linia
2. CRLF - Pierwsza linia\r\nDruga linia\r\nTrzecia linia

## Problem z uruchamianiem skryptów .sh przy użyciu CRLF

Skrypty `.sh` są często uruchamiane w środowisku Linux, np. w kontenerach Docker. Jeśli plik został zapisany w systemie 
Windows z zakończeniami linii CRLF, może to powodować problemy z jego uruchomieniem.

Przykładowy plik start.sh:

```shell
#!/bin/bash
echo "Uruchamiam aplikację
```

Pierwsza linia, nazywana shebang, określa interpreter, który ma wykonać skrypt. W tym przypadku jest to Bash 
znajdujący się pod ścieżką /bin/bash.

### Dlaczego CRLF może powodować błąd?

Jeśli plik zostanie zapisany z zakończeniami CRLF, pierwsza linia zawiera znaki \r\n:

```shell
#!/bin/bash\r\n
```

Podczas bezpośredniego uruchamiania skryptu Linux może potraktować znak \r jako część ścieżki interpretera. 
W rezultacie próbuje znaleźć interpreter pod nieprawidłową ścieżką, zamiast pod /bin/bash.

Może to skutkować błędem podobnym do:

```shell
/bin/bash^M: bad interpreter: No such file or directory
```

Zapis ^M oznacza w tym komunikacie znak CR (\r).
