### Czym jest IP

IP to skrót od Internet Protocol (po polsku: Protokół Internetowy). W skrócie każde urządzenie łączące się z internetem
otrzymuje adres IP, który identyfikuje miejsce urządzenia w sieci, adres IP może być tymczasowy lub stały. Dzięki temu 
przesyłając dane w internecie, mają one adres nadawcy i odbiorcy.

Adres IP można sprawdzić, wchodząc na stronę: https://whatismyipaddress.com/

### Czym jest adres MAC?

MAC to skrót od MAC address. To unikalny identyfikator sprzętowy karty sieciowej w urządzeniu. Każde urządzenie, które 
może łączyć się z siecią internetową, ma kartę sieciową, a każda taka karta ma przypisany adres MAC.

Przykład adresu MAC:

```text
00:1A:2B:3C:4D:5E
```

#### Do czego służy adres MAC?

Adres MAC działa na niższym poziomie niż IP:
- IP działa w Internecie i sieciach (warstwa "logiczna")
- MAC działa w lokalnej sieci (warstwa "sprzętowa")

Jest używany do:
- Identyfikacji urządzenia w sieci lokalnej (LAN)
- komunikacji między urządzeniami w tej samej sieci
- przekazywania ramek danych w Wi-FI i Ethernet

#### Czy adres MAC się zmienia?

W większości przypadków jest stały, zapisany w karcie sieciowej, ale może być zmieniany programowo, przez tak zwane
spoofing MAC. Telefony często używają losowych MAC w Wi-FI dla prywatności.

#### Czy MAC działa w Internecie?

Nie, adres MAC działa tylko w lokalnej sieci, nie jest przesyłany przez Internet. Gdy pakiet danych wychodzi z sieci
lokalnej, MAC znika w routerze, dalej liczy się już tylko IP.


### Czym jest protokół DHCP?

DHCP to skrót od Dynamic Host Configuration Protocol. To protokół sieciowy, który automatycznie przydziela urządzeniom
w sieci lokalnej adresy IP oraz inne parametry niezbędne do poprawnego działania w sieci. Dzięki temu użytkownik
nie musi robić tego ręcznie.

#### Jakie dane konfiguruje DHCP?

- Adres IP: unikalny identyfikator urządzenia w sieci
- Maska podsieci: definiuje zakres adresów IP w danej sieci
- Brama domyślna: adres router, który umożliwia dostęp do internetu
- Serwery DNS: adresy serwerów odpowiadających za tłumaczenie nazw domen na adresy IP.

#### Proces działania DHCP w sieci lokalnej

Proces działania DHCP składa się z czterech głównych etapów:

1. Discover (Odkrywanie)
Urządzenie, które chce uzyskać adres IP, wysyła do sieci zapytanie DHCP Discover w celu zlokalizowania serwera DHCP
2. Offer (Oferta)
Serwer DHCP odpowiada na zapytanie, oferując adres IP oraz inne parametry sieciowe.
3. Request (Żądanie)
Urządzenie akceptuje ofertę serwera, wysyłając zapytanie DHCP Request.
4. Acknowledge (Potwierdzenie)
Serwer DHCP potwierdza przydzielenie adresu IP, kończąc proces konfiguracji.

DHCP zwykle przyznaje Adres Ip, Maskę Sieci, Bramę domyślną, DNS, Czas dzierżawy.

| Parametr       | Wartość       |
|----------------|---------------|
| Adres IP       | 192.168.1.50  |
| Maska sieci    | 255.255.255.0 |
| Brama domyślna | 192.168.1.1   |
| DNS            | 8.8.8.8       |
| Czas dzierżawy | 24h           |

#### Jak Router zapisuje, komu przydzielił adres IP?

Tutaj pojawia się adres MAC, gdy urządzenie nie ma jeszcze adres UP, ma swój adres MAC.

Router może więc zapisać:

| MAC               | IP           |
|-------------------|--------------|
| AA:BB:CC:DD:EE:FF | 192.168.1.50 |

#### Gdzie znajduje się serwer DHCP?

Zwykle jest wbudowany w router lub w większych sieciach, np.: firmowych, zainstalowany na dedykowanym serwerze, 
np.: w systemie Windows Server.

Routerem tutaj jest każde urządzenie, które może przekazywać pakiety między co najmniej dwiema sieciami. Np.: routerem
może być telefon, który udostępnia internet w formie Wifi lub przez usb/bluetooth do innych urządzeń, wtedy w telefonie
będzie znajdować się serwer DHCP, który przydziela IP łączącym się z telefonem urządzeniom.

W dużych firmach serwer DHCP często będzie na oddzielnym serwerze niż sam router, ponieważ może obsługiwać tysiące lub
miliony urządzeń.

Wpisując polecenie:

```cmd
ipconfig /all
```

Możemy odszukać nasze połączenie internetowe, tam będzie wpis "DHCP Server" z adresem IP urządzenia, na którym znajduje
się serwer DHCP.

DHCP Server . . . . . . . . . . . : 10.131.177.184
