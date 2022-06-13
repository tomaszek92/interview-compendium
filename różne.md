# GIT
## merge
Powstaje "commit scalenia", który będzie łączył historie obydwu gałęzi, dzięki czemu struktura gałęzi będzie wyglądała następująco:

<img src="./assets/git/merge.svg" height="300">

## rebase
Powoduje przesunięcie całej gałęzi `feature`, aby zaczynała się od końcówki gałęzi `main`. Pozwoli to na skuteczne włączenie wszystkich nowych commitów do gałęzi `main`. Jednak zamiast tworzenia "commita scalenia", operacja powoduje przepisanie historii projektu poprzez utworzenie zupełnie nowych commitów dla każdego commita w gałęzi pierwotnej.

<img src="./assets/git/rebase.svg" height="300">

## fetch
Pobieranie zmian ze zdalnego repozytorium – wszystkie nowe dane z commitów, które zostały wykonane od czasu ostatniej synchronizacji ze zdalnym repozytorium, są pobierane do kopii lokalnej. Jednak – nowo pobrane dane nie są integrowane z plikami lokalnymi, a zmiany nie są mergowane z naszym kodem.

## pull
Pobieranie (`fetch`) i integrowanie (`merge`) zdalnych zmian.

# HTTP
Protokół warstwy aplikacji, odpowiedzialny za transmisję dokumentów hipermedialnych, np. HTML. Został stworzony do komunikacji pomiędzy przeglądarkami, a serwerami webowymi, ale może być używany również w innych celach. HTTP opiera się na klasycznym modelu klient-serwer, gdzie klient inicjuje połączenie poprzez wysłanie żądania, następnie czeka na odpowiedź. HTTP jest protokołem bezstanowym, co oznacza, że serwer nie przechowuje żadnych danych (stanów) pomiędzy oboma żądaniami. Często opiera się na warstwie TCP/IP, może używać także UDP.

## Wersja 2.0
- rok 2015
- wykorzystywany protokół: SPDY – protokół, opracowany przez Google, który bazuje na TCP i jest ukierunkowany na prędkość działania komunizacji przeglądarki z serwerem wykorzystując kompresje i manipulacje pakietami.
- praca w trybie: trwałego połączenia, które jest aktywne do zamknięcia strony
- umożliwia na wykonywanie wielu zapytań do serwera na raz (w przypadku poprzednich wersji protokołu HTTP zapytania były kolejkowanie)
## Wersja 3.0
- rok 2018
- wykorzystywany protokół: QUIC – nowy protokół, również opracowany przez Google, który bazuje na protokole UDP. Motywacja do zmiany protokołu był problem związany z blokadą żądania HTTP, aż do czasu uzyskania odpowiedzi. UDP jest szybszy i ma mniejsze opóźnienie od TCP.

### SPDY (TCP) vs QUIC (UDP)
TCP w przypadku zagubienia jednego z pakietów powoduje, że nie może obsłużyć żądania dopóki nie nastąpi retransmisja zagubionego pakietu. Do tego czasu dane te są blokowane lub nawet usuwane, gdy błąd jest korygowany.

W przypadku QUIC zgubienie jednego z wielu pakietów nie powoduje blokady innych pakietów, gdyż każdy pakiet posiada swój niezależny strumień. Jest to zwłaszcza bardzo przydatne w poprawianiu wydajności łączy podatnych na błędy.

# REST
- Jednolity interfejs – dla realizacji konkretnego działania jest tylko jedna droga dojścia.
- Bezstanowość – bez względu na to jakie wcześniej operacje zostały wykonane, to zawsze każda kolejna operacja nie będzie zmieniała rezultatu dla innej.
- Podział na aplikacje klient-serwer - rozdzielenie aplikacji pozwala na ich niezależny rozwój i działanie. Taki podział zdecydowanie zwiększa możliwości skalowania, przenośności i rozszerzalność, a obsługa błędów jest znacznie łatwiejsza.
- Cache danych - wykorzystanie mechanizmu przeglądarek lokalnie „zapamiętujących” odpowiedzi serwera w celu zmniejszenia obciążenie sieciowe.
- Odseparowane warstwy - komunikacja i wymiana danych między aplikacjami klienckimi, a API nie powinna być obciążona informacjami o zewnętrznych serwisach i usługach, z których serwer korzysta.
## Metody
### GET
- Pobieranie zasobu.
- Idempotentność[^idempotence] :green_circle:
- Request body :red_circle:
- Response body :green_circle:
- Cacheable :green_circle:
- Kody odpowiedzi:
    - 200 (OK) - zasób został odnaleziony
    - 404 (Not Found) - zasób nie został odnaleziony

### POST
- Tworzenie zasobu.
- Idempotentność[^idempotence] :red_circle:
- Request body :green_circle:
- Response body :green_circle:
- Cacheable :red_circle:
- Kody odpowiedzi:
    - 201 (Created) - zasób został stworzony (w headerze Location powinien zawierać link do utworzonego zasobu)
    - 409 (Conflict) - zasób już istnieje

### PUT
- Aktualizowanie zasobu.
- Idempotentność[^idempotence] :green_circle:
- Request body :green_circle:
- Response body :red_circle:
- Cacheable :red_circle:
- Kody odpowiedzi:
    - 200 (OK) lub 204 (No Content) - zasób został zaktualizowany
    - 404 (Not Found) - zasób nie został odnaleziony

### DELETE
- Usuwanie zasobu.
- Idempotentność[^idempotence] :green_circle:
- Request body :yellow_circle:
- Response body :yellow_circle:
- Cacheable :red_circle:
- Kody odpowiedzi:
    - 200 (OK) - zasób został usunięty i odpowiedź zawiera informacje
    - 204 (No Content) - zasób został usunięty i odpowiedź nie zawiera informacji
    - 202 (Accepted) - zostało zlecone usuwanie zasobu
    - 404 (Not Found) - zasób nie został odnaleziony

### PATCH
- Częściowe aktualizowanie zasobu.
- Idempotentność[^idempotence] :red_circle:
- Request body :green_circle:
- Response body :green_circle:
- Cacheable :red_circle:
- Kody odpowiedzi:
    -  200 (OK) - zasób został zaktualizowany i odpowiedź zawiera informacje
    - 204 (No Content) - zasób został zaktualizowany i odpowiedź nie zawiera informacji
    - 404 (Not Found) - zasób nie został odnaleziony

[^idempotence]: Dowolna liczba identycznych żądań pozostawi zasób w tym samym stanie.

# CORS (Cross-Origin Resource Sharing)
Mechanizm oparty na nagłówkach HTTP, który umożliwia serwerowi wskazanie dowolnego źródła (schematu, domeny lub portu) innego niż jego własne, z którego przeglądarka powinna umożliwiać ładowanie zasobów. CORS opiera się również na mechanizmie, dzięki któremu przeglądarki wysyłają żądanie "preflight" do serwera hostującego zasób, aby sprawdzić, czy serwer zezwoli na rzeczywiste żądanie.

## Scenariusze wywołania zapytania
### Simple
Zapytania, które wykorzystują metody takie jak GET, HEAD, POST, podlegają prostym zapytaniom, ponieważ nie powodują i nie wywołują skutków ubocznych na danych obecnych po stronie serwera.
### Preflight
Zapytania, które wykorzystują metody takich jak DELETE lub PUT podlegają zapytaniom preflight, ponieważ za pomocą tych metod każdy może modyfikować lub usuwać dane obecne po stronie serwera, a tym samym skutki uboczne. Przeglądarka przed wysłaniem rzeczywistych zapytań wysyła zapytanie preflight z `access-control-allow-methods` i `access-control-allow-headers`, które służą do sprawdzenia, czy serwer zezwala na te metody. Jeśli serwer wyśle ​​status 200 stwierdzający, że zapytanie preflight zostało zweryfikowane, przeglądarka wysyła rzeczywiste żądanie.

# Local storage
Dane istnieją dopóki nie zostaną usunięte jawnie (`localStorage.removeItem('key')`).

# Session storage
Dane istnieją w ramach sesji (karta w przeglądarce). Po zakończeniu sesji są usuwane.

# Kolejki
## Outbox pattern
Ten wzorzec zapewnia, że ​​wiadomość została pomyślnie wysłana (np. do kolejki) przynajmniej raz. Dzięki temu wzorcowi zamiast bezpośrednio publikować wiadomość do kolejki, przechowujemy ją w magazynie tymczasowym (np. w tabeli bazy danych). Opakowujemy zapisywanie encji i przechowywanie wiadomości jednostką pracy (transakcja). Mając zapisane takie zdarzenia możemy zrobić proces działający w tle, który będzie sprawdzał czy w tabeli są jakieś zdarzenia, które nie zostały jeszcze wysłane. Jeśli takie znajdzie to próbuje je wysłać i po tym jak dostanie potwierdzenie o sukcesie wysłania (np. ACK z kolejki) to oznacza zdarzenie jako wysłane. Dlaczego więc zapewnia at-least-once, a nie exactly-once? Bo zapis do bazy może się nie powieźć (np. nie będzie odpowiadała), wtedy proces obsługujący outbox pattern spróbuje za jakiś czas ponownie wysłać zdarzenie i będzie próbował to robić dopóki nie zostanie zapisana poprawnie zdarzenie oznaczone jako wysłane.

## Inbox pattern
Ten wzorzec zapewnia, że ​​wiadomość została pomyślnie wysłana (np. do kolejki) przynajmniej raz. Dzięki temu wzorcowi po otrzymaniu wiadomości z kolejki jest ona od razu zapisywana w bazie i jest wysyłwany ACK. Mając zapisane takie zdarzenia możemy zrobić proces działający w tle, który będzie sprawdzał czy w tabeli są jakieś zdarzenia, które nie zostały jeszcze obsłużone. Zaletą dodatkowej tabeli jest to, że możemy szybko przyjąć zdarzenia z szyny, a potem w dogodnym tempie i czasie przeprocesować je wewnętrznie nie ryzykując, że szyna padnie

## RabbitMQ
- **Producent**: aplikacja, która wysyła wiadomość.
- **Konsumer**: aplikacja, któr przyjmuje wiadomość.
- **Kolejka**: bufor, który przechowuje nieobsłużone wiadomości.
- **Wiadomość**: informacja, która jest przesyłana pomiędzy producentem, a konsumerem.
- **Połączenie**: połącznie TCP pomiędzy aplikacją, a Rabbitem.
- **Kanał**: wirtualne połączenie do kolejki, dzięki któremy zyskujemy odsperawane połaczenia na jednym połączeniu TCP.
- **Exchange**: otrzymyje wiadomości od producenta i przekazuje jest do kolejeki w zależności od zdefiniowanych reguł. Aby kolejka otrzymała wiadomość musi być połaczona z exchange.
- **Binding**: połączenie między kolejką, a exchange.
- **Routing key**: klucz, który definiuje czy wiadomość ma trafić do kolejki. Można to porównać do adresu dla wiadomości pod który ma trafić.
- **AMQP (Advanced Message Queuing Protocol)**: protokół używany przez RabbitMQ.
- **Użytkownik**: aby połączyć się do kolejki RabbitMQ potrzebujemy użytkownika i hasła. Każdy użytkownik może mieć przypisane odpowiednie uprawnienia takie jak odczyt czy zapis.
- **Vhost (virtual host)**: umożliwia separację aplikacji w jednej instancji RabbitMQ.

### Schemat działania
1. Producent publikuję wiadomość do exchange. Kiedy tworzy się exchange, typ exchange musi być podany.
1. Exchange otrzymuje wiadomość i przekierowuje wiadomość do odpowiednich kolejek w zależności o zdefiniowanego routing key i podłączonych kolejek.
1. Wiadomość zostaje w kolejce dopóki nie zostanie przeprocesowana przez konsumera.

### Typy exchange
<img src="./assets/rabbitmq/exchanges-topic-fanout-direct.png" height="300">

#### Direct
Wiadomość jest kierowana do kolejek, których binding key idealnie pasuje do routing key wiadomości. Przykładowo, jeśli kolejka jest połączona z exchange przy pomocy binding `pdfprocess`, wiadomość została opublikowana do exchange z routing key `pdfprocess`, wtedy wiadomość zostanie przekaza do kolejki.
#### Topic
Używa wzorca, który jest zdefiniowany w połączeniu kolejek do exchange i następnie sprawdza do których z nich pasuje routing key. Jeśli pasuje, to do tej kolejki jest wysyłana wiadomość (może zostać wysłana do wielu kolejek).
#### Fanout
Exchange przekujze wiadomości do wszystkich kolejek, które są z nim połączone.
#### Headers
Używa nagłówka do routingu.