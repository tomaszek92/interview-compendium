# Założenia języka C#
## Kod zarządzany
Przez lata najczęstszym sposobem działania kompilatorów było przetwarzanie kodu źródłowego i generowanie wyników, których postać pozwalała na ich bezpośrednie wykonanie przez procesor. Kompilatory generowały kod maszynowy- instrukcje w formacie wymaganym przez konkretny rodzaj procesora. Kompilator języka C# działa jednak w modelu bazującym na generowaniu kodu zarządzanego (managed code).
W przypadku kodu zarządzanego to środowisko uruchomieniowe, a nie kompilator generuje kod maszynowy wykonywany następnie przez procesor. Dzięki temu środowisko uruchomieniowe jest w stanie dostarczyć usług, których udostępnianie w tradycyjnym modelu działania byłoby trudne lub nawet niemożliwe. Kompilator generuje pośrednią formę kodu binarnego, tak zwany język pośredni (intermediate language IL). Natomiast środowisko uruchomieniowe tworzy wykonywalny kod binarny w trakcie działania programu.
## CLR Common Language Runtime
To środowisko uruchomieniowe dla platformy .NET, przewidziane do pracy na wielu systemach operacyjnych.Wspólne środowisko uruchomieniowe to podstawa całego systemu .NET Framework. Wszystkie języki środowiska .NET (na przykład C# czy Visual Basic .NET), a także wszystkie biblioteki klas obecne w .NET Framework (ASP.NET, ADO.NET i inne) oparte są na CLR.
Środowisko CLR kompiluje i wykonuje zapisany w standardowym języku pośrednim CIL (dawniej MSIL) kod aplikacji zwany kodem zarządzanym (ang. managed code), zapewniając wszystkie podstawowe funkcje konieczne do działania aplikacji. Podstawowym elementem CLR jest standardowy zestaw typów danych, wykorzystywanych przez wszystkie języki oparte na CLR, a także standardowy format metadanych, służących do opisu oprogramowania wykorzystującego te typy danych. W CLR wbudowane są także mechanizmy kontroli bezpieczeństwa wykonywania aplikacji — bezpieczeństwo oparte na uprawnieniach kodu (Code Access Security — CAS) oraz bezpieczeństwo oparte na rolach (Role-Based Security — RBS).
### JIT just in time
Każda funkcja jest kompilowana w trakcie działania programu, przed jej pierwszym wywołaniem.
### AOT (ahead of time)
Każda funkcja jest kompilowana przed uruchomieniem programu.

# Typy
## Wartościowe
- `int`, `long`, `float`, `double`, `byte`, `char`, `bool`, `DateTime`, typy wyliczeniowe - enum, struktury
- dziedziczą z `System.ValueType`, który dziedziczy `System.Object`
- obiekt umieszczany na stosie (heap)
- przekazywanie przez wartości do funkcji
- nie zmieniają swojej wielkości
- kopiowanie poprzez operator przypisania (`=`)
- nie można definiować wartości domyślnych i konstruktora bezparametrowego
- nie można po nich dziedziczyć - są zamknięte
- używamy gdy
    - obiekty są małe
    - obiekty są logicznie niezmienne
### Enum
- pierwszy element ma domyślnie wartość 0 chyba, że zmienimy
- możemy dziedziczyć z long’a, wtedy mamy więcej, lub na byte (domyślnie z  int’a)
- nie możemy definiować w nich żadnych składowych
- Dodanie atrybutu `[Flag]` powoduje, że wywołanie metody `ToString()` wyświetli nazwy wartości zamiast wartości liczbowej dla zmiennej, która składa się z kilku wartości
- polepszają czytelność kodu: zamiast np: `new Person(20, true)`, lepiej `new Person(20, Gender.Male)`
## Referencyjne
- `string`, klasy, delegacje, tablice
- zmienne tego typu nie przechowują samych instancji klasy, lecz referencje do nich
- dziedziczą z `System.Object`
- obiekt umieszczany na stercie (stack), referencja do obiektu umieszczana na stosie
- przekazywanie referencji do funkcji
- mogą zmieniać swoja wielkość
## Kiedy używać referencyjnych, a kiedy wartościowych?
Najważniejszym pytaniem jest to, czy tożsamość danych ma dla nas jakiekolwiek znaczenie, czy nie. Innymi słowy, czy ważne jest rozróżnienie pomiędzy jednym obiektem a drugim. W przypadku typu Counter takie rozróżnienie jest ważne: jeśli chcemy, by coś dla nas liczyło, to najprościej będzie, jeśli ten licznik będzie czymś unikatowym, dysponujący własną tożsamością. Z kolei w przypadku typu Point, odpowiedź na to pytanie jest negatywna, dlatego też jest on rozsądnym kandydatem do bycia typem wartościowym.
## struct
Od C# 7.2 możliwe jest zadeklarowanie, by struktura była przeznaczona tylko do odczytu. W tym celu trzeba dodać `readonly` przed `struct`.
### Konstruktor
Nie można definiować konstruktora bezargumentowego (domyślnie jest generowany -> wszystkie wartości ustawiane na 0 dla liczbowych, false dla bool i null dla referencyjnych)- jest to kopia płytka.
## class
- domyślnie dostępność ustawiona na `internal`
- możemy ustawić jeszcze `public` oraz `private` - tylko dla typu zagnieżdżonego
### Konstruktor
- domyślnie generowany jest konstruktor bezargumentowy
- aby wywołać inny konstruktor po definicji, a przed {} musimy napisać: this(...), np.
```csharp
public class Item
{
    private static int lastId = 0;
 
    private int id;
    private string name;
 
    public Item() : this(String.Empty)
    {
    }
 
    public Item(string name)
    {
        this.id = ++lastId;
        this.name = name;
    }
}
```
### Konstruktor statyczny 
- wywoływany nie więcej niż jeden raz w trakcie działania aplikacji
- wywoływany jest automatycznie - przed pierwszym użyciem danej klasy (przed utworzeniem instancji danej klasy lub po odwołaniu się do dowolnej statycznej zmiennej danej klasy)
- nie można przekazywać do nich żadnych argumentów
- brak modyfikatora dostępu
### Proces inicjalizacji
1. wypełnienie zerami (bądź ich odpowiednikami) statycznych pól
1. inicjalizatory statycznych pól (ważna kolejność, od góry do dołu)
1. statyczny konstruktor
1. wypełnienie zerami (bądź ich odpowiednikami) niestatycznych pol
1. inicjalizatory niestatycznych pól
1. statyczny konstruktor
```csharp
public class Test
{
    static Test()
    {
        Console.WriteLine("static constructor");
    }
    
    public Test()
    {
        Console.WriteLine("constructor");       
    }
    
    public static int ItemStatic = GetValue("static field");
    public int ItemField = GetValue("field");
    public int ItemProperty => GetValue("property");

    private static int GetValue(String msg)
    {
        Console.WriteLine(msg);
        return 1;
    }
}
```
```csharp
var test = new Test();
Console.WriteLine(test.ItemProperty);
Console.WriteLine(test.ItemField);
```
```
static field
static constructor
field
constructor
property
1
1
```
## Record
- wprowadzono w C# 9
- typ wartościowy
- skrót od `record class`
- niezmienny
- Od C# 10 można tworzyć `record struct`, tylko nie są one immutable. Aby było immutable, trzeba dodać `readonly`.
## Składowe
### Metody
#### ref
Przekazanie argumentu przez referencję.
#### out
Przekazanie argumentu przez referencję, ale obiekt musi zostać zainicjalizowany w ciele metody.
#### in
Przekazanie przez referencję, z tym zastrzeżeniem, że nie można nadpisać wartości. Użycie tego słowa kluczowego pozwala na uniknięcie tworzenia kopii. Należy używać wyłącznie w razie operowania na danych, które są większe od wskaźników. To właśnie z tego powodu nie warto go używać do przekazywania danych typu int. W procesach 32-bitowych referencje są 32-bitowymi wskaźnikami (w 64-bitowych są 64-bitowymi wskaźnikami). 
Stosowanie słow kluczoego `in` w miejscu wywołania jest opcjonalne.
Używać wyłącznie podczas korzystania z typów wartościowych przeznaczonych tylko do odczytu (readonly).
#### Rozszerzające (extension method)
- nowa składowa już istniejącego typu.
- metoda statyczna
- pierwszym argumentem jest klasa którą rozszerzamy z `this` na początku
### Właściwości
- zakamuflowana metoda
- Reprezentuje informację o obiekcie, a nie operację, jaką ten obiekt może wykonać. Zatem odczyt właściwości nie jest kosztowny i nie powinien mieć żadnych poważnych efektów ubocznych
#### Zmienne typu wartościowego
Można doporowadzić do sytuacji, w której nie jest modyfikownaa wartość, lecz jej kopia. 
```csharp
public class Foo
{
   private PointStruct _pointStruct;
   public ref PointStruct PointStruct => ref _pointStruct;
}

var foo = new Foo();
ref var point = ref foo.PointStruct;
point.X = 10;
```
### Destruktory
Specjalna metoda, która rozdziela typ na elementy składowe.
```csharp
public readonly struct Point
{
    public Point(int x, int y)
    {
        X = x;
        Y = y;
    }
    
    public int X { get; }
    public int Y { get; }
    
    public void Deconstruct(out int x, out int y)
    {
        x = X;
        y = Y;
    }
}

public static class Program 
{
    public static void Main() 
    {
        var point = new Point(10, 11);
        var (x, y) = point;
        Console.WriteLine($"X: {x}, Y: {y}");
    }
}
```
### Operatory
- zawiera słowo kluczowe `static`, typ zwracany, słowo kluczowe operator oraz jaki operator przeciążamy, następnie argumenty
- możemy definiować niestandardowe konwersje
    - explicit (jawne)
    - implicit (niejawne)
## Interfejsy
- deklaruje metody, właściwości, zdarzenia, ale nie określa ich implementacji, ani zawartości
- właściwości określają, czy należy utworzyć akcesory `get` i `set`
- implementacja jawna 
    - metody, właściwości, zdarzenia z interfejsu są prywatne
    - nie możemy się odwołać za pośrednictwem referencji do instancji danego typu - tylko za pośrednictwem wyrażenia typu interfejsu
podajemy nazwę interfejsu i po kropce nazwę składowej interfejsu
```csharp
public interface IDoStuff 
{
    int SomeMethod();
}

public class DoStuff: IDoStuff
{
    int IDoStuff.SomeMethod() => 1;
}
...
var doStuff = new DoStuff();
var iDoStuff = (IDooStuff)doStuff;
Console.WriteLine(iDoStruff.SomeMethod());
```
- Od C# 8 wprowadzono domyślną implementację metod w interfejsach. Zostało to dodane w celu zapewnienia wstecznej kompatybilności. Przykładowo do jakiegoś interfejsu zostanie dodana nowa metoda, to może to wywołać problemy w istniejącym kodzie, który korzysta z tego interfejsu.
## Typy anonimowe
```csharp
var x = new { Name = "Tomasz", Age = 5 };
```
- typ referencyjny
- dla każdego typu anonimowego kompilator generuje całkowicie normalną definicję 
- niezmienne, gdyż wszystkie właściwości pozwalają wyłącznie na odczyt
- Kompilator przesłania metodę `Equals` w taki sposób, że poszczególne instancje typu można porównywać na podstawie ich wartości. Co więcej, dostępna jest także implementacja metody `GetHashCode`.
### Krotki
- typ wartościowy
- nazwy właściwości są jedynie wygodną fikcją. `(X: 10, Y:20)` oraz `(W:10, H: 20)` są uważane za równoznaczne

# Typy ogólne
Typy generyczne pozwalają na opóźnienie w dostarczeniu specyfikacji typu danych w elementach takich jak klasy czy metody do momentu użycia ich w trakcie wykonywania programu. Innymi słowy, typy generyczne pozwalają na napisanie klasy lub metody, która może działać z niektórymi typami danych.
## Ograniczenia
-  musi implementować interfejs lub dziedziczyć z klasy - `where T : IComparable<T>` 
- konstruktora bezparametrowego - `where T : new()`
- typu referencyjnego - `where T : class`
- typu wartościowego - `where T : struct`
- typu notnull- argument będzie musiał być typem wartościowym, bądz referencyjnym, który nie akcetpuje wartości null - `where T: notnull`
- typu enum - `where T : Enum`


# Kolekcje
## Tablice
- dostęp do części metod poprzez statyczne metody z klasy `Array`, np. `Array.BinarySearch`, `Array.FindAll`, itp.
## IEnumerable\<T>
- posiada tylko metodę `GetEnumerator`
- tylko do odczytu, iterowania danych
- nie można dodawać, usuwać danych
- brak możliwości dostępu do konkretnego elementu za pomocą `[]`
- brak możliwości sprawdzenia liczby elementów (nie wliczając metody rozszerzającej `Count()`)
## IAsyncEnumerable\<T>
- dodany w .NET Core 3.0
- różnica między nim, a `IEnumerable<T>` polega na tym, że `IAsyncEnumerable<T>` zapewnia wsparcie dla operacji asynchronicznych
- nie są dostępna nieogólna wersja tego interfejsu
- można korzystać z niego przy użyciu wyspecjalizowanej wersji pętli `async foreach`
## ICollection\<T>
- rozszerza `IEnumerable<T>`
- możliwość dodawania i usuwania
- brak możliwości dostępu do konkretnego elementu za pomocą `[]`
- możliwość sprawdzenia liczby elementów - właściwość Count
## Collection\<T>
Klasa bazowa do budowy własnych typów kolekcji.
## IList\<T>
- rozszerza `ICollection<T>`
- możliwość dostępu do elementu za pomocą `[]`
## List\<T>
Przechowywane jako tablice, zapisane w jednym bloku pamięci. Dzięki takiemu rozwiązaniu zwyczajny dostęp do elementów tablicy jest bardzo wydajny, lecz również z jego powodu operacje wstawiania wiążą się z przesuwaniem elementów w celu utowrzeni amiejsca na nowe dane, a operacje usuwania także wymagają przesuwania elementów, by zlikwidować postwałą lukę.
## IQueryable<T>
- rozszerza `IEnumerable<T>`
- odpowiednie do zapytań bazodanowych, ponieważ filtracja danych odbywa się po stronie serwera bazy danych, a nie klienta
## IDictionary<TKey, TValue>
- rozszerza ICollection<KeyValuePair<TKey, TValue>>
## Dictionary<TKey, TValue>
todo 
## ISet<T>
todo
## Odwołanie do elementów z użyciem indeksów i zakresów
### System.Index
Operator `^` można umieszczać przed każdym wyrażeniem typu `int`. Generuje on wartość typu `System.Index`, służącą do reprezentowania położenia. W przypadku tworzenia indeksu przy użyciu operatora `^` określa on położenie względem końca. 
```csharp
var lastOld = numbers[number.Length - 1];
var lastNew = numbers[^1];
```
### System.Range
`Range` to jedynie para wartości typu `Index`, do których można się odwołać przy użyciu właściwości `Start` i `End`. Typ `Range` udostępnia konstruktor pobierający dwie wartości typu `Index`.
```csharp
var array = new int[] { 1, 2, 3, 4, 5 };
var slice1 = array[2..^3];    // array[new Range(2, new Index(3, fromEnd: true))]
var slice2 = array[..^3];     // array[Range.EndAt(new Index(3, fromEnd: true))]
var slice3 = array[2..];      // array[Range.StartAt(2)]
var slice4 = array[..];       // array[Range.All]
```

# Dziedziczenie
## Metody wirtualne
- przesłanianie metody
- sprawdzany jest przekazany obiekt i jeśli posiada on własną implementację metody wirtualnej, to będzie ona wywołana
- metoda jest wybierana na podstawie faktycznego typu obiektu docelowego, określanego w trakcie działania programu, a nie na podstawie statycznego typu (określanego podczas kompliacji)
- metody statyczne nie mogą być wirtualne
## Klasy abstrakcyjne
- dziedzicząc z klasy abstrakcyjnej musimy implementować wszystkie metody abstrakcyjne
- mogą implementować interfejs bez konieczności jego implementacji (wtedy metody z interfejsu są abstrakcyjne)
## Metody abstrakcyjne
- nie posiadają implementacji
- są wirtualne
- nie mogą być prywatne
## Metody new
- ukrywanie metody
```csharp
public class Student : Person
{
...
}

Person person = new Person();
Student student = new Student();
Person studentP = new Student();

// dla metody przesłoniętej (override)
Console.WriteLine(person.GetClassInfo()); // Person
Console.WriteLine(student.GetClassInfo()); // Student 
Console.WriteLine(studentP.GetClassInfo()); // Student

// dla metody ukrytej (new)
Console.WriteLine(person.GetClassInfo()); // Person
Console.WriteLine(student.GetClassInfo()); // Student 
Console.WriteLine(studentP.GetClassInfo()); // Person
```
## Proces inicjalizacji
1. pola klasy pochodnej
1. pola klasy bazowej
1. konstruktor klasy bazowej
1. konstruktor klasy pochodnej

# Zarządzanie pamięcią
## Garbage Collector
- Wszystkie obiekty, które trafiają do pamięci są umieszczane na stosie i/lub na stercie.
- Stos jest czyszczony gdy kończy się metoda. O to aby tak było dba CLR.
- Zarządzaniem stertą zajmuje się GC.
- GC uruchamia się w sytuacjach:
    - gdy kończy się pamięć na stercie do umieszczenia kolejnego obiektu 
    - gdy OS zgłasza mało pamięci
- GC kasuje obiekty, gdy nie ma do nich żadnych referencji.
- Referencja główna - jest to miejsce w pamięci, które może zawierać referencję, na pewno zostało zainicjowane i którego program będzie mógł użyć w przyszłości bez potrzeby korzystania z jakiejś innej referencji do obiektu.
### Działanie mechanizmu
1. Mechanizm określa do których obiektów można dotrzeć używając listy referencji głównych istniejących we wszystkich wątkach.
1. Sprawdza kolejno każdą referencję i jeśli jest ona różna od null, to uznaje, że obiekt, do którego się ona odwołuje jest osiągalny.
1. Dla każdego nowo odkrytego obiektu, mechanizm ten dodaje jego pola instancji typu referencyjnego do listy referencji, które trzeba sprawdzić, a następnie usuwa z niej ewentualnie powtórzenia.
1. Oznacza to, że jeśli obiekt jest osiągalny, to osiągalne są także wszystkie inne obiekty, których referencje są w nim przechowywane.
1. Mechanizm jest powtarzany tak długo, aż wyczerpie się lista referencji, które należy sprawdzić
jeśli obiekt do którego nie udało się dotrzeć w tym procesie, jest uznawany za nieosiągalny.

https://www.geeksforgeeks.org/garbage-collection-in-c-sharp-dot-net-framework/

## IDisposable
- wykorzystywany przy reprezentowaniu zasobów spoza CLR, takich jak:
    - połączenia z bazą danych
    - połączenia sieciowe
    - operacje wejścia/wyjścia na plikach
    - COM
    - DLL z c++
- metoda `Dispose()` jest uruchamiana po bloku `using`

# Delegaty
Obiekty udostępniające referencję do metody, przy czym może to być metoda statyczna jak i metoda instancji.
## Typy
- `bool Predicate<in T>(T obj)` - oznacza funkcję określającą, czy pewien warunek jest spełniony
- `void Action <in T>(T arg)`
- `TResult Func<out TResult, int T>(T arg)`

# Pytania
## Checked oraz unchecked
Słowa `unchecked` oraz `checked` służą do kontrolowania czy nie nastąpił overflow podczas operacji arytmetycznych. Wszystkie niepoprawne operacje w klauzuli `checked` wywołają wyjątek overflow, ponieważ podczas wykonywania obliczeń sprawdzane jest czy wynik wciąż się mieści w zmiennej
- `unchecked` często używa się do liczenia hashy `GetHashCode())`. 
- Ponadto `checked`\\`unchecked` dotyczy wyłącznie `int`\\`uint`. Zasięg dla `decimal` jest zawsze sprawdzany (nawet w `unchecked` – nie ma to znaczenia). Z kolei operacje na `float`\\`double` nie są nigdy sprawdzane, ponieważ istnieją tam wartości takie jak `Inf`, `Nan`.
## yield
- służy do "leniwego" dodawania do kolekcji
- poszczególne elementy dodawane są dopiero w momencie zgłoszenia na nie zapotrzebowania
- zamiast dodawać do listy, używamy `yield return` i element, który chcemy dodać do listy
- `yield break` - przerwanie działania metody
## lock
- za pomocą operatora `lock` blokujemy dostęp do dostarczonego obiektu
- jeśli drugi wątek będzie chciał w tym samym momencie wejść do obszaru lock, będzie musiał poczekać, aż pierwszy skończy wszelkie operacje zawarte w klauzuli lock
- nie powinno się używać referencji this oraz operatora lock
## Eager-loading
Wykonywanie czynności zaraz po jej wywołaniu. Przykład: mnożenie dwóch macierzy - obliczenia są od razu wykonywane.
## Lazy-loading
Czynności są wykonywane tylko gdy zajdzie taka potrzeba. Przykład: mnożenie dwóch macierzy - obliczenia są wykonywane dopiero gdy chcemy uzyskać dostępu do konkretnego elementu.