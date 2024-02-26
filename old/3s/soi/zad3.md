[..](../soi.md)

## Wymagania do zadania 3

Bartosz Kozłowski: bartosz.kozlowski@pw.edu.pl
Raporty PDF do 8.12, 10:15

* Synchronizacja wątków z wykorzystaniem semaforów
* C++, system Linux
* Wątki w standardzie POSIX (pthread.h)
* Synchronizacja dostępu **konsumentów** i **producentów** do wspólnego **magazynu**
* Magazyn o pojemności $k$ sztuk towaru
* $n$ konsumentów, każdy z nich jednorazowo odbiera partię towaru w liczbie sztuk ~ $U(a, b)$
* $m$ producentów, każdy z nich jednorazowo produkuje partię towaru w liczbie sztuk ~ $U(c, d)$
* Producenci przekazują towar konsumentom za pośrednictwem magazynu
* Producenci i konsumenci obsługują tylko całe partie towaru
* Zalecane rozwiązanie w oparciu o dziedziczenie (przekazywanie) sekcji krytycznej [przykład](http://smurf.mimuw.edu.pl/node/1258)

- Magazyn zawiera tylko swój aktualny stan
- Każdy wątek zawiera swój log
```
producent próbuje wstawić partię 10 sztuk towaru
...
udało mu się.
...
producent próbuje wstawić partię 3 sztuk towaru
...
nie udało mu się.
...
```

lepsze rozwiązanie z dziedziczeniem sekcji krytycznej

### Raport

- algorytm producenta i konsumenta, testy w formie przewidywań działania

### Wywołanie

```
./program -n prod_num -m con_num
```

dotfile
