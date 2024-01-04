[..](../wsi.md)

# WSI Lab - Zadanie 2

### Bartosz Nowak, 325201

## Opis zadania

 Celem zadania drugiego było zaprojektowanie i implementacja algorytmu ewolucyjnego, a następnie zbadanie jego zbieżności na funkcjach ``F1`` oraz ``F9`` z benchmarku [CEC2017](https://github.com/tilleyd/cec2017-py) na wymiarowości $n = 10$ oraz porównanie tej zbieżności z implementacją algorytmu gradientu prostego.

<hr>

## Opis działania i implementacji algorytmu

 Zaimplementowana zostałą strategia ewolucyjna $ES(\mu, \lambda)$

 Zaimplemenotwanie metody ``__add__`` pozwala również na prostą możliwość przekształcenia w strategię $ES(\mu + \lambda)$

<hr>

*Pseudokod $ES(\mu, \lambda)$*

```sh
Result: x, omega

// population - populacja początkowa
// fn - funkcja celu

t <- 0

x, omega <- population.find_best()

while kryterium_stopu(t, tmax) do:
    reproduced <- wybierz lambda losowych osobników
    reproduced.mutacja()

    x`, omega` <- najlepszy osobnik z populacji mutantów
    if omega < omega then
        x <- x`
        omega <- omega`
    end

    final_population = reproduced // dla mu + lambda: reproduced + population
    
    population <- mu najlepszych osobników z final_population (fn, mu)
    t <- t + 1
    
end
```

Z kolei mutacja przebiega w następujący sposób:

Dla każdego osobnika:

1. Przygotowanie liczb pseudolosowych: $a = \mathcal{N}(0, 1), b_{[i]} = \mathcal{N}(0, 1)$
2. Mutacja wektora $\sigma$: $\sigma_{[i]} * exp(\tau'a + \tau b_{[i]})$, gdzie $\tau = \frac{1}{\sqrt(2n)}, \tau' = \frac{1}{\sqrt{2\sqrt{n}}}$
3. Mutacja chromosomu kodującego rozwiązanie

Algorytm posiada również dwa warunki stopu:
* Liczba iteracji algorytmu osiągnęła zadaną w parametrze funkcji wartość.
* Liczba kolejnych iteracji, w których najlepszy osobnik zmienił się o conajwyżej $\epsilon$ wyniosła 10% całkowitej liczby iteracji

Przetestowana została zarówno elitarna ($ES(\mu + \lambda)$) oraz nieelitarna ($ES(\mu, \lambda)$) wersja algorytmu, ostatecznie wybrana została wersja nieelitarna, ze względu na mniejszą skłonność do osiadania w minimach lokalnych.

## Badanie zbieżności i działania algorytmu

<center>Wykres 1</center>

![](img/2023-11-14-19-02-23.png)

> Populacja 50 losowo stworzonych osobników, 400 iteracji, funkcja F1

Powyższy wykres przedstawia pojedyncze, przykładowe działanie algorytmu dla funkcji F1 z benchmarku CEC2017, dla populacji 50 osobników i $\lambda = 8 * \mu$

Po takiej ilości iteracji wartość funkcji celu nadal jest ogromna (10^8), jednak w stosunku do wartości początkowej jest to znaczna poprawa, teraz sprawdzimy jak algorytm zachowa się przy znacznie większej ilości iteracji

Biorąc pod uwagę udział zmiennych losowych w działaniu algorytmu, tym razem wykonamy go więcej razy, aby uzyskać uśrednione wyniki.

<center>Wykres 2</center>

![](img/2023-11-14-19-13-19.png)

> Populacja 50 losowo stworzonych osobników, 2500 iteracji, funkcja F1, 25 wywołań algorytmu

Po 2500 iteracjach otrzymujemy średnią wartość równą $1,5 * 10^6$, oraz wartość minimalną równą **250**. 

Widzimy, że algorytm jest w stanie skutecznie zoptymalizować funkcję F1.
Zauwazyć możemy również jego wady, algorytm ma skłonność do osiadania w minimach lokalnych funkcji podczas dalszych iteracji. Może to wynikać z kształtu samej funkcji, oraz ze zbyt niskich wartości siły mutacji posiadanych przez najlepszych osobników.

<center>Wykres 3</center>

![](img/2023-11-14-19-29-45.png)

> 50 osobników, 2500 iteracji, F1, 250 wywołań algorytmu

W tym przypadku algorytm został wywołany 250 razy. Średnia wartość prezentuje się na podobnym poziomie, co na wykresie 2, ($8 * 10^5$), tutaj z kolei wartość minimalna jest znacznie wyższa niż w poprzednim przypadku, i wynosi ona 4106.

<hr>
<center>Wykres 4</center>

![](img/2023-11-15-16-00-30.png)

> 50 osobników, 400 iteracji, funkcja F9

Teraz rozpatrzymy działanie algorytmu dla funkcji F9. W przypadku pojedynczego wywołania możemy zauważyć, że algorytm szybko zatrzymał się przy wartościach bliskich 1200-1300, co może świadczyć o istnieniu minimum w tej okolicy. Teraz wywołamy algorytm 100 razy dla tej samej funkcji, i zbadamy jak wpłynie to na wyniki, aby móc ocenić zbieżność algorytmu.

<center>Wykres 5</center>

![](img/2023-11-14-19-25-36.png)

> 50 osobników, 2500 iteracji, F9, 100 wywołań

W przypadku funkcji F9 możemy zaobserwować znacznie większą różnorodność wyników, dające średnią równa 1673.

Funkcja F9 była więc znacznie większym wyzwaniem dla algorytmu niż F1, a algorytm znacznie częściej zatrzymywał się w minimach lokalnych, dla których wartości różniły się o nawet kilkanaście punktów procentowych od znalezionego minimum (1130).


<hr>

## Porównanie działania algorytmu do algorytmu gradientu prostego.

<center>Wykres 6</center>

![](img/2023-11-15-16-24-11.png)

> Algorytm gradientu prostego dla $\alpha = 0.00000001$

Możemy zauważyć, że w przypadku funkcji F1 algorytm gradientu prostego również jest w stanie poradzić sobie z optymalizacją, jednak w niektórych przypadkach potrafi się on zatrzymać w minimach lokalnych o wartościach rzędu 15000. Wadą algorytmu gradientu prostego jest fakt, że dla danego punktu będzie przyjmował stałą wartość, z kolei algorytm ewolucyjny jest po wielu wywołaniach w stanie odnaleźć lepszą wartość minimalną.

Kolejną wadą algorytmu gradientu prostego jest fakt, że nie otrzymamy wyniku nawet zbliżonego do minimum globalnego bez odpowiedniego dobrania parametru $\alpha$, co nie jest problemem w przypadku algorytmu ewolucyjnego. W przypadku wykresu szóstego, została dobrana taka wartość $\alpha$, dla której algorytm radzi sobie odpowiednio dobrze, by wyznaczyć minimum z podobną dokładnością, co algorytm ewolucyjny.

Dla funkcji F9 algorytm nie był w stanie wygenerować gradientu, zatem przetestowana została funkcja F4. Algorytm ewolucyjny radzi sobie z nią podobnie jak z funkcją F1, skutecznie znajdujac minima zbliżone do minimum globalnego.

<center>Wykres 7</center>

![](img/2023-11-15-17-01-07.png)

> 50 osobników, 400 iteracji, F4, 10 wywołań

W tym przypadku średnie wartości, na których algorytm kończy działanie to około 450.

![](img/2023-11-15-17-11-35.png)

> Gradient prostu dla F4, $\alpha = 0.01$

W tym przypadku ponownie największym wyzwaniem jest znalezienie takiego parametru $\alpha$, dla którego algorytm będzie zbieżny i zwróci wartości bliskie minimum. Taką wartością, która zwraca podobne rezultaty do algorytmu ewolucyjnego jest $\alpha = 0.01$.

## Modyfikacja parametrów algorytmu

> Na koniec sprawdzimy jak ma się liczba początkowych osobników do wyników algorytmu. Ponownie rozpatrzymy funkcję F1.

<center>Wykres 8</center>

![](img/2023-11-16-07-41-53.png)

Możemy zauważyć o wiele większą rozbieżnośc wyników, oraz ich znacznie większa średnią, wynoszącą ponad $10^{11}$

<center>Wykres 9</center>

![](img/2023-11-16-08-23-36.png)

> 200 osobników, 2500 iteracji, funkcja f1, 10 wywołań

W przypadku znacznie większej ilości osobników możemy zaobserwować znaczny spadek średniej wartości końcowej algorytmu, która wynosi **7400** i jest bardzo znalezionego minimum (**5882**)
