# Domain Driven Design (DDD) 
Jest to sposób tworzenia aplikacji i systemów, które w głównej mierze powinny w jak najlepszy sposób odzwierciedlać potrzeby biznesu – a więc założenia biznesowe, które stają się jednocześnie warunkami koniecznymi zawartymi w oprogramowaniu.

Mikrousługi powinny być zaprojektowane wokół możliwości biznesowych, a nie warstw poziomych, takich jak dostęp do danych lub obsługa komunikatów. Mikrousługi są luźno powiązane, jeśli można zmienić jedną usługę bez konieczności jednoczesnego aktualizowania innych usług. Mikrousługę jest spójna, jeśli ma jeden, dobrze zdefiniowany cel, taki jak zarządzanie kontami użytkowników lub śledzenie historii dostarczania. Usługa powinna hermetyzować wiedzę o domenie i abstrakować wiedzę od klientów.

Projektowanie oparte na domenach znacznie ułatwia opracowanie zestawu dobrze zaprojektowanych mikrousług. Projektowanie DDD ma dwie odrębne fazy: strategiczną i taktyczną.  

# Strategiczne DDD
W fazie strategicznej definiuje się ogólną strukturę systemu, która zapewnia ukierunkowanie architektury na zdolności biznesowe. W tej fazie definoiwane są ograniczony kontekst, wszechobecny język czy mapa kontekstów.

## Ograniczony kontekts (*bounded context*)
Ograniczony kontekst jest jedną z najważniejszych koncepcji DDD, można powiedzieć, że jest to granica pojęciowa, w której ma zastosowanie model dziedzinowy. Oznacza to, że w kadżdy komponent ma określone znaczenie i robi określone rzeczy.
Podczas próby modelowania dużej domeny napotkasz duże trudności, ponieważ różne grupy ludzi będą używać subtelnie różnych terminów i zdań. Oznacza to, że każde użycie tego słownictwa poza tym limitem prawdopodobnie będzie oznaczać coś innego.

Można więc powiedzieć, że ograniczony kontekst jest głównie rozgraniczeniem językowym. Ważne jest, aby zrozumieć, że każdy ograniczony kontekst będzie miał swój własny wszechobecny język.

W miarę ewolucji Twojego modelu poczujesz potrzebę stworzenia relacji między kontekstami ograniczonymi, w tym celu użyjemy Map kontekstowych.

## Wszechobecny język (*ubiquitous language*)
Wszechobecny język jest modelowany w ograniczonym kontekście, w którym identyfikowane są terminy i koncepcje domeny biznesowej, gdzie nie powinno być dwuznaczności. Aby opracować skuteczny wszechobecny język, musisz zrozumieć biznes.

Jedną z wielkich zalet wszechobecnego języka jest zgromadzenie ekspertów dziedzinowych, zespołu technicznego i innych osób zaangażowanych w projekt. Wszechobecny język jest rozwijany w porozumieniu całego zespołu oraz musi być używany przez cały czas między członkami zespołu.

## Mapa kontekstów (*context maps*)
Mapy kontekstów pomagają w zrozumieniu całego projektu, będąc w stanie pokazać relacje między różnymi ograniczonymi kontekstami.
Niezwykle ważne jest zrozumienie relacji między ograniczonymi kontekstami, aby można było poprawnie zbudować model domeny.

### Wspólne jądro (*shared kernel*)
Wspólny kontekst między dwoma lub więcej zespołami, co ogranicza powielanie kodu, jednak wszelkie zmiany muszą być łączone i zgłaszane między zespołami.

### Dostawca/klienta (*customer/supplier*)
Jest to relacja między klientem (downstream), a serwerem (upstream), gdzie zespoły są w ciągłej integracji.

### Konformista (*conformist*)
Jest to scenariusz, który obejmuje zespoły upstream i downstream, ale w tym modelu zespół upstream nie ma motywacji do zaspokojenia potrzeb zespołu downstream.

### Partner
Jest to scenariusz, w którym zespoły są zależne i muszą mieć relację współpracy, aby mogły zaspokoić potrzeby rozwojowe obu systemów.

### Warstwa antykorupcyjna (*anti corruption layer*)
Jest to scenariusz, w którym klient (downstream) tworzy warstwę pośrednią, która komunikuje się z kontekstem upstream, aby sprostać własnemu modelowi domeny.

# Taktyczne DDD
W fazie taktycznej używany jest zestaw wzorców projektowych, które pozwalają utworzyć model domeny. Wzorce te obejmują encje, agregacje i usługi domenowe.

## Encja (*entity*)
Encja opisuje znaczący kawałek domeny, który posiada unikalną tożsamość wraz z zestawem zachowań i danych (które mogą się zmieniać z czasem). 
- Identyfikowalne, ma tożsamość – czyli posiada coś, co pozwala ją jednoznacznie zidentyfikować – jest to główna cecha encji. Obecnie najczęściej nadaje się tożsamość obiektowi domenowemu przez wygenerowaniu mu Id, za co odpowiedzialny jest silnik bazodanowy. Innymi przykładami z życia to numer konta bankowego albo numer seryjny urządzenia nadawany przez producentów. Encja będzie się zmieniać z czasem i może wyglądać zupełnie inaczej niż na samym początku, gdy ją tworzyliśmy, ale dalej będzie miała tą samą tożsamość (np. Id).
- Opisuje coś znaczącego w domenie – encja powinna modelować konkretną logikę domenową aplikacji, np. jeżeli mielibyśmy obiekt User, to mógłby on zawierać logikę związaną z rejestracją użytkownika lub też jego zablokowaniem. Jeżeli mielibyśmy obiekt, który trzyma tylko typy proste, jedynie dba o ich poprawną wartością i nie posiada żadnej innej logiki domenowej to prawdopodobnie nie jest to Encja (może Value Objects?), raczej będzie to anemiczny model, który jest antywzorcem.
- Posiada domenowe zachowania i dane – encja posiada metody, które odpowiadają biznesowym akcjom lub procesom. Jeżeli akcją biznesową jest „Publikowanie Artykułu” to możliwe, że nasza domena będzie zawierać encję Article z metodą Publish().
- Zmiana danych Encji odbywa się wyłącznie przez metody – nie ustawiamy właściwości przez bezpośrednie przypisanie wartości do nich, tylko używamy odpowiednich metod, które jednocześnie dbają o poprawność danych.
- Encja posiada walidację, która zapobiega wprowadzeniu jej w niepoprawny stan – każda metoda, która zmienia stan Encji, powinna weryfikować parametry wejściowe oraz stan całego obiektu.
- Publikuje zdarzenia domenowe– wracając do przykładu z metodą Publish() – to na jej końcu moglibyśmy wygenerować event ArticleHasBeenPublishedEvent, który by powiadamiał cały system o tym wydarzeniu.

## Obiekt wartości (*value object*)
Obiekt wartości nie ma tożsamości. Jest on definiowany tylko przez wartości jego atrybutów. Obiekty wartości są również niezmienne. Aby zaktualizować obiekt wartości, zawsze należy utworzyć nowe wystąpienie, aby zastąpić stary.  Typowe przykłady obiektów wartości obejmują kolory, daty i godziny oraz wartości walutowe.

## Agregat (*aggregate*) 
Grupuje on powiązane biznesowo ze sobą Encje i Value Objects, które można traktować jako pojedynczy obiekt. Agregat zapewnia transakcyjności operacji biznesowych w systemie.

Posiada Encję, która jest korzeniem agregatu (Aggregate root) – inaczej mówiąc wszystkie powiązane Encje i ValueObjecty mają jednego rodzica – Aggregate Root. Jeżeli usuniemy rodzica to usuwamy również wszystkie powiązane obiekty. Bez Aggregate Root ich istnienie nie ma sensu. Np. możemy mieć agregat Calendar, która posiada kolekcję CalendarEvents, jeżeli usuniemy instancję Calendar to instancje CalendarEvents już nie mają sensu, ponieważ były powiązane z konkretnym kalendarzem. Jest to bardzo ważna zasada, która pomaga nam poprawnie modelować agregaty.

## Serwis domenowy (*domain service*)
Serwis domenowy (domain service) to biznesowa operacja, która nie wpasowuje się w odpowiedzialności Agregatów lub Value Objectów. 
- Bezstanowy (stateless) – nie przechowuje żadnych danych.
- Należy do warstwy domenowej – nazwy metod serwisów domenowych odzwierciedlają operacje biznesowe.
- Specjalizuje się w robieniu tylko jednej rzeczy – jeden serwis domenowy jest odpowiedzialny za robienie tylko jednej rzeczy.
- Może publikować zdarzenia domenowe (Domain Events) – może powiadamiać resztę systemu, że dana operacja domenowa została zakończona.
- Może wchodzić w interakcje z innymi serwisami domenowymi lub repozytoriami – czyli mogą wykorzystywać inne serwisy domenowe i do pewnego stopnia repozytoria.
- Przykład: Serwis, który konwertuje temperature (która w tym wypadku jest ValueObjectem) z stopni Celsjusza na Fahrenheita i w drugą stronę. Serwis ma dwie metody ale obie robią tylko jedną i tą samą rzecz – konwertuje jeden obiekt domenowy na drugi.

## Repozytorium (*repository*)
Jego rolą jest zapisywanie i pobieranie obiektów z bazy danych lub innego miejsca gdzie możemy utrwalić dane.
- Operują na Agregatach – jest to najważniejsza właściwość repozytorium – pobiera lub zapisuje wyłącznie agregaty wraz z połączonymi encjami (aczkolwiek są pewne wyjątki od tego 😉, o tym poniżej). Powinniśmy dążyć do tego, aby wyciągać całe agregaty, a nie tylko jego części.
- Repozytoria mogą być używane przez Agregaty – do pobierania dzieci (np. Lazy Loading w przypadku dużych agregatów) lub do pobierania potrzebnych danych z innych agregatów – są to wyjątki do powyższej zasady.
- Jedno Repozytorium na jeden agregat – Każdy agregat powinien mieć swoje repozytorium, za pomocą którego można go zapisać lub pobrać.

## Fabryka (*factory*)
Głównym celem fabryk, albo mówiąc inaczej metod wytwórczych jest uproszczenie tworzenia skomplikowanych obiektów.
- Tworzenie instancji złożonych obiektów – Agregat, Encja lub Value Objects może posiadać np. statyczną metodę wytwórczą, która upraszcza tworzenie instancji obiektów domenowych posiadających np. konstruktory z wieloma parametrami.
- Nazwy metod odwzorowują domenowy język – konstruktory mówią zazwyczaj jakie dane są wymagane do utworzenia obiektu, a nie co konkretnie powstanie np. mając klasę Car, która przyjmuje jako parametry ilość koni mechanicznych i prędkość maksymalną, to przy użyciu samego konstruktora nie dowiemy się jaki samochód tworzymy, ale jeżeli mielibyśmy metodę CreateFerrari, która by tworzyła obiekt Car z odpowiednimi parametrami, to kod byłby dużo bardziej czytelny.

## Zdarzenie domenowe (*domain event*)
Są odpowiedzialne za powiadamianie całej naszej domeny o tym, że coś interesującego się wydarzyło. Inna część aplikacji może nasłuchiwać na konkretne wydarzenie i zareagować na nie. Agregaty lub Encje publikują zdarzenia — każda metoda, która zmienia stan systemu, może publikować zdarzenia domenowe.

# Event storming
Event Storming jest metodą odkrywania i konkretyzowania informacji o domenie biznesowej, w ramach której wytwarzane jest oprogramowanie.

## Kto bierze udział
- eksperci techniczni, jak np. programiści, analitycy, czy właściciele produktu (PO),
- eksperci domenowi ze strony klienta/biznesu,
- koordynator - osoba zaznajomiona z techniką Event Stormingu, dbająca o sprawny i prawidłowy przebieg warsztatu.

## Przebieg
Jest to zazwyczaj kilkugodzinny warsztat. Sama forma warsztatu wydaje się dość prosta – do dyspozycji mamy dużą tablicę czy ścianę oraz mnóstwo różnokolorowych karteczek. Każdy z uczestników identyfikuje zdarzenia („Domain Events”), które występują w trakcie działania programu – np. „Utworzono konto użytkownika”, „Zamówiono towar”, czy „Wygenerowano fakturę”. Zdarzenia te zapisuje się na karteczkach i umieszcza na tablicy.

Jak zapewne można zauważyć, powyższe przykłady zdarzeń to bardzo ogólne pojęcia. Podczas Event Stormingu staramy się nie korzystać ze słownictwa fachowego i języka ściśle specjalistycznego. Celem jest, aby każdy rozumiał każdego, a używane określenia funkcjonowały w ramach danej domeny biznesowej.

Powyższe zdarzenia domenowe stanowią punkt wyjścia do dalszych pytań – co należy zrobić, aby miało miejsce zdarzenie „Utworzono konto użytkownika”? Program musi otrzymać sygnał, czyli musi być przekazana komenda („Command”) taka, jak np. „Utwórz konto”. Ale kto lub co jest źródłem takiej komendy? Czy to użytkownik sam tworzy swoje konto, poprzez rejestrację? Czy może konto użytkownika generowane jest automatycznie, na podstawie informacji otrzymanych z systemu zewnętrznego? I tu dochodzimy do kolejnego elementu identyfikowanego podczas warsztatu, którym są tzw. aktorzy („Actors”) wywołujący daną komendę.

Ustalenia dotyczące komend i aktorów też trzeba jakoś zapisać. Oczywiście w formie kolejnych karteczek, które jedna za drugą trafiają na coraz bardziej zapełniającą się tablicę. Na dalszym etapie warsztatu grupuje się powiązane ze sobą elementy – wyodrębniać zaczynają się tzw. agregaty („Aggregates”).

## Zalety
### Zrozumienie domeny
Podczas Event Stormingu następuje wymiana wiedzy pomiędzy ekspertami technicznymi a ekspertami domenowymi. Nie rozmawia się o bazach danych, frameworkach, czy mapowaniach relacyjno- obiektowych. Zamiast tego, wszyscy uczestnicy posługują się pojęciami domenowymi. Eksperci techniczni zapoznają się dzięki temu z domeną, a eksperci domenowi zgłębiają sposób działania danego oprogramowania.

### Wspólny język
Jako styk biznesu i programowania, Event Storming pozwala ujednolicić pojęcia używane we współpracy z klientem. A wspólny i jednolity język to już spory krok do stworzenia projektu na podstawie Domain-Driven Design.

### Szybkość działania
W przypadku nowych dopiero rozpoczynających się projektów, Event Storming jest alternatywą dla innych, dłuższych procesów planowania wstępnego. Zebranie w ramach jednego, kilkugodzinnego warsztatu różnych osób zaangażowanych w dany produkt, już na tak wczesnym etapie jego tworzenia, może być korzystniejsze niż odkrywanie poszczególnych wymogów produktowych dopiero w trakcie developmentu.

### Dystrybucja wiedzy
Event Storming propaguje wiedzę – tak wewnątrz zespołów, jak i pomiędzy nimi. Taka dystrybucja wiedzy między różne osoby zaangażowane w wytwarzanie oprogramowania przeciwdziała tworzeniu tzw. silosów wiedzy. Specjalistom technicznym daje to możliwości nauki i większego zaangażowania w projekt, a także zapewnia płynność prac w projekcie, na wypadek zmian kadrowych.

### Zapewnienie jakości
Event Storming przekłada się nie tylko na zrozumienie domeny, ale także na dogłębną analizę zależności pomiędzy poszczególnymi funkcjonalnościami oprogramowania. A to właśnie osadzona w wiedzy domenowej, pełna świadomość procesów zachodzących w wytwarzanym produkcie jest jednym z czynników prowadzących do dostarczenia produktu wysokiej jakości.

### Identyfikacja problemów
Również długo funkcjonujące projekty zyskać mogą na wdrożeniu Event Stormingu jako narzędzia analizy problemów, używanego, chociażby do eliminowania tzw. wąskich gardeł.

### Integracja
Event Storming stanowi okazję do integracji zespołu poprzez wspólną naukę – oparty jest przecież przede wszystkim na rozmowie i zaangażowaniu wielu stron. A dla osób rozpoczynających pracę w danym projekcie, udział w sesji Event Stormingu może być jedną z metod wdrożenia projektowego.