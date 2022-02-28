# Wzorce kreacyjne
## Metoda wytwórcza
Udostępnia interfejs do tworzenia obiektów w ramach klasy bazowej, ale pozwala podklasom zmieniać typ tworzonych obiektów.

### Problem
Wyobraź sobie, że tworzysz aplikację do zarządzania logistyką. Pierwsza wersja twojej aplikacji pozwala jedynie na obsługę transportu za pośrednictwem ciężarówek, więc większość kodu znajduje się wewnątrz klasy `Ciężarówka`.

Po jakimś czasie twoja aplikacja staje się całkiem popularna. Codziennie otrzymujesz tuzin próśb od firm realizujących spedycję morską, abyś dodał stosowną funkcjonalność do swej aplikacji.

Świetna wiadomość, prawda? Ale co z kodem? W tej chwili większość twojego kodu jest powiązana z klasą Ciężarówka. Dodanie do aplikacji klasy Statki wymagałoby dokonania zmian w całym kodzie. Co więcej, jeśli później zdecydujesz się dodać kolejny rodzaj transportu, zapewne będziesz musiał dokonać tych zmian jeszcze jeden raz.

Rezultatem powyższych działań będzie brzydki kod, pełen instrukcji warunkowych, których zadaniem będzie dostosowanie zachowania aplikacji zależnie od klasy transportu.

### Rozwiązanie
Wzorzec projektowy Metody wytwórczej proponuje zamianę bezpośrednich wywołań konstruktorów obiektów na wywołania specjalnej metody wytwórczej. Obiekty powstają za kulisami — z wnętrza metody wytwórczej.

Na pierwszy rzut oka zmiana ta może wydawać się bezcelowa. Przecież przenieśliśmy jedynie wywołanie konstruktora z jednej części programu do drugiej. Ale zwróć uwagę, że teraz możesz nadpisać metodę wytwórczą w podklasie, a tym samym zmienić klasę produktów zwracanych przez metodę.

Istnieje jednak małe ograniczenie: podklasy mogą zwracać różne typy produktów tylko wtedy, gdy produkty te mają wspólną klasę bazową lub wspólny interfejs. Ponadto, zwracany typ metody wytwórczej w klasie bazowej powinien być zgodny z tym interfejsem.

Na przykład zarówno klasy `Ciężarówka`, jak i `Statek` powinny implementować interfejs `ITransport`, który z kolei deklaruje metodę `dostarczaj`. Każda klasa różnie implementuje tę metodę: ciężarówki dostarczają towar drogą lądową, statki drogą morską. Metoda wytwórcza znajdująca się w klasie `LogistykaDrogowa` zwraca obiekty `Ciężarówka`, zaś metoda wytwórcza w klasie `LogistykaMorska` zwraca `Statek`.

Kod, który wykorzystuje metodę wytwórczą (zwany często kodem klienckim) nie widzi różnicy pomiędzy faktycznymi produktami zwróconymi przez różne podklasy. Klient traktuje wszystkie produkty jako abstrakcyjnie pojęty `ITransport`. Klient wie także, że wszystkie obiekty transportowe posiadają metodę `dostarczaj`, ale szczegóły jej działania nie są dla niego istotne.

### Zastosowanie
Stosuj Metodę Wytwórczą gdy nie wiesz z góry jakie typy obiektów pojawią się w twoim programie i jakie będą między nimi zależności.

 Metoda Wytwórcza oddziela kod konstruujący produkty od kodu który faktycznie z tych produktów korzysta. Dlatego też łatwiej jest rozszerzać kod konstruujący produkty bez konieczności ingerencji w resztę kodu.

Przykładowo, aby dodać nowy typ produktu do aplikacji, będziesz musiał utworzyć jedynie podklasę kreacyjną i nadpisać jej metodę wytwórczą.

## Fabryka abstrakcyjna
Pozwala tworzyć rodziny spokrewnionych ze sobą obiektów bez określania ich konkretnych klas.

### Problem
Wyobraź sobie, że tworzysz symulator sklepu meblowego. Twój kod składa się z klas, które reprezentują rodzinę spokrewnionych produktów: `Fotel`, `Sofa` i `StolikKawowy`. Plus każdy z produktów na swój wariant: `Nowoczesny`, `Wiktoriański` i  `ArtDeco`. Trzeba produkować poszczególne meble w taki sposób, aby do siebie pasowały. Klienci nie cierpią bowiem otrzymywać mebli w zupełnie różnych stylizacjach.

### Rozwiązanie
Pierwszą rzeczą, jaką proponuje wzorzec projektowy Fabryka abstrakcyjna, jest wyraźne określenie interfejsów dla każdego konkretnego produktu z jakiejś rodziny. Następnie trzeba sprawić, aby wszystkie warianty produktów były zgodne z tymi interfejsami. Na przykład wszystkie fotele implementują interfejs `IFotel`, wszystkie stoliki kawowe implementują interfejs `IStolikKawowy` i tak dalej.

Kolejnym krokiem jest deklaracja interfejsu Fabryka abstrakcyjna (przykładowo `IFabrykaMebli`), który zawrze listę metod kreacyjnych wszystkich produktów w ramach jednej rodziny (na przykład, `stwórzFotel`, `stwórzSofę`, `stwórzStolikKawowy`). Metody te muszą zwracać wyłącznie abstrakcyjne typy produktów, reprezentowane uprzednio określonymi interfejsami: `IFotel`, `ISofa`, `IStolikKawowy` i tak dalej.

A co z poszczególnymi wariantami produktów? Otóż, dla każdego wariantu rodziny produktów tworzymy osobną klasę fabryczną na podstawie interfejsu `IFabrykaMebli`. Klasa fabryczna to taka klasa, która zwraca produkty danego rodzaju. A więc, `FabrykaNowoczesnychMebli` może zwracać wyłącznie obiekty: `NowoczesnyFotel`, `NowoczesnaSofa` oraz `NowoczesnyStolikiKawowy`.

Kod kliencki będzie korzystał z fabryk oraz produktów za pośrednictwem ich interfejsów abstrakcyjnych. Dzięki temu będzie można zmienić typ fabryki przekazywanej kodowi klienckiemu oraz zmienić wariant produktu jaki otrzyma kod kliencki i to wszystko bez ryzyka popsucia samego kodu klienckiego.

### Zastosowanie
Stosuj Fabrykę abstrakcyjną, gdy twój kod ma działać na produktach z różnych rodzin, ale jednocześnie nie chcesz, aby ściśle zależał od konkretnych klas produktów. Mogą one bowiem być nieznane na wcześniejszym etapie tworzenia programu, albo chcesz umożliwić przyszłą rozszerzalność aplikacji.

## Budowniczy
Daje możliwość tworzenia złożonych obiektów etapami, krok po kroku. Wzorzec ten pozwala produkować różne typy oraz reprezentacje obiektu używając tego samego kodu konstrukcyjnego.

### Problem
Na przykład pomyślmy, jak stworzyć obiekt `Dom`. Do zbudowania najprostszego domu wystarczą cztery ściany i podłoga. Do tego drzwi, parę okien i dach. Ale co, jeśli chcesz większy, jaśniejszy dom z podwórkiem i innymi dodatkami (ogrzewanie, kanalizacja, elektryczność)?

Najprostsze rozwiązanie to rozszerzenie klasy bazowej `Dom` i stworzenie zestawu podklas, które spełniałyby każdy możliwy zestaw wymogów. Ale takie podejście doprowadzi do wielkiej liczby podklas. Dodanie kolejnego parametru, jak styl werandy, jeszcze bardziej rozbuduje tę hierarchię.

Istnieje jednak inne rozwiązanie, które nie wiąże się z mnożeniem podklas. Można stworzyć jeden wielki konstruktor w klasie bazowej `Dom`, uwzględniający wszystkie możliwe parametry, które sterują obiektem typu dom. W ten sposób nie mnożymy liczby klas, ale tworzymy nieco inny problem.

W większości przypadków parametry pozostaną nieużyte, a wywołania konstruktora będą wyglądać niechlujnie. Na przykład tylko niektóre domy mają basen, więc parametry dotyczące basenu w dziewięciu na dziesięć przypadków będą niepotrzebne.

### Rozwiązanie
Wzorzec projektowy Budowniczy proponuje ekstrakcję kodu konstrukcyjnego obiektu z jego klasy i umieszczenie go w osobnych obiektach zwanych budowniczymi.

Ten wzorzec projektowy dzieli konstrukcję obiektu na pewne etapy (`budujŚciany`, `wstawDrzwi`, itd.). Aby powołać do życia obiekt, wykonuje się ciąg takich etapów za pośrednictwem obiektu-budowniczego. Istotne jest to, że nie musisz wywoływać wszystkich etapów. Możesz bowiem ograniczyć się tylko do tych kroków, które są niezbędne do określenia potrzebnej nam konfiguracji obiektu.

Niektóre etapy konstrukcji mogą wymagać odmiennych implementacji, zależnie od potrzebnej w danej chwili reprezentacji produktu. Na przykład, ściany leśnej chatki mogą być drewniane, ale mury zamku warownego — kamienne.

W takim przypadku, można utworzyć wiele różnych klas budowniczych które implementują te same etapy konstrukcji, ale w różny sposób. Można następnie korzystać z tych budowniczych podczas procesu konstrukcji (np. odpowiednia kolejność wywołań etapów budowy) aby wytworzyć różne rodzaje obiektów.

### Zastosowanie
Stosuj wzorzec Budowniczy, aby pozbyć się “teleskopowych konstruktorów”.

Wzorzec Budowniczy pozwala konstruować obiekty krok po kroku, w miarę jak staje się to w programie potrzebne. Po zaimplementowaniu tego wzorca, nie musisz przekazywać konstruktorowi tuzina parametrów.

Stosuj wzorzec Budowniczy, gdy potrzebujesz możliwości tworzenia różnych reprezentacji jakiegoś produktu (na przykład, domy z kamienia i domy z drewna).

Wzorzec Budowniczy można użyć gdy konstruowanie różnorakich reprezentacji produktu obejmuje podobne etapy, które różnią się jedynie szczegółami.

## Prototyp
Umożliwia kopiowanie już istniejących obiektów bez tworzenia zależności pomiędzy twoim kodem, a klasami obiektów.

## Singelton
Pozwala zapewnić istnienie wyłącznie jednej instancji danej klasy. Ponadto daje globalny punkt dostępowy do tejże instancji.

# Wzorce kreacyjne
## Adapter
Pozwala na współdziałanie ze sobą obiektów o niekompatybilnych interfejsach.

## Dekorator
Pozwala dodawać nowe obowiązki obiektom poprzez umieszczanie tych obiektów w specjalnych obiektach opakowujących, które zawierają odpowiednie zachowania.

## Fasada
Wyposaża bibliotekę, framework lub inny złożony zestaw klas w uproszczony interfejs.

# Wzorce behawioralne
## Mediator
Pozwala zredukować chaos zależności pomiędzy obiektami. Wzorzec ten ogranicza bezpośrednią komunikację pomiędzy obiektami i zmusza je do współpracy wyłącznie za pośrednictwem obiektu mediatora.

## Obserwator
Pozwala zdefiniować mechanizm subskrypcji w celu powiadamiania wielu obiektów o zdarzeniach dziejących się w obserwowanym obiekcie.

## Strategia
Pozwala zdefiniować rodzinę algorytmów, umieścić je w osobnych klasach i uczynić obiekty tych klas wymienialnymi.