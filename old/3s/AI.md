[..](../notes.md)

### Artificial Intelligence-Assisted Surgery: Potential and Challenges
> https://pubmed.ncbi.nlm.nih.gov/33447600/

### Potencjał AI w operacjach

> Wsparcie decyzji lekarza

> Pomoc kontekstowa

> Robotyka poznawcza

### SensorOR - Sensorycznie wspomagane sale operacyjne

> Sale operacyjne zawierają ogrom czujników, informacji i urządzeń potrzebnych do przeprowadzenia operacji. Pozwala to lekarzom na dokładne monitorowanie stanu pacjenta oraz może pomagać w podejmowaniu dalszych decyzji i bezpiecznym przeprowadzeniu operacji. Jednak tak ogromny natłok informacji może być dodatkowym czynnikiem stresującym lekarza podczas zabiegów, które są w stanie być stresujące same w sobie. Użycie sztucznej inteligencji mogłoby w tym przypadku ograniczyć ilość informacji jaką otrzymuje lekarz i odpowiednio dobrać ją do kontekstu.

### Decision Support

> SI jest obecnie używana lub rozwijana i badana w wielu dziedzinach medycyny, jednak to użycie skupia się głównie na diagnozie i rekomendacjach. W przypadku zabiegów medycznych SI nie ma jeszcze aż tak szerokiego zastosowania. Przykładowo podczas operacji onkologiczych mogłyby istnieć systemy, które byłby w stanie, rozpatrując dane pacjenta, publikacje naukowe i historię podobnych operacji wspomóc lekarzy wykonujących zabieg, w taki sposób, żeby maksymalnie zminimalizować szanse wystąpienia efektów ubocznych i komplikacji lub dobrania właściwego leczenia.

### Context-Aware assistance

> SI może być również użyte do podawania lekarzowi właściwych informacji we właściwym czasie, aby uniknąć natłoku informacji. Aby to osiągnąć możemy przedstawić proces zabiegu w formie modelu i za pomocą uczenia maszynowego nauczyć SI rozpoznawać konkretne jego etapy. Zastosowanie takiego modelu mogłoby również ostrzegać lekarzy w przypdaku znacznego odejścia od standardowej procedury lub dostarczyć informacji o pozostałym czasie operacji co pomogłoby w organizacji pracy szpitali.

### Cognitive Robotics

> Obecnie roboty używane w zabiegach to głównie telemanipulatory, czyli mechanizmy sterowane zdalnie przez operator. Badane i projektowane są obecnie systemy, które są w stanie przeprowadzać zabiegi, jednak nie są one gotowe na przeprowadzanie zabiegów na ludziach w warunkach szpitalnych.

## Therac 25

http://sunnyday.mit.edu/papers/therac.pdf

> Stosowana w latach 80 maszyna do radioterapii nowotworów produkowana przez Atomic Energy of Canada Limited. W latach 1985-1987 była przyczyną conajmniej sześciu wypadków. 5 osób spośród poszkodowanych zmarło

> Główną przyczyną nieprawidłowego funkcjonowania maszyny był błąd oprogramowania, na skutek którego w pewnych trudnych do odtworzenia warunkach, przy odpowiednio szybkiej pracy operatora dochodziło do podania znacznie większej dawki promieniowania.

> Ze względu na redukcje kosztów zrezygnowano z mechanicznych zabezpieczeń stosowanych we wcześniejszych generacjach maszyny

> Systemy robotyki kognitywistycznej również opierałyby się na systemach informatycznych, więc byłyby podatne na błędy oprogramowania, co mogłoby spowodować powtórzenie takich incydentów.

> Wydarzenia wiążące się z maszynami Therac-25 weszły do kanonu studiów na temat niezawodności oprogramowania i stanowią istotny argument przemawiający za stosowaniem specjalnych kroków projektowych w krytycznych dla życia lub zdrowia ludzkiego zastosowaniach technologii informatycznych.

## Jak jednak wygląda to w przypadku AI?

https://haiweb.org/wp-content/uploads/2023/03/MDR-AIAct_OnePager_FINAL.pdf
https://spyro-soft.com/pl/blog/healthcare-and-life-sciences/eu-mdr-2021
https://www.europarl.europa.eu/news/en/headlines/society/20230601STO93804/eu-ai-act-first-regulation-on-artificial-intelligence

> W przypadku oprogramowaniu opartemu o sztuczną inteligencję mamy podobny, jeśli nie większy problem związany z potencjalnymi błędami w implementacji samych algorytmów lub decyzji modelu, na którym się opieramy. Jeśli w poprzednich przypadkach możemy stwierdzić, że winę prawnie ponosi producent, w przypadku sztucznej inteligencji może być to znacznie mniej oczywiste i o wiele cięższe do określenia.

> AI act dzieli w obecnej formie systemy sztucznej inteligencji w podkategorie związane z ryzykiem, gdzie systemy związane z medycyną będą klasyfikowane jako systemy wysokiego ryzyka, będą one musiały być odpowiednio ocenione i zbadane zarówno przed wprowadzeniem na rynek, jak i w trakcie ich funkcjonowania na nim.

> Jednakże to prawo dotyczy urządzeń medycznych podlegających pod MDR - Medical Device Regulation, czyli regulację Unii Europejskiej dotyczącą klasyfikacji urządzeń medycznych. Według MDR urządzenie medyczne jest wytworzone po to, aby spełniało jedno lub więcej poniższych kryteriów:

* leczenie, diagnoza, prewencja, monitorowanie, prognozowanie chorób,
* wszelkiego rodzaju działania, które miałyby wspomóc pacjentów z niepełnosprawnościami,
* badanie, zastępowanie i modyfikacja anatomii, fizjologii lub procesów fizjologicznych, które odbywają się w ludzkim ciele, lub elementów, które mają wpływ np. na metabolizm, w sposób pośredni lub bezpośredni.

> Widzimy tutaj brak jednoznacznego wymienienia urządzeń robotyki kognitywistycznej, urządzenia podlegające pod MDR głównie skupiają się na diagnozie i monitorowaniu oraz wsparciu osób niepełnosprawnych. Jednakże biorąc pod uwagę potencjalne zastosowania AI w medycynie, możemy zauważyć, że realne byłoby wprowadzenie urządzeń pomocy kontekstowej, które pokazywałyby lekarzowi właściwe informacje we właściwym czasie wspomagając tym samym lekarza wykonującego zabieg.

> MDR zostało wprawione w życie w maju 2021 roku, więc biorąc pod uwagę ogromne tempo rozwoju sztucznej inteligencji w ostatnim czasie, może okazać się, że poza AI act będziemy potrzebować również nowego sposobu klasyfikacji urządzeń medycznych, aby uwzględnić bardziej zaawansowane systemy. Jednakże tak jak opisaliśmy wcześniej, systemy robotyki kognitywistycznej nie zostały jeszcze wprowadzone do sal operacyjnych, więc na ten moment pozostaje nam jedynie czekać.

> Badania urządzeń medycznych wspartych systemami AI opierałoby się na ocenie, czy pozytywny wpływ na przebieg zabiegu będzie większy od ryzyka, co najpewniej również wyklucza wprowadzenie niektórych systemów w najbliższych latach.

> W przeciwieństwie do urządzeń sklasyfikowanych przez MDR, AI act wymaga nadzoru człowieka nad urządzeniami i systemami medycznymi wysokiego ryzyka (czyli de facto wszystkich urządzeń medycznych wspartych AI)

> Dane używane przez systemy AI wysokiego ryzyka również mają znaczenie. Według AI act muszą być one "znaczące, reprezentatywne, wolne od błędu i kompletne""
