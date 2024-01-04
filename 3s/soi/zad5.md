[..](../soi.md)

# Zarządzanie pamięcią

* bez swapa

* Zaimplementować własny algorytm przydziału pamięci (zastępca malloc())

* Wybrać niezajęte obszary pamięci

* Znajdujemy wystarczająco duży obszar i pobieramy co do jednostki, zwracamy adres.

* Zaimplementować jeden algorytm. (worst feed wystarczy)

Worst feed:
    - szukamy najwiękzego obszaru pamięci i z niego alokujemy

można poeksperymentować i wybrać inny algorytm

wywołania:
1. Zwraca bufer o konkretnej liczbie bajtow
2. Zwraca informacje o zajętości pamięci (mapę)

