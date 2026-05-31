### Certyfikat TLS

Certyfikat TLS to cyfrowy dowód tożsamości używany w internecie, który potwierdza, że dana strona lub serwer
rzeczywiście jest tym, za kogo się podaje oraz umożliwia szyfrowanie danych.

Rozwinięcie skrótu TLS to "Transport Layer Security". Jest to protokół kryptograficzny, który szyfruje dane
przesyłane przez sieć pomiędzy klientem a serwerem.

### Rozpoznanie Certyfikatu TLS przez przeglądarkę

Wchodząc na daną stronę internetową, gdy wpiszemy protokół "https", przeglądarka musi użyć TLS.

Nawiązuje więc połączenie z serwerem znajdującym się pod wpisaną domenę na porcie 443. Wysyłając do serwera:
1. Wersje TLS, które obsługuje
2. Listę szyfrów
3. Losową wartość (do późniejszego klucza)

Serwer odpowiada wysyłając:
1. Certyfikat TLS
2. Podpis CA

Teraz przeglądarka musi zweryfikować certyfikat, czy został on wydany przez urząd certyfikacji (CA), który przeglądarka
uznaje. Następnie sprawdza, czy domena, dla której został wystawiony certyfikat zgadza się z domeną strony internetowej.
Oraz czy certyfikat jest ważny, czy ma aktualną datę. Jeśli wszystko się zgadza, przeglądarka uznaje, że serwer
jest właścicielem danej domeny.

Następnie przeglądarka i serwer wspólnie tworzą klucz sesji, który będzie używany do szyfrowania ruchu w sieci. Ten klucz
nie jest przesyłany pomiędzy nimi, jest niezależnie wyliczany na podstawie wymiany kryptograficznej.

Warto zaznaczyć, że sam certyfikat TLS nie potwierdza autentyczność danej firmy/instytucji. Strony podszywające się pod
daną firmę też mogą wykupić sobie certyfikat TLS.

### Różnice między HTTP a HTTPS

Http i Https różnią się przede wszystkim poziomem bezpieczeństwa.

Http przesyła dane jawnie, w postaci niezaszyfrowanej, nie ma mechanizmu uwierzytelniania serwera ani ochrony przed
modyfikacją danych w trakcie transmisji. Wszystkie dane jak hasła, formularza, cookies mogą zostać przechwycone lub
zmienione przez kogoś z dostępem do sieci.

Https wykorzystuje protokół TLS, który zapewnia bezpieczeństwo transmisji. Przesyłane dane są szyfrowane, nie można
ich zmienić w trakcie transmisji, ponieważ podsłuchujący nie zna szyfru do danych. Serwer jest uwierzytelniany jako
właściciel domeny.

Większość stron internetowych automatycznie przekierowuje z protokołu HTTP na HTTPS, jeśli ktoś spróbuje dostać się
przez HTTP.

### Wewnętrzne certyfikaty (Private CA) 

Często w firmach tworzone są własne "urzędy weryfikacji" (CA), które
1. Nie są publicznie zaufane przez przeglądarki
2. Działają tylko w obrębie systemów danej firmy
3. Wydają certyfikaty dla wewnętrznych usług

Jest to robione, żeby wewnętrzne systemy mogły komunikować się ze sobą i tylko wzajemnie ze sobą.

Tutaj wchodzi pojęcie mTLS (mutual TLS). Według którego nie tylko serwer, ale również klient musi się uwierzytelniać.
Wtedy obydwie komunikujące się aplikacje muszą mieć zainstalowane certyfikaty firmy.
