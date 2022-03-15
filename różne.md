# REST
- Jednolity interfejs – dla realizacji konkretnego działania jest tylko jedna droga dojścia.
- Bezstanowość – bez względu na to jakie wcześniej operacje zostały wykonane, to zawsze każda kolejna operacja nie będzie zmieniała rezultatu dla innej.
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
