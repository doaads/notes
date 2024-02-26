[..](../swo.md)

# S🤓LID

* Reguły projektowania kodu obiektowego
    * SRP – Single Responsibility Principle
    * OCP – Open-Closed Principle
    * LSP – Liskov Substitution Principle
    * ISP – Interface Segregation Principle
    * DIP – Dependency Inversion Principle


## SRP

> Klasa/funkcja robi jedną rzecz

## OCP

> Moduły powinny być otwarte na rozszerzenie, ale zamknięte na modyfikacje

Moduł powinien pozwalać dodawać sobie zachowanie, ale zmiana zachowania innego modułu nie powinna wpływać na mój

Nie powinniśmy musieć dotykać kodu modułu, by
wykorzystać jego funkcje w inny sposób

## LSP

> Funkcje które używają referencji do klas bazowych, muszą być w
stanie używać również obiektów klas dziedziczących po klasach
bazowych, bez dokładnej znajomości tych obiektów

Każdy obiekt pochodny po obiekcie bazowym powinien
być jego dobrym zastępnikiem

Kontrakt klas - pisząc obiekt zawieram kontrakt z osobą, która go użyje

## ISP

> Wiele dedykowanych interfejsów jest lepsze niż jeden ogólny
