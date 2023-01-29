# Zalety
## Coraz dłuższy czas tworzenia nowych funkcjonalności
W systemach monolitycznych istnieje wiele warstw, przez które trzeba się przebijać, aby realizować nowe funkcjonalności. Okazuje się, że stworzenie osobnego programu realizującego założone funkcjonalności jest często znacznie szybsze niż do implementowanie kodu w architekturze monolitycznej. Znalezienie odpowiedniego miejsca w kodzie, spełnienie wymagań interfejsów pośrednich i przejście do warstwy widoku jest coraz trudniejsze i rośnie w miarę rośnięcia złożoności kodu. Dlatego rozwiązaniem jest pisanie małych programów z interfejsem do komunikacji.
## Chodząca dokumentacja
W ferworze tworzenia oprogramowania często zapomina się o dokumentacji. Nawet jeśli stara się nadążyć z dokumentacja, to trudno ja utrzymać, tak aby była zrozumiała i pełna. Głównie wiedza na temat projektu, odpowiedzialności, powiazań, uruchomienia, wdrożenia i pozostałych rzeczy jest przechowywana w pamięci programistów. Problem się pojawia, kiedy kluczowy programista się zwolni, zachoruje lub napotka go inny losowy przypadek.  Rozwiązaniem jest tworzenie małych programów. Ponieważ każdy zespół realizuje na tyle małą i komplementarną aplikacje, że przekazanie wiedzy na jej temat jest szybkie i bezproblemowe.
## Problem w skalowaniu aplikacji
Aplikacja monolityczna ciężko się skaluje. Zazwyczaj odbywa się poprzez skalowanie jej całej – co jest bardzo kosztowne. W przypadku mikroarchitektury możemy skalować tylko ten serwis, który jest pod największym obciążeniem. Redukuje to znacznie koszty utrzymania.
## Trudność w migracji technologii 
Kiedy tworzona jest aplikacja monolityczna, to z góry zespoły mają narzucony stack technologiczny. Trudno jest później migrować rozwiązanie, lub nawet aktualizować wersje języków, frameworków czy bibliotek. W przypadku rozwiązania opartego na mikroarchitekturze, to wybór narzędzi jest dobrowolny dla każdego serwisu. Można zastosować różne języki programowania (dobrane według potrzeb danego projektu). Bez większego problemu można również aktualizować technologie w ramach serwisu, bez obawy, że będzie to miało impakt na cały system.
## Mniej kosztowne błędy
Czasem jeden ze serwisów jest błędnie napisany, lub wymaga zmian. W przypadku mikroarchitekury nie wiąże się z tak dużymi kosztami wzięcie serwisu, wyrzucenie go do kosza i przepisanie na nowo. Tak samo można (brutalnie, ale można) podejść do całego zespołu. Zwolnić cały zespół, jeśli nam nie odpowiada i zaangażować zupełnie nowy. Sprzyja to też outsourcingowi specjalistów.

# Wady
# Spójności danych 
Jest trudniejsza do implementacji, ponieważ wymaga dodatkowej koordynacji całego procesu, który może być realizowany w kliku usługach. W aplikacji monolitycznej taki problem byłby rozwiązany za pomocą transakcji na poziomie bazy danych.
## Dość wymagające testowanie i monitorowanie pracy systemu
Testowanie czy debugowanie wielu seriwsów oraz kouminikacji między nimi, wprowadza większy stopień skomplikowania niż w przypadku aplikacj monolitycznej. W związku z tym zwiększa się trudność w znalezieniu przyczyny ewentualnej awarii.

## Skomplikowana infrastruktura
Każdy mikroserwis może mieć swoją bazę danych czy inne elementy, które są wymagane do jego poprawnego działania. Wprowadza to dodatkowe elementy, które trzeba utrzymaywać.

## Dowolność technologiczna może prowadzić do chaosu
Coś co jest pluem, może przeobraźić się w minus. Niech jeden z mikroserwisów będzie napisany w innym stosie techonologicznym niż pozostałe. Wyobraźmy sobie sytację, że odchodzi osoba, która rozwijała ten mikroserwis. Pozostałe osoby nie znają technologii i mogą mieć problem z utrzymaniem tego mikroserwisu.

# Jak dzielić mikroserwisy?
Rozpocznij od określenia granic kontekstu (`boundex context`). W ogólności, funkcjonalność w mikroserwisie nie powinna obejmować więcej niż jednego granicznego kontekstu. Z definicji graniczny kontekst określa granicę danego modelu domeny. Jeśli zauważysz, że mikroserwis łączy razem różne modele domenowe, to jest to oznaka, że może trzeba powrócić i udoskonalić analizę domeny.

Następnie zwróć uwagę na agregaty w swoim modelu domeny. Agregaty często są dobrymi kandydatami na mikroserwisy. Dobrze zaprojektowany agregat wykazuje wiele cech dobrze zaprojektowanego mikroserwisu, takich jak:
- Agregat jest wyodrębniany z wymagań biznesowych, a nie z kontekstów technicznych, takich jak dostęp do danych czy komunikacja.
- Agregat powinien mieć wysoką funkcjonalną spójność.
- Agregat jest granicą trwałości.
- Agregaty powinny być słabo powiązane

Usługi domenowe (`domain services`) są również dobrymi kandydatami na mikroserwisy. Usługi domenowe to bezstanowe operacje wielu agregatów. Typowym przykładem jest proces biznesowy, który obejmuje kilka mikroserwisów.

Na koniec rozważ wymagania niefunkcjonalne. Zwróć uwagę na czynniki, takie jak wielkość zespołu, typy danych, technologie, wymagania skalowalności, wymagania dostępności i wymagania bezpieczeństwa. Te czynniki mogą skłonić do dalszej dekompozycji mikroserwisu na dwa lub więcej mniejszych serwisów lub odwrotnie - połączenia kilku mikroserwisów w jeden.

Po zidentyfikowaniu mikroserwisów w aplikacji, sprawdź swój projekt pod kątem następujących kryteriów:
- Każdy serwis ma jedną odpowiedzialność.
- Nie ma rozmownych połączeń między serwisami. Jeśli podzielenie funkcjonalności na dwa serwisy powoduje, że są one nadmiernie rozmowne, może to być symptomem tego, że te funkcje należą do tego samego serwisu.
- Każdy serwis jest wystarczająco mały, aby można było go napisać i utrzymywać przez mały zespół pracujący niezależnie.
- Nie ma wzajemnych zależności, które wymagałyby wdrożenia dwóch lub więcej serwisów jednocześnie. Zawsze powinno być możliwe wdrożenie serwisu bez przebudowy innych serwisów.
- Serwisy nie są mocno powiązane i mogą ewoluować niezależnie.

# Wzorce projektowe
## Saga
Wzorzec projektowy Saga to sposób zarządzania spójnością danych między mikrousługami w scenariuszach transakcji rozproszonych. Saga to sekwencja transakcji, która aktualizuje każdą usługę i publikuje zdarzenie w celu wyzwolenia następnego kroku transakcji. Jeśli krok zakończy się niepowodzeniem, saga wykonuje transakcje wyrównywujące, które przeciwdziałają poprzednim transakcjom.
![image](/assets/mikroserwisy/saga-overview.png)
Istnieją dwa typowe podejścia implementacji sagi, choreografia i orkiestracja. Każde podejście ma własny zestaw wyzwań i technologii do koordynowania przepływu pracy.
### Choreografia
Choreografia to sposób koordynowania sag, w których uczestnicy wymieniają wydarzenia bez scentralizowanego punktu kontroli. Z choreografią każda transakcja lokalna publikuje zdarzenia domeny, które wyzwalają transakcje lokalne w innych usługach.
![image](/assets/mikroserwisy/choreography-pattern.png)
#### Plusy
- Dobry dla prostych przepływów pracy, które wymagają kilku uczestników i nie potrzebują logiki koordynacji.
- Nie wymaga dodatkowej implementacji i konserwacji usługi.
- Nie wprowadza jednego punktu awarii, ponieważ obowiązki są rozłożone na uczestników sagi.
#### Wady
- Przepływ pracy może stać się mylący podczas dodawania nowych kroków, ponieważ trudno jest śledzić, którzy uczestnicy sagi słuchają poleceń.
- Istnieje ryzyko cyklicznej zależności między uczestnikami sagi, ponieważ muszą korzystać ze sobą poleceń.
- Testowanie integracji jest trudne, ponieważ wszystkie usługi muszą być uruchomione w celu symulowania transakcji.

### Orkiestracja
Orkiestracja to sposób koordynowania sag, w których scentralizowany kontroler informuje uczestników sagi o tym, co mają być wykonywane transakcje lokalne. Orkiestrator saga obsługuje wszystkie transakcje i informuje uczestników, które operacje mają być wykonywane na podstawie zdarzeń. Orkiestrator wykonuje żądania sagi, przechowuje i interpretuje stany każdego zadania i obsługuje odzyskiwanie po awarii za pomocą transakcji wyrównywalnych.
![image](/assets/mikroserwisy/orchestrator-pattern.png)

#### Plusy
- Dobre dla złożonych przepływów pracy obejmujących wielu uczestników lub nowych uczestników dodanych w czasie.
- Nadaje się do kontroli nad każdym uczestnikiem procesu i kontroli nad przepływem działań.
- Nie wprowadza cyklicznych zależności, ponieważ orkiestrator jednostronnie zależy od uczestników sagi.
- Uczestnicy sagi nie muszą wiedzieć o poleceniach dla innych uczestników. Jasne rozdzielenie problemów upraszcza logikę biznesową.
#### Wady
- Dodatkowa złożoność projektowania wymaga implementacji logiki koordynacji.
- Występuje dodatkowy punkt awarii, ponieważ koordynator zarządza kompletnym przepływem pracy.

### Kiedy używać tego wzorca
Użyj wzorca Saga, jeśli musisz:
- Zapewnienia jednoznaczności danych w systemie rozproszonym bez mocnego powiązania.
- Wycofania lub rekompensaty, jeśli jedna z operacji w sekwencji nie powiedzie się.

Wzorzec saga jest mniej odpowiedni dla:
- Ściśle powiązanych transakcji.
- Rekompensowania transakcji, które występują w wcześniejszych uczestnikach.
- Cyklicznych zależności.

## Asynchronous Request-Reply
W środowisku IT składającym się z wielu współpracujących ze sobą aplikacji, niezależnie od tego, czy są to mikrousługi, czy nie, często stosuje się komunikację asynchroniczną w celu zminimalizowania lub wyeliminowania ścisłego powiązania między aplikacjami. Ścisłe sprzężenie między aplikacjami jest często spowodowane komunikacją synchroniczną, czyli wtedy, gdy jedna aplikacja wywołuje inną i czeka na odpowiedź.

Problem z komunikacją synchroniczną polega na tym, że jeśli maszyna B nie działa, na przykład podczas wdrażania lub konserwacji, żądania z maszyny A do maszyny B zakończą się niepowodzeniem. Oznacza to, że klienci inicjujący operacje tworzenia zamówień za pośrednictwem komputera A również zakończą się niepowodzeniem.
![image](/assets/mikroserwisy/request-replay-synchronous.webp)

### Rozwiązanie
Aby uniknąć ścisłego powiązania z maszyną B, można wprowadzić komunikację asynchroniczną. Na rysunku poniżej widzimy, że zamiast bezpośredniej interakcji maszyny A z maszyną B przesyła ona wiadomość do kolejki. Gdy maszyna B jest gotowa, odbiera i przetwarza komunikaty w kolejce. Ważną częścią jest tutaj to, że zadanie maszyny A jest wykonywane natychmiast po przesłaniu wiadomości. Nie ma znaczenia, kiedy wiadomość zostanie przetworzona i czy pójdzie dobrze.
![image](/assets/mikroserwisy/request-replay-asynchronous.webp)

Ale co teraz, jeśli maszyna A wymaga wyniku z maszyny B po przetworzeniu komunikatu o utworzeniu zamówienia? Może się zdarzyć, że maszyna A chce zaktualizować lokalny status zamówienia, które ma zostać utworzone lub nie, w zależności od wyniku przetwarzania go przez maszynę B. W tym miejscu pojawia się omawiany wzorzec.

Można go zaimplementować przy użyciu sondowania HTTP. Sondowanie jest przydatne w kodzie po stronie klienta, ponieważ może być trudne długotrwałe utrzymywanie połączenia.
![image](/assets/mikroserwisy/request-replay-diagram.png)
1. Klient wysyła żądanie i odbiera odpowiedź HTTP 202 (zaakceptowana).
1. Klient wysyła żądanie HTTP GET do punktu końcowego stanu. Praca jest nadal oczekująca, więc to wywołanie zwraca protokół HTTP 200.
1. W pewnym momencie praca jest ukończona, a punkt końcowy stanu zwraca 302 (znaleziono) przekierowanie do zasobu.
1. Klient pobiera zasób pod określonym adresem URL.

Maszyna 
