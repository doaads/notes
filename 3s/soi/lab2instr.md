[..](../soi.md)

### Zadanie

Adam Krzemienowski, adam.krzemienowski@pw.edu.pl
kopia raportu:      bartosz.kozlowski@pw.edu.pl

> Koncepcja na 24.11, 10:15 (pdf)

ZEGAR ---> impulsy co $\Delta t$, $\Delta t$ = const
   |
algorytm
szeregowania

> Procesy I/O

> Procesy obliczeniowe - 2 razy więcej czasu niż procesy I/O

> Procesy inter. - 2 razy więcej czasu niż procesy obliczeniowe

testowanie:

```c
time ./int &time ./obl &time ./wewy
ps
kill PID1; kill PID2; kill PID3
```

> Przykładowe wykonanie

```
Czas wykonania wszystkich procesów:     7s
Czas wykonania procesów inter:          4s
Czas wykonania procesów obl:            2s
Czas wykonania procesów I/O:            1s
-------------------------------------------
liczba proc. inter.:                    5
liczba proc. obl.:                      3
liczba proc. I/O:                       1
-------------------------------------------
czas wykonania 1 proc. inter.:         0.8s
czas wykonania 1 proc. obl.:           0.8s
czas wykonania 1 proc. I/O:            0.8s
```

### Zawartość koncepcji

1. koncepcja
2. algorytm
3. testy

> 2 punkty za koncepcje

> Sprawdzić implementowalność projektu

> Ogólna idea projektu. pamiętać, że $\Delta t = const$

> Wyrazić pomysł w języku programowania wysokiego poziomu, wygenerować wyniki testowe

> Taki wynik to ilość wybrań procesu przez procesor.

> Zamodelować zegar przy użyciu pętli na potrzeby testów

> Nie można alokować pamięci, tylko statyczne struktury danych

> Parametryzacja, podanie ilości procesów na grupę

> Liczniki wyboru wewnątrz algorytmu, ilość wybrań każdego procesu

### etc

> np. lista która będzie zawierała indeksy listy z typami procesów.

``[0, 1, 0, 2, 0, 0, 1, 0]``

> I druga lista z typami procesów

``["inter", "obl", "io"]``

> pętlą można udawać, że proces jest interaktywny

> Napisać funkcję systemową funckję definiującą flagi procesów (np. 0, 1, 2)

1. Wywołać funkcję systemową, nadać priorytet
2. Reszta
