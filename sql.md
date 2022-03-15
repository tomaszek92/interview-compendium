# Partycjonowanie
Partycjonowanie tabel polega na tym, że fizycznie dane rozkładane są do wielu grup plików, a logicznie, dla użytkownika, pozostają widoczne jakby wchodziły w skład jednej tylko tabeli. Takie rozwiązanie ma wiele zalet:
- Jeżeli grupy plików znajdują się na różnych dyskach (z dedykowanymi kontrolerami!), to odczyt z takiej tabeli odbywa się jednocześnie równolegle z każdego z tych dysków.
- Jeżeli użytkownik uruchomi transakcję blokującą wiele rekordów w ramach jednej tylko partycji, to serwer może zdecydować o zablokowaniu tylko tej jednej partycji zamiast całej tabeli (do czego by mogło dojść, gdyby tabela nie była partycjonowana)
- Można „przełożyć” partycję z jednej tabeli do innej niepartycjonowanej lub partycjonowanej tabeli bez kopiowania rekordów tej tabeli. Kopiowanie polega po prostu na podpięciu tej partycji z jednej tabeli do innej.

Partycjonowanie opiera się na dwóch obiektach:
- Funkcji partycjonującej, która patrzy na dane wpisywane do tabeli i na podstawie tych danych rozstrzyga, czy rekord należy zapisać do jednej czy do innej partycji. Funkcja przyjmuje określony typem parametr i zwraca numer partycji. Funkcje partycjonujące są bardzo miłe do napisania, bo składają się po prostu z listy granicznych wartości, które rozdzielają partycje. Przykładowo lista składa się z pierwszego dnia miesiąca stycznia 2008 i lutego 2008 roku. Pytanie tylko, czy data 2008-01-01 (czyli graniczna data) ma należeć do pierwszej czy do drugiej partycji!? Zależy to od słowa `LEFT` lub `RIGHT` w definicji funkcji. Jeżeli napisałeś `RIGHT`, to data graniczna należy do przedziału na prawo, czyli 2008-01-01 należy do partycji drugiej, zaś każda data wcześniejsza do partycji pierwszej. 
    ```sql
    CREATE PARTITION FUNCTION MyPF(DATETIME)
    AS
    RANGE RIGHT FOR VALUES('2008-01-01','2008-02-01')
    GO
    ```
- Schematu partycjonowania, który jest równie prosty jak funkcja partycjonująca, bo składa się z listy grup plików na których  mają być składowane partycje. Schemat partycjonowania wiąże jednocześnie się z funkcją, która będzie ustalać numer partycji dla danego rekordu.
    ```sql
    CREATE PARTITION SCHEME PartitionBymonth
    AS PARTITION PartitioningBymonth
    TO (January, February, March, April, May, June, July, August, September, October, November, December);
    ```