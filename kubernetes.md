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

## Depolyment
Służy do definiowania sposobu wdrażania podów: jakiego obrazu powinny używać, liczby uruchomionych replik, zużywanych zasobów, sposobu wdrożenia nowej wersji itp. Reprezentują pożądany stan klastra.

## Service
Opisuje sposób komunikacji pomiędzy podami.

### ClusterIP
Daje dostęp do serwisu wewnątrz klastra.

### NodePort
Daje dostęp do node po wyspecyfikowanym porcie i następnie przekierowywuje to do portu serwisu.

### LoadBalancer
Daje dostęp do serwisu z zewnątrz. 

## Ingress
Eksponuje HTTP i HTTPS z zewnątrz klastra do serwisów wewnątrz klastra. Działa jako reverse proxy usług, równoważąc żądania między różnymi usługami działającymi na różnych węzłach.