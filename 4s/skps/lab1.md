[..](../skps.md)

# SKPS - Laboratorium numer 1. Bartosz Nowak, Paweł Wysocki

## 1. Przygotowanie stanowiska

Ćwiczenie laboratoryjne rozpoczynamy od przygotowania stanowiska pracy.

![](img/2024-03-06-13-22-02.png)

## 2. Pierwsze uruchomienie RPi

Rozpoczynamy od podłączenia się do terminala UART za pomocą ``tio``

![](img/2024-03-06-13-23-49.png)

Połączenie powodzi się. Mamy teraz dostęp do terminala RPi

![](img/2024-03-06-13-25-19.png)

Logujemy się jako ``root``.

Następnie ręcznie uruchamiamy DHCP i czekamy na przydział adresu ip

![](img/2024-03-06-13-26-34.png)

Z terminala RPi sprawdzamy, czy połączenie zostało nawiązane

![](img/2024-03-06-13-28-29.png)

Następnie robimy to samo po stronie hosta

![](img/2024-03-06-13-32-20.png)

Jak widać pakiety zostają poprawnie odebrane

## 3. Przesyłanie plików przez http

Rozpoczynamy od uruchomienia serwera http na komputerze hosta za pomocą ``python -m http.server``

Na potrzeby tego ćwiczenia przygotowaliśmy testowy plik ``test.txt``, który zostanie pobrany z komputera hosta na RPi.

Za pomocą ``wget`` pobieramy plik, podając ``http://10.42.0.1:8000/test.txt``

![](img/2024-03-06-13-36-41.png)

Jak widać plik został poprawnie pobrany.


![](img/2024-03-06-13-33-15.png)

## 4. Budowanie obrazu linuxa w buildroot

Rozpoczynamy od obrazu z ``initramfs`` (ramdyskiem startowym). Aby utworzyc taką konfigurację, wchodzimy w menu konfiguracji za pomocą ``make menuconfig`` i w zakładce ``filesystem images`` ustawiamy poniższe flagi:

![](img/2024-03-06-13-44-13.png)

Wybieramy również dwa edytory tekstowe: ``vim`` oraz ``nano``

![](img/2024-03-06-13-45-20.png)

Rozpoczynamy kompilację za pomocą ``make``

![](img/2024-03-06-14-06-38.png)

Po około pół godziny, kompilacja powiodła się.
Wymagane również było zwiększenie rozmiaru partycji ``boot`` do 128 MB w pliku
``<buildroot>/board/raspberrypi4-64/genimage-raspberrypi4-64.cfg``

## 5. Uruchomienie zbudowanego obrazu

Pobieramy zbudowany obraz z maszyny hosta za pomocą ``wget``

![](img/2024-03-06-14-13-19.png)


Montujemy partycję 1 (boot) karty SD w katalogu /mnt za pomocą polecenia ``mount /dev/mmcblk0p1 /mnt``

Następnie kompiujemy pobrane pliki na kartę SD i zmieniamy nazwę pliku Image na ``kernel8.img``

![](img/2024-03-06-14-18-14.png)

Restartujemy system i widzimy, że został zbudowany i uruchomiony poprawnie

![](img/2024-03-06-14-23-53.png)

## 6. Test działania ramdysku startowego.

Sprawdzimy czy ramdysk startowy działa poprawnie. Stworzymy plik ``test.txt`` a następnie zrestartujemy system za pomocą ``reboot``, aby sprwadzić, czy plik został zapisany (spodziewamy się braku zapisu)

![](img/2024-03-06-14-16-27.png)

Po restarcie systemu widzimy, że plik nie został zapisany

![](img/2024-03-06-14-16-45.png)

## 7. Budowanie obrazu bez initramfs

Ropoczynamy od wyczyszczenia obrazu (po skopiowaniu pliku .config)

``make linux-dirclean``

Następnie wyłączamy ramdysk startowy i włączamy system plików ``ext2``

Konfiguracja w ``make menuconfig`` w zakładce ``Filesystem images`` wygląda następująco:

![](img/2024-03-06-14-11-32.png)

Następnie budujemy obraz za pomocą ``make``

Po zbudowaniu obrazu ponownie kopiujemy go na RPi za pomocą ``wget``

Następnie nagrywamy system plików z poziomu systemu ratunkowego na partycję ``rootfs`` karty SD.

![](img/2024-03-06-14-49-02.png)

Ponownie montujemy partycję 1 karty SD i przeniosimy tam wymagane pliki

![](img/2024-03-06-14-48-50.png)

Restartjemy system i zauważamy, że uruchomienie powiodło się

![](img/2024-03-06-14-48-20.png)

## 7. Test działania obrazu z systemem plików ext2

Tworzymy plik ``test.txt`` i restartujemy system

![](img/2024-03-06-14-50-18.png)

Po restarcie systemu widzimy, że plik się zachował. To oznacza, że system używa systemu plików ``ext2``, a po restarcie zapisane pliki nie są usuwane
