[..](../soi.md)

# Koncepcja do 6 zadania laboratoryjnego SOI

### Bartosz Nowak

## Cel zadania
Celem zadania szóstego jest implementacja systemu plików w systemie MINIX, w języku C.

Zaimplementowany zostanie system plików z przechowywaniem o organizacji indeksowej.

Warunki:
* Każdy plik posiada unikalną nazwę
* Każdy plik posiada swój węzeł indeksowy
* Każdy plik posiada 1 lub więcej bloków danych
* Konieczność przechowywania nazwy, rozmiaru i dane o sposobie rozmieszczenia (indeksy bloków) dla każdego pliku
* Funkcjonalność tworzenia dysku
* Funkcjonalność kopiowania plików między dyskiem wirtualnym i systemem
* Wyświetlanie zawartości katalogu
* Usuwanie plików oraz dysków
* Wyświetlanie informacji o systemie plików

### Węzły indeksowe:

Węzły indeksowe jako struktura zawierająca informacje o indeksach bloków, w których przechowywane są pliki, oraz podstawowe informacje o plikach.

Implementacja węzła indeksowego w języku C:

```c
/* Inode structure */
typedef struct inode {
	char name[MAX_NAME_SIZE];
	unsigned file_size;
	unsigned short busy;
	unsigned short data_ids[MAX_BLOCKS];
	unsigned short used_blocks;
} inode;
```

gdzie:
``MAX_NAME_SIZE`` - maksymalna długość nazwy pliku
``MAX_BLOCKS`` - maksymalna ilość bloków danych dla węzła indeksowego

### Superblok:

Na początku stworzonego dysku wirtualnego występują metadane dysku połączone w *superblok*. Jest on pewnego rodzaju nagłówkiem dla reszty dysku i zawiera informacje o rozmiarach poszczególnych sekcji, ilości bloków i ich rozmiarze, oraz dane o i-węzłach i nazwę dysku.

Implementacja superbloku w języku C:

```c
/* Superblock structure */
typedef struct superblock {
	char name[MAX_NAME_SIZE];
	unsigned int sb_size;
	unsigned int inodes_size;
	unsigned int data_info_size;
	unsigned int data_size;
	unsigned int disk_size;
	unsigned int block_amount;
	unsigned int block_size;
	unsigned int max_file_size;
	int total_inodes;
} superblock;
```

## Blok Data Info

Z kolei na końcu sekcji metadanych dysku znajdzie się struktura zawierająca informacje o zajętości każdego z bloków danych (Data Info)

Implementacja:
```c
typedef struct data_info {
	unsigned short busy;
} data_info;
```

Poniżej przedstawiona zostanie koncepcja tworzenia dysku wirtualnego i początkowej inicjacji parametrów.

1. Sprawdzenie nazwy pliku (czy nazwa istnieje, czy jest dostatecznej długości)
2. Inicjalizacja superbloku, nadanie mu nazwy, wielkości, parametrów (rozmiar bloku, ilość plików)
3. ustalenie rozmiaru bloku i-węzłów (może się nie powieść, jeżeli maksymalna ilość plików będzie zbyt duża)
4. obliczenie maksymalnej wielkości pliku. (ta zależna jest od długości tablicy zawierającej indeksy bloków dla każdego i-węzła)
5. inicjalizacja każdego i-węzła, ustawienie zajętośi oraz wielkości pliku i użytych bloków na 0.
6. Obliczenie największej możliwej ilości bloków, które można zaalokować na dysku.
7. inicjalizacja bloków danych, ustawienie zajętości na 0 dla każdego bloku
8. Utworzenie pliku reprezentującego dysk.
9. Zapisanie metadanych dysku w pliku, oraz zapisanie sekcji danych zerami.

W celu dokonywania zmian/odczytywania stanu dysku będzie konieczne "otwarcie" go, czyli odczytanie metadanych pliku.

Implementacja otwierania dysku:
```c
/* open the drive and return file_system structure */
file_system* open_drive(char* name) {
	int i;
	FILE* drive;
	char* buffer;
	file_system* fs = (file_system*) malloc(sizeof(file_system));
	
	/* check if the drive exists */
	if (access(name, F_OK) == -1) {
		printf("Error: drive does not exist\n");
		return NULL;
	}
	/* open the drive */
	drive = fopen(name, "rb");
	if (drive == NULL) {
		printf("Error: could not open drive\n");
		return NULL;
	}

	/* read superblock data from the drive */
	fs->sb = (superblock*) malloc(sizeof(superblock));
	fread(fs->sb, sizeof(superblock), 1, drive);

	/* read inode data from the drive */
	fs->inodes = (inode*) malloc(fs->sb->inodes_size);
	fread(fs->inodes, fs->sb->inodes_size, 1, drive);

	/* read data block info from the drive */
	fs->block_info = (data_info*) malloc(fs->sb->data_info_size);
	fread(fs->block_info, fs->sb->data_info_size, 1, drive);

	return fs;
}
```

gdzie struktura file_system to wskaźnik na każdą z trzech struktur składających się na metadane dysku.

### Kopiowanie plików.

Kopiowanie plików odbywać się będzie w następujący sposób.

1. Podanie przez użytkownika parametrów - nazwy pliku na dysku wirtualnym oraz ścieżki w systemie plików użytkownika
2. Sprawdzenie, czy nazwa pliku jest odpowiednia, oraz czy taki plik już nie istnieje
3. Sprawdzenie, czy rozmiar pliku nie jest zbyt duży.
4. Ustalenie, ile bloków potrzebne będzie do alokacji pliku na dysku.
5. Rezerwacja i-węzła, ustawienie jego pól: nazwy pliku, rozmiaru pliku, ilości bloków
6. Zajmowanie kolejnych bloków danych i dodawanie ich do i-węzła.
7. Jeśli okaże się, że bloków nie wystarczy, zostanie zwrócony błąd, a plik nie zostanie utworzony
8. Zapisanie pliku na zarezerwowanych blokach danych.
9. Zapisanie zmienionych metadanych pliku na dysku.

### Usuwanie plików.

Usuwanie plików polega na wymazaniu pól i-węzła oraz ustawienie jego bloków na niezajęte, bloki ustawiane są na nieużywane, a i-węzeł może być ponownie użyty przez następny plik, metoda kopiowania zapenia, że dane, które pozostaną na nieużywanym bloku, nie będą widoczne w pliku, który zostanie do tego bloku przekopiowany później.

### Sprawdzanie stanu pamięci.

Do sprawdzania stanu pamięci wirtualnego dysku udostępnione zostaną parametry wywołania:
* map - mapa pamięci (adres, rozmiar, zajętość, typ, nazwa bloku)
* ls - wylistowaine katalogu
* info - informacje o systemie plików (zajęte bloki, węzły itd.)
