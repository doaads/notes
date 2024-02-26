[..](../soi.md)

# Koncepcja 2. zadania laboratoryjnego SOI

### Bartosz Nowak, 325201

Celem zadania drugiego jest implementacja algorytmu szeregowania w systemie operacyjnym MINIX.

Mamy do dyspozycji procesy o trzech priorytetach:

- Inter. - oznaczany dalej jako priorytet 0
- Obl. - oznaczany dalej jako priorytet 1
- Wejścia/Wyjścia - oznaczany dalej jako priorytet 2

Każdy z priorytetów powinien otrzymywać następującą ilość czasu procesora:

- 2 - $T_2 = t$
- 1 - $T_1 = 2t$
- 0 - $T_0 = 4t$

Gdzie $t$ wyrażone jest w jednostce czasu

Model algorytmu przedstawiony został w języku **Python**. Algorytm będzie wybierał proces, któremu zostanie przydzielony czas procesora.

Impuls zegara jest odzwierciedlony poprzez pojedynczą iteracje, w której wybierany jest procs o odpowiednim priorytecie.

> W celu przejrzystości kodu algorytm zamodelowany został w klasie SchedulingAlgorithm

```python
class SchedulingAlgorithm:

    def __init__(self, impulses: int, int_num: int, comp_num: int, io_num: int):

        self.starting_impulses = impulses

        # create the model proc list
        self.define_procs(int_num, comp_num, io_num)

    def define_procs(self, int_num: int, comp_num: int, io_num: int):
        int_procs = [0] * int_num
        comp_procs = [0] * comp_num
        io_procs = [0] * io_num
        self.nums = [int_num, comp_num, io_num]
        self._procs = [int_procs, comp_procs, io_procs]

    @property
    def procs(self) -> list:
        return self._procs
```

W tym przypadku w klasie SchedulingAlgorithm definiowany jest model, dla którego następnie metoda ``schedule`` wykona szeregowanie procesów. Definiowana jest wartość "impulsów procesora" przez które algorytm ma się wykonywać oraz tworzona tablica reprezentująca liczbę wybrań poszczególnych procesów. Jest to tablica dwuwymiarowa, dla której pierwszy indeks będzie równy priorytetowi, a drugi indeksowi procesu.

## Algorytm

Sam algorytm szeregowania zaimplementowany został w następujący sposób

```python
def schedule(self) -> list:
    """
    Scheduling algorithm
    """
    
    # define all variables used by the algorithm
    iter_end = self.starting_impulses
    choice_iter = 0

    # lists will have a fixed length to act like static data types
    iterators = [0, 0, 0]
    results = [0, 0, 0]
    choice = [0, 1, 0, 2, 0, 1, 0]
    
    # condition equivalent to (iter_end == 0)
    while iter_end:
        # check if any processes with next priority exist.
        starting_iter = choice_iter
        while not self.procs[choice[choice_iter]]:
            choice_iter += 1
            if choice_iter >= len(choice):
                choice_iter = 0
            # this checks if no processes of any priority exist (to avoid infinite loops)
            if choice_iter == starting_iter:
                return results

        # decide which priority will be chosen from choice list
        priority = choice[choice_iter]

        # increment the results
        self.procs[priority][iterators[priority]] += 1
        results[priority] += 1

        # increment iterators
        iterators[priority] += 1
        choice_iter += 1

        print(f'Priority {priority} chosen, process num {iterators[priority]}')

        # check if iterators reached the end of a list, if so, reset
        if iterators[priority] == self.nums[priority]:
            iterators[priority] = 0
        if choice_iter >= len(choice):
            choice_iter = 0

        iter_end -= 1
    return results
```

**Gdzie poszczególne zmienne oznaczają**:
- ``iterators`` - iteratory dla każdego priorytetu równe ilości każdego z procesów
- ``results`` - liczba wybrań każdego z priorytetów
- ``iter_end, choice_iter`` iterator zegara, iterator dla tablicy choice
- ``choice`` - tablica reprezentująca częstotliwość wyboru każdego z priorytetów. Priorytet 0 występuje w niej 4-krotnie, 1 2-krotnie, i 2 tylko raz. Ma to odzwierciedlać wartości $T_0, T_1 i T_2$

Metoda ``schedule`` zwraca trójwartościową tablicę zawierającą ilość wybrań każdego z priorytetów.

## Testy

Przygotowany został również krótki program testowy zwracające wyniki zbliżone do tych, wymaganych w treści zadania, jednak dostosowanych do modelu i zastępujacy czas wykonania 1-go procesu z każdej grupy ilością wybrań poszczególnych procesów w formie listy.

> Przykładowy wynik wykonania programu dla 7 iteracji, 5 procesów iter, 3 procesów obl, i 1 procesu we/wy

![](img/2023-11-22-15-57-44.png)

W przypadku, gdy nie utworzone zostaną żadne procesy o danym priorytecie, algorytm nie będzie go wybierał i zadana liczba iteracji zostanie rozłożona między pozostałe procesy:

> 5 iteracji, 1 proces iter, 1 proces we/wy

![](img/2023-11-22-16-52-47.png)

> 4 iteracje, 2 procesy iter, 0 pozostałych

![](img/2023-11-22-16-53-35.png)

Gdy nie utworzymy żadnych procesów algorytm zatrzyma się.

![](img/2023-11-22-16-55-27.png)

Ten przypadek został zaimplementowany na potrzebę modelu, aby uniknąć nieskończonych pętli (zakładam, że w systemie MINIX nie wystąpi taka sytuacja)

### Pozostałe testy

Poniżej zamieszczone są wyniki pozostałych testów algorytmu, wraz z liczbą poszczególnych typów procesów oraz ilością wybrań każdego z nich przez algorytm. Możemy zauważyc, że zadana proporcja czasów procesora jest zachowana.

| iteracje | l. inter | l. obl | l. we/wy | wybr. inter. | wybr. obl. | wybr. we/wy |
| ----| ---- | ---- | ---- | ---- | ---- | ---- |
| 140 | 4 | 6 | 5 | 80 | 40 | 20 |
| 230 | 10 | 20 | 30 | 131 | 66 | 33 |
| 1000 | 1 | 2 | 3 | 571 | 286 | 143 | 
| 700 | 0 | 0 | 3 | 0 | 0 | 700 |
| 700 | 5 | 0 | 3 | 560 | 0 | 140 |
| 700 | 5 | 3 | 0 | 467 | 233 | 0 |
| 100 | 1 | 1 | 1 | 57 | 29 | 14 |
| 3 | 1 | 1 | 1 | 2 | 1 | 0 |
