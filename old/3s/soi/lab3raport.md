[..](../soi.md)

# Koncepcja do 3 zadania laboratoryjnego SOI
*Bartosz Nowak, 325201, 6.12.23*

## Informacje o zadaniu i założenia

Celem ćwiczenia trzeciego jest synchronizacja procesów z wykorzystaniem semaforów. Do obsługi mamy obszar krytyczny jakim jest magazyn, do którego producenci dostarczają towar, i konsumenci go odbierają. Założenia ćwiczenia:
* Magazyn reprezentowany jest jako plik z jego aktualną zasobnością
* $n$ konsumentów, każdy z nich jednorazowo odbiera partię towaru w liczbie sztuk ~ $U(a, b)$
* $m$ producentów, każdy z nich jednorazowo produkuje partię towaru w liczbie sztuk ~ $U(c, d)$ 
* Producenci przekazują towar konsumentom za pośrednictwem magazynu
* Producenci i konsumenci obsługują tylko całe partie towaru

Koncepcja zakłada realizację zadania zaleconą przez prowadzących, opartą o dziedziczenie sekcji krytycznej.

## Koncepcja

Koncepcja zadania zakłada użycie dwóch dodatkowych semaforów poza semaforem wzajemnego wykluczania (mutex)

Jeden semafor będzie odpowiedzialny za wpuszczanie do mutexa producentów, a drugi konsumentów.
Decyzja o przydzieleniu sekcji krytycznej będzie zatem zależała od aktualnego stanu magazynu.

Ostatecznie algorytm zaimplementowany będzie w języku ``C++``. Na potrzeby koncepcji algorytm konsumenta oraz producenta zostały zaimplementowane w języku ``Python``; głównie ze względu na czytelność kodu.

Producent i konsument zawierają metody odczytujące z pliku ``warehouse.txt`` stan magazynu, oraz metodę dodającą lub usuwającą konkretną ilość towaru z magazynu.
Po zarówno udanej lub nieudanej próbie dodania/zabrania towaru, producent/konsument będzie podejmował decyzję o dalszym działaniu i w zależności od stanu magazynu oraz powodzenia operacji na magazynie zadecyduje o tym, czy wrócić do mutexa lub przejść do kolejki przed semaforem producentów/konsumentów oraz czy przepuścić kolejnego konsumenta/producenta do mutexa.

Poniżej zamieszczony został kod źródłowy algorytmu producenta:

## Algorytm producenta

```python
import random

MAX_PRODUCE = 10
WAREHOUSE_PATH = "warehouse.txt"

class Producer:

    def __init__(self):
        self.produce = 0
        self.last_success = False

    def extractProducedNum(self) -> int:
        with open(WAREHOUSE_PATH) as fp:
            contents = fp.read()
        return int(contents)

    def generateRandomProduce(self, c: int, d: int) -> int:
        return random.randint(c, d)

    def insertIntoWarehouse(self) -> bool:

        if not self.produce:  # c++ equivalent: (produce == 0)
            self.produce = self.generateRandomProduce(3, 6)

        current_num = self.extractProducedNum()
        if (current_num + self.produce > MAX_PRODUCE):
            self.last_success = False
            return False

        with open(WAREHOUSE_PATH, 'w') as fp:
            fp.write(str(current_num + self.produce))
        
        self.produce = 0
        self.last_success = True
        return True
    
    def decideNextMove(self):
        current_num = self.extractProducedNum()
        if current_num > 5 or not self.last_success:
            self.LET_IN_CUSTOMER_FROM_SEMAPHORE_TO_MUTEX()

        if self.last_success:
            self.RETURN_SELF_TO_PRODUCERS_SEMAPHORE()
        else:
            self.ADD_SELF_TO_MUTEX()
```

## Algorytm konsumenta

```python
import random

EMPTY_WAREHOUSE = 0
WAREHOUSE_PATH = "warehouse.txt"  # placeholder for modeling purpose

class Consumer:

    def __init__(self):
        self.demand = 0
        self.last_success = False

    def extractProducedNum(self) -> int:
        with open(WAREHOUSE_PATH) as fp:
            contents = fp.read()
        return int(contents)

    def generateRandomDemand(self, a: int, b: int) -> int:
        return random.randint(a, b)

    def takeFromWarehouse(self) -> bool:

        if not self.demand:
            self.demand = self.generateRandomDemand(3, 6)

        current_num = self.extractProducedNum()
        if (current_num - self.demand < EMPTY_WAREHOUSE):
            self.last_success = False
            return False

        with open(WAREHOUSE_PATH, 'w') as fp:
            fp.write(str(current_num - self.demand))

        self.demand = 0
        self.last_success = True
        return True

    def decideNextMove(self):
        current_num = self.extractProducedNum()
        if current_num < 5 or not self.last_success:
            self.LET_IN_PRODUCER_FROM_SEMAPHORE_TO_MUTEX()

        if self.last_success:
            self.RETURN_SELF_TO_CONSUMERS_SEMAPHORE()
        else:
            self.ADD_SELF_TO_MUTEX()
```

## Testy

### Sytuacja 1
*Testowanie powodzenia operacji konsumenta i producenta*

**Magazyn:** 4

**Mutex:** (Konsument 1) (Producent 1)

**Producenci:** (Producent 2) (Producent 3)

**Konsumenci:** (Konsument 2) (Konsument 3)

1. Magazyn jest pusty, mutex wpuszcza konsumenta.
2. Konsument losuje 3, i wyciąga taką ilość towaru z magazynu
3. Konsument decyduje o dalszej czynności:
    * Magazyn zawiera mniej niż połowę możliwych towarów.
    * Przepuść Producenta do mutexa.
    * Udało mi się odebrać produkt. Przejdź na koniec kolejki konsumentów.
4. (Producent 2) wchodzi do mutexa.
5. Magazyn jest pusty, mutex wpuszcza Producenta 1
6. Producent produkuje 6, dodaje taką ilość do magazynu.
7. Producent decyduje o dalszej czynności, wpuszcza konsumenta i przechodzi na koniec kolejki producentów

## Sytuacja 2
*Testowanie niepowodzenia operacji producenta*

**Magazyn**: 7

**Mutex**: (Producent 2) (Konsument 2)

**Producenci**: (Producent 3) (Producent 1)

**Konsumenci**: (Konsument 3) (Konsument 1)

1. (Producent 2) wchodzi do magazynu
2. (Producent 2) losuje 4, nie jest w stanie dodać takiej ilości towaru do magazynu.
3. Producent podejmuje decyzję.
    * W magazynie jest więcej niż połowa towarów.
    * Przepuść konsumenta do mutexa.
    * Nie udało mi się przechować towaru, przejdź do końca mutexa

**Magazyn**: 7
**Mutex**: (Konsument 2) (Konsument 3) (Producent 2 (oddaje: 4))

W tej sytuacji na końcu mutexa przechowywany jest Producent, który ma już wylosowaną ilość towaru do oddania, ponowi on próbę, kiedy przyjdzie na niego kolej.

## Sytuacja 3
*Testowanie niepowodzenia operacji konsumenta*

**Magazyn**: 2

**Mutex**: (Konsument 3) (Producent 2 (oddaje: 4)) (Producent 3)

**Producenci**: (Producent 1)

**Konsumenci**: (Konsument 1) (Konsument 2)

1. (Konsument 3) wchodzi do magazynu.
2. (Konsument 3) losuje 3, nie jest w stanie dodać takiej ilości towaru do magazynu.
3. Konsument podejmuje decyzję
    * W magazynie jest mniej niż połowa towarów
    * Przepuść producenta do magazynu
    * Przejdź na koniec mutexa

Możemy szybko zauważyć problem w dobranej heurystyce, w ramach jednej z decyzji przepuszczamy do mutexa producenta/konsumenta jednocześnie samemu w niej pozostając, co po odpowiednio wysokiej liczbie iteracji może prowadzić do opróżnienia kolejek do semafora producenta i konsumenta.

Pokazuje to jak ważne jest odpowiednie dobranie heurystyki, w celu uzyskania zadowalająco optymalnych wyników.

## Wnioski oraz potencjalne problemy w implementacji i ich potencjalne rozwiązania.

W celu zrealizowania zadania w optymalny sposób, potrzebne będzie dobranie odpowiedniej heurystyki, aby uniknąć zawieszenia wątków lub opróżnenia kolejek przed którymś z semaforów.

Przykładowym rozwiązaniem problemu opisanego po trzeciej sytuacji testowej jest zmiana wartości do której porównujemy ilość towaru w magazynie, w powyższym algorytmie jest to połowa, lecz warto równieżrozważyć wartości takie jak 60% oraz 70%

Innym rozwiązaniem dla powyższego algorytmu jest dodanie pola ``self.delayed`` do klas konsumenta oraz producenta. Pole to przyjmie wartość ``True``, jeśli konsument wróci do mutexa przynajmniej raz. W takiej sytuacji, po wykonaniu pomyślnej operacji w magazynie, producent/konsument wróci do odpowiedniej kolejki bez wpuszczania nikogo na jego miejsce, nowy kod metody ``decideNextMove(self)`` będzie wyglądał następująco.

```py
def decideNextMove(self):
    current_num = self.extractProducedNum()
    if current_num < 5 or not self.last_success or not self.delayed:
        self.LET_IN_PRODUCER_FROM_SEMAPHORE_TO_MUTEX()

    if self.last_success:
        self.RETURN_SELF_TO_CONSUMERS_SEMAPHORE()
        self.delayed = False
    else:
        self.ADD_SELF_TO_MUTEX()
        self.delayed = True
```

