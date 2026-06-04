### Czym jest IP

IP to skrót od Internet Protocol (po polsku: Protokół Internetowy). W skrócie każde urządzenie łączące się z internetem
otrzymuje adres IP, który identyfikuje miejsce urządzenia w sieci, adres IP może być tymczasowy lub stały. Dzięki temu 
przesyłając dane w internecie, mają one adres nadawcy i odbiorcy.

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

