# Dziedziczenie
## Table-per-hierarchy (TPH)
Domyślnie mapowanie dziedziczenia. Używa jednej tabeli do przechowywania danych dla wszystkich typów w hierarchii, a jedna kolumna służy do identyfikowania typu, który reprezentuje każdy wiersz.

Powyższy model jest mapowany na następujący schemat bazy danych (zwróć uwagę na niejawnie utworzoną kolumnę Discriminator, która identyfikuje typ bloga przechowywany w każdym wierszu).

## Table-per-type (TPT)
Wszystkie typy są mapowane do poszczególnych tabel. Właściwości, które należą wyłącznie do typu podstawowego lub typu pochodnego, są przechowywane w tabeli, która mapuje do tego typu. Tabele mapowane na typy pochodne przechowują również klucz obcy, który łączy tabelę pochodną z tabelą podstawową.

## Wydajność
TPH jest zazwyczaj szybsze. Wynika to z tego, że w TPT musimy wykonać joiny.

https://docs.microsoft.com/en-us/ef/core/performance/modeling-for-performance#inheritance-mapping