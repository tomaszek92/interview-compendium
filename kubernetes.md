# Co to jest Kubernetes?
Jest to open-source platforma do zarządzania (orkiestrowania) kontenerami (zazwyczaj Dockerowymi). Zarządza cyklem życia i komunikacją pomiędzy kontenerami. Dzięki temu nie musimy się obawiać o sytuację jak aplikacji przestanie działać.

# Komponenty

## Cluster
To zestaw węzłów (node'ów), w których działają aplikacje umieszczone w kontenerach. Klastry umożliwia łatwiejsze tworzenie, przenoszenie i zarządzanie aplikacjami. Klastry umożliwiają działanie kontenerów na wielu maszynach i środowiskach: wirtualnych, fizycznych, w chmurze i lokalnych. Klastry składają się z jednego węzła głównego i wielu węzłów roboczych. Te węzły mogą być komputerami fizycznymi lub maszynami wirtualnymi, w zależności od klastra.

## Node
Węzeł może być maszyną wirtualną lub fizyczną, w zależności od klastra.
Wyrówżniamy dwa typu węzłów:
- główny (*master*) -  zawiera wszystkie usługi *control plane* wymagane do uruchomienia klastra. Zazwyczaj węzeł główny obsługuje tylko dostęp do zarządzania i nie uruchamia żadnych aplikacji kontenerowych.
- robczy (*worker*) - są używane do uruchamiania podów.

## Pod
Pod to mała i proste jednostka, która działa jako pojedyncza instancja aplikacji. Może pomieścić jeden kontener lub wiele kontenerów. Zazwyczaj pod zawiera jeden kontener.

### `restartPolicy`
Dotyczy wszystkich kontenerów w podzie. Domyślna wartość to `Always`.

#### Always
Kontenery będą restartowane zawsze, nieważne z jakim kodem zakończyły działanie. Może to być wykorzystwane w przypadku serwera www, który powinien diałać zawsze.
#### OnFailure
Kontenery będą restartowane tylko w przypadku zakończenia działania błędem.
#### Never
Kontenery nie będą restartowane w ogóle.

### `resources`
Gdy określisz `request` zasobu dla kontenerów w podu, Kubernetes użyje tych informacji, aby zdecydować, w którym węźle umieścić pod. Gdy określisz `limit` zasobów dla kontenera, Kubernetes wymusza te limity, aby uruchomiony kontener nie mógł używać więcej tego zasobu niż ustawiony limit. Kubernetes rezerwuje również co najmniej żądaną ilość tego zasobu systemowego specjalnie do użycia przez ten kontener.

Jeśli podasz limit dla kontenera i nie podasz początkowych wartości, to Kubernetes skopiuje wartości z limitu do żądania.

Przykład:
```yaml
resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"
```

#### CPU
Limity i żądania zasobów procesora są mierzone w jednostkach procesora. W Kubernetes 1 jednostka procesora odpowiada 1 fizycznemu rdzeniowi procesora lub 1 rdzeniowi wirtualnemu.

Wartością może być ułamek. Przykładowo, jeśli zdefiniujesz `spec.containers[].resources.requests.cpu` równe `0.5`, to żądasz o połowę mniej czasu procesora w porównaniu do tego, gdybyś prosił o `1.0`. W przypadku jednostek zasobów procesora wyrażenie `0.1` jest równoważne wyrażeniu `100m`, które można odczytać jako „sto millicpu”.

Zasób procesora jest zawsze określany jako bezwzględna ilość zasobu, nigdy jako ilość względna. Nie ma znaczenia, czy działa to na procesorze z jednym rdzeniem, czy z czterema.

#### Pamięć
Limity i żądania pamięci są mierzone w bajtach. Pamięć można wyrazić jako liczbę całkowitą lub liczbę stałoprzecinkową, używając jednego z następujących sufiksów ilościowych: `E`, `P`, `T`, `G`, `M`, `k`. Możesz również użyć potęgi dwójki: `Ei`, `Pi`, `Ti`, `Gi`, `Mi`, `Ki`. 
Na przykład poniższe reprezentują mniej więcej tę samą wartość: 
```
128974848, 129e6, 129M, 128974848000m, 123Mi
```

### Status
#### Pending
Pod został zaakceptowany przez klaster, ale kontenery nie są jeszcze utworzone i gotowe do działania. Obejmuje to czas, który pod czeka na zaplanowanie, a także czas poświęcony na pobieranie obrazów kontenerów przez sieć.
#### Running
Pod został powiązany z węzłem i wszystkie kontenery zostały utworzone. Co najmniej jeden kontener nadal działa lub jest w trakcie uruchamiania lub ponownego uruchamiania.
#### Succeed
Wszystkie kontenery w podzie zakończyły działanie z sukcesem i nie będą restartowane.
#### Failed
Wszystkie kontenery w podzie zostały zakończone, a co najmniej jeden kontener zakończył działanie niepowodzeniem.
#### Unkown
Z jakiegoś powodu nie można było uzyskać stanu poda. Ta faza zwykle występuje z powodu błędu w komunikacji z węzłem, w którym powinien działać pod.

## Depolyment
Służy do definiowania sposobu wdrażania podów: jakiego obrazu powinny używać, liczby uruchomionych replik, zużywanych zasobów, sposobu wdrożenia nowej wersji itp. Reprezentują pożądany stan klastra.

Przykład:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  strategy: 
    type: RollingUpdate
    rollingUpdate:
      maxUnavailable: 1
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```
### `apiVerion`
Wskazuje, dla której wersji Kubernetes API jest to zdefiniowane.

### `metadata`
Definiuje szczegóły takie jak nazwa zasobu, a także etykiety.

#### `labels`
Etykiety to para klucz wartość, które są poziązan z zasobem. Mają głównie charakter informacyjny.

Etykiety mogą być przydatne, gdy chcesz użyć wiersza polecenia. Na przykład możesz wyświetlić listę wszystkich podów, które mają zarówno etykietę `frontend` oraz `staging`
```console
kubectl get pods -l environment=staging,system=frontend
```
Etykiety są one również używane przez Kubernetes wszędzie tam, gdzie zasób musi odwoływać się do innych zasobów. Na przykład opisany wcześniej depoloyment musi wiedzieć, jak rozpoznać zarządzane przez niego pody. Robi to poprzez zdefiniowanie `selector`.

Selektor definiuje szereg warunków, które muszą być spełnione, aby obiekt został uznany za pasujący. 
```yaml
selector:
  matchLabels:
    app: nginx
```
Na przykład powyższa sekcja selektora będzie pasować do wszystkich podów, które mają etykietę `app` i mają wartość `nginx`. Jeśli jest zdefiniowanych kilka warunków, to wszystkie muszą być spełnione.

Deployment nie jest jedynym zasobem korzystającym z selektorów. Serwisy używają tego samego mechanizmu do definiowania, do których podów przekazują ruch.

### `replicas`
Definiuje jak wiele instacji poda powinno być stworzonych. Domyślna wartość to 1.

### `strategy`
Określa strategię do zamiany starych podów przez nowe. `rollingUpdate` jest domyślną wartością.

#### rollingUpdate
`maxUnavailable `- określa ile maksymalnie podów może być niedostępnych podczas procesu aktualizacji. Nie może to być 0. Może to być liczba całkowita lub procent (wtedy liczba jest wyliczna zaokroglając w dół). Domyślnie to 25%.

Na przykład, gdy ta wartość jest ustawiona na 30%, stary zestaw ReplicaSet może przeskalować w dół do 70% żądanych podów natychmiast po rozpoczęciu aktualizacji. Gdy nowe pody są gotowe, stary ReplicaSet może dalej skalować w dół, a następnie skalować nowy ReplicaSet, zapewniając, że całkowita liczba podów dostępnych przez cały czas podczas aktualizacji wynosi co najmniej 70% żądanych podów.

`maxSurge`- określa ile maksymalnie podów, które moją być stworzone ponad pożądaną liczbę podów. Nie może to być 0. Może to być liczba całkowita lub procent (wtedy liczba jest wyliczna zaokroglając w górę). Domyślnie to 25%.

Na przykład, gdy ta wartość jest ustawiona na 30%, nowy zestaw ReplicaSet może być skalowany w górę natychmiast po rozpoczęciu aktualizacji, tak aby całkowita liczba starych i nowych podów nie przekraczała 130% żądanych podów. Gdy stare pody zostaną zabite, nowy zestaw ReplicaSet może dalej skalować, zapewniając, że całkowita liczba podów uruchomionych w dowolnym momencie podczas aktualizacji wynosi maksymalnie 130% pożądanych podów

#### recreate
Wszystkie pody są ubijane przed stworzeniem nowych.

### Sondy (*probe*)
#### Startup
Służy to wykrywania czy aplikacja jest uruchomiona. Jeśli tylko zostanie zwrócny sukces, to Kubernetes zaczyna używać liveness to zidentyfikowania czy aplikacja żyje.

Jest to pierwszy probe, który jest uruchamiany. Kiedy aplikacja startuje może być potrzeba wykonania dużej ilości pracy. Podczas tego procesu, aplikacja nie powinna przyjmować zapyta. Kiedy tylko aplikacja wystartuje i startup probe zwróci sukces, to już więcej nie wywołuje ten sondy. Jeśli startup nigdy nie zwróci sukcesu, to Kubernetes ubija kontener i podlega polityce restartu poda.

Przykład:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-app-api-deployment
spec:
  template:
    metadata:
      labels:
        app: test-app-api
    spec:
      containers:
      - name: test-app-api
        image: andrewlock/my-test-api:0.1.1
        startupProbe:
          httpGet:
            path: /health/startup
            port: 80
          failureThreshold: 30
          periodSeconds: 10
```
Sonda jest zdefiniowana w `startupProbe` i wywołuje adres URL `/health/startup` na porcie `80`. Sonda powinna zostać wypróbowana 30 razy przed niepowodzeniem, z 10-sekundowym okresem oczekiwania pomiędzy sprawdzaniami. W tym przykładzie, maksymalny czas oczekiwania na uruchomienie się kontenera to 300 sekund.

#### Livness
Słłuży do wykrywania czy aplikacja działa. Jeśli zwróci błąd, to Kubernester zatrzyma kontener i stworzy nowy.

Przykład:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-app-api-deployment
spec:
  template:
    metadata:
      labels:
        app: test-app-api
    spec:
      containers:
      - name: test-app-api
        image: andrewlock/my-test-api:0.1.1
        livenessProbe:
          httpGet:
            path: /healthz
            port: 80
          initialDelaySeconds: 0
          periodSeconds: 10
          timeoutSeconds: 1
          failureThreshold: 3
```
Sonda jest zdefiniowana w `livenessProbe` i wywołuje adres URL `/healthz` na porcie `80`. 

#### Readiness
Służy do wykrywania czy aplikacja jest gotowa do przyjmowania zapytań. Jeśli zwróci błąd, to Kubernetes zostawi działający kontener, ale nie będzie wysyłał do niego zapytań.

Przykład:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-app-api-deployment
spec:
  template:
    metadata:
      labels:
        app: test-app-api
    spec:
      containers:
      - name: test-app-api
        image: andrewlock/my-test-api:0.1.1
        readinessProbe:
          httpGet:
            path: /ready
            port: 80
          successThreshold: 3
```
Sonda jest zdefiniowana w `redinessProbe` i wywołuje adres URL `/ready` na porcie `80`. 

#### Konfiguracja
- `initialDelaySeconds`- liczba sekund po uruchomieniu kontenera przed zainicjowaniem sond. Domyślnie 0 sekund. Minimalna wartość to 0. 
- `periodSeconds`- jak często (w sekundach) przeprowadzać sondowanie. Domyślnie 10 sekund. Minimalna wartość to 1. 
- `timeoutSeconds`- liczba sekund, po których następuje timeout. Domyślnie 1 sekunda. Minimalna wartość to 1. 
- `successThreshold`- minimalna liczba kolejnych sukcesów, aby sonda została uznana za udaną po niepowodzeniu. Wartością domyślną jest 1. Musi być 1 dla liveness i startup. Minimalna wartość to 1. 
- `failureThreshold`- gdy sonda nie powiedzie się, Kubernetes spróbuje tyle razy przed poddaniem się. Domyślnie 3. Minimalna wartość to 1.

#### Cykl sond
![image](/assets/kubernetes/probes.svg)

> Zazwyczaj sondowanie odbywa się przy pomocy HTTP. Jeśli endpoint zwraca kod od 200 do 399, to jest sukces. Cokolwiek innego jest traktowane jako błąd. Jest możliwość na używanie również TCP czy gRPC.

#### Jak używac sond?
1. Używaj smart startup- sprawdzanie czy są gotowe połączenia do bazy/kolejki czy wszystkie zadania do uruchomienia aplikacji zakończyły się
1. Używaj dumb liveness- sprawdzenie powinno być szybkie, bo jest często wywoływane.
1. Używaj smart/dumb readiness- sprawdzenie powinno być w miarę szybkie, przykładowo czy nadal istnieje połaczenie do bazy/kolejki.

## Service
Opisuje sposób komunikacji pomiędzy podami. Pod może działać lub może być zatrzymany. W związku z tym zamiast robić zapytania do poda, można zrobić to do serwisu, który przekieruje to do powiązanych podów.

Przykład:
```yaml
apiVersion: v1
kind: Service
metadata:
  name: my-shop-backend
  labels: 
    app: my-shop
    system: backend
spec:
  type: ClusterIP
  selector:
    app: my-shop
    system: backend
  ports:
  - port: 80
    targetPort: 8080
    protocol: TCP
```

### ClusterIP
Daje dostęp do serwisu wewnątrz klastra.

### NodePort
Daje dostęp do node po wyspecyfikowanym porcie i następnie przekierowywuje to do portu serwisu.

### LoadBalancer
Daje dostęp do serwisu z zewnątrz używając chumorowego load balancera dostawcy. 

![image](./assets/kubernetes/services.jpg)

## ConfigMap
Pozwala na przechowywanie konfiguracji (para klucz wartość). Następnie tych wartości można używać w podach jako konkretne klucze (wybierając które) lub całe zbiory. Nazwa config mapy musi być zgodna z nazwą subdomeny DNS.

Przykład:
```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: game-demo
data:
  player_initial_lives: "3"
  ui_properties_file_name: "user-interface.properties" 
```

## PersistentVolumeClaim
Po usunięciu czy restarcie poda, wszystkie informacja na nim zapisane są tracone. `PersistentVolume` umożliwia zachowanie informacji.

Przykład:
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

### Tryby dostępu
- `ReadWriteOnce` - wolumen może być zamontowany jako do odczytu i zapisu przez jeden węzeł
- `ReadOnlyMany` - wolumen może być zamontowany jako do odczytu przez wiele węzłów
- `ReadWriteMany` - wolumen może być zamontowany jako do odczytu i zapis przez wiele węzłów

## Ingress
Pozwala światu zewnętrznemu na dostęp do zasobów klastra. Dla przykładu każdy naszych deploymentów może mieć serwis typu LoadBalancer która udostępnia adres IP na zewnątrz i zarządza dostępem do podów. Jednak taki LoadBalancer wymaga adresu by się do niego dostać. Weźmy 5-6 takich mikroserwisów z LoadBalancerem i mamy 5-6 adresów API do zarządzania.

Przykład:
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: example-ingress
  annotations:
    ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
        - path: /apple
          backend:
            serviceName: apple-service
            servicePort: 5678
        - path: /banana
          backend:
            serviceName: banana-service
            servicePort: 5678
```