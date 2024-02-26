# WSI - Zadanie 5 - Bartosz Nowak

## Cel zadania

Celem zadania piątego była implementacja perceptronu wielowarstwowego oraz użycia go w celu aproksymacji funkcji:

$$ f(x) = x^2 \cdot sin(x) + 100 \cdot sin(x) \cdot cos(x) $$
<center>dla</center>
$$ x \in [-10;10] $$

oraz zbadanie wpływu liczby neuronów na jakość uzyskanej aproksymacji.

## Implementacja

Najważniejsze funkcje zaimplementowanego solwera aproksymacyjnego to:
* ``forward`` - przejście przez sieć od wejścia do wyjścia dla zadanego wektora X i zwrócenie wyników
* ``backward`` - obliczenie wartości błędu oraz gradientu dla każdej warstwy rozpoczynając od końca, a następnie zaaplikowanie algorytmu gradientu prostego dla wag i wartości ``bias``
* ``train`` - funkcja przyjmująca wektor X oraz y (dane trenujące), która dla zadanego pakietu danych zajmie się uczeniem sieci neuronowej
* ``sigmoid`` i ``sigmoid_der`` to funkcja aktywacyjna dla neuronów warstwy ukrytej oraz jej pochodna
* ``predict`` zwraca wartości y dla zadanego X (feed forward dla wektora X)

Funkcją aktywacyjną neuronów warstwy ukrytej jest funkcja logistyczna.

$$ a(x) = \frac{1}{1+e^{-x}} $$

Ze względu na częste problemy z przepełnieniem i zwracaniem wartości NaN przez algorytm funkcja została przedstawiona w następujący sposób:

$$ a(x) = \frac{e^x}{1 + e^x} $$

Pochodna funkcji logistycznej wyraża się w następujący sposób:

$$ a'(x) = a(x) \cdot (1 - a(x)) $$

## Test działania algorytmu

Na sam początek rozważymy funkcję $y = sin(x)$ i spróbujemy dokonać jej aproksymacji dla $x\in[0;\frac{\pi}{2}]$

![](img/2024-01-06-12-20-03.png)
> 10000 iteracji, 20 neuronów w warstwie

Możemy szybko zauważyć, że dla funkcji rosnących algorytm radzi sobie bardzo dobrze. i potrafi skutecznie przewidzieć wartości funkcji. Teraz rozpatrzymy przedział $x\in[\frac{\pi}{2};\pi]$

![](img/2024-01-06-12-24-39.png)

W tym przypadku algorytm poradził sobie prawie równie dobrze.

Teraz rozważymy funkcję $f(x)$ Opisaną wyżej.

![](img/2024-01-06-12-30-50.png)

W tym przypadku algorytm nie poradził sobie tak dobrze, zwrócona aproksymacja jest funkcją liniową, spróbujemy zatem przyjrzeć się funkcji z bliska i podzielimy jej dziedzinę na mniejsze elementy.

### Aproksymacja dla $x\in[0;2,5]$:

![](img/2024-01-06-12-40-27.png)

W tym przypadku aproksymacja wygląda już znacznie lepiej. Spróbujemy zatem dokonać aproksymacji funkcji na całej jej dziedzinie łącząc cztery wykresy aproksymacji dla $x\in[2.5\cdot(i-4) ; 2.5\cdot(i-8)]$, gdzie $i$ - numer aproksymacji, $i \in [0; 8] \land i \in \mathbb{N}$

### Połączenie wielu aproksymacji w celu uzyskania spójnej całości

![](img/2024-01-06-12-50-24.png)

> Aproksymacja dla 5000 iteracji, 20 neuronów w warstwie.

![](img/2024-01-06-12-53-04.png)

> Aproksymacja dla 10000 iteracji, 20 neuronów w warstwie.

Z powyższych wykresów możemy wyciągnąć dwa istotne wnioski:
1. Algorytm radzi sobie o wiele lepiej po podzieleniu aproksymowanej funkcji na mniejsze i późniejszym "sklejeniu" aproksymacji w jedną
2. Bardzo ważne jest zadanie algorytmowi odpowiednio wysokiej liczby iteracji oraz właściwych parametrów uczących.

Dla $i=3$ dopiero w okolicy 3000 epoki wartość loss zmalała poniżej 0.1 i finalnie wyniosła zaledwie 0.005. (dla iteracji 9000). Przy 5000 iteracjach nie bylibyśmy, dla zadanych parametrów, dobrze zaproksymować tej funkcji.

### Badanie wpływu ilości neuronów w warstwie na wynik aproksymacji.

Teraz rozważymy sytuację, gdzie:
$$ x\in[(i-10) ; (i-9)], i \in [0; 20), i \in \mathbb{N}$$

Zaczniemy badanie od 20 neuronów w warstwie ukrytej

![](img/2024-01-06-13-19-25.png)

Teraz zmniejszymy tę liczbę do 5-ciu neuronów

![](img/2024-01-06-13-21-39.png)

Możemy zaobserwować, że aproksymacja wciąż jest dość trafna, jednakże pojawiły się w niej pewne braki oraz generalne pogorszenie jakości. Braki wynikają z wartości NaN na wyjściu algorytmu spowodowanej przepełnieniem. Teraz zmniejszymy liczb

![](img/2024-01-06-13-24-11.png)ę neuronów do dwóch.

Możemy zaobserwować całkowity spadek jakości aproksymacji.

Na powyższym wykresie widać również, że aproksymacja przyjęła postać prostej linii, co mogłoby oznaczać, że przy odpowiednio wysokiej liczbie neuronów oraz iteracji i właściwych parametrach, moglibyśmy zaproksymować całą funkcję f(x) za pomocą jednego perceptronu. Nie będziemy jednak tego próbować, ze względu na ogromny czas potrzebny do aproksymacji funkcji o większej liczbie neuronów, próbek oraz iteracji.

## Wnioski

Przy odpowiednio dobranych parametrach takich jak ilość neuronów, learning rate, odpowiedni podział danych oraz wielkość jego zbioru perceptron wielowarstwowy jest w stanie skutecznie aproksymować funkcje nieliniowe.
