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
- int, long, float, double, byte, char, bool, DateTime, typy wyliczeniowe - enum, struktury
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
