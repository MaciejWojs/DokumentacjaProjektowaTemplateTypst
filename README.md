# Dokumentacja projektowa w Typst

Szablon dokumentacji zespołowego przedsięwzięcia inżynierskiego na Wydziale Nauk Inżynieryjnych Akademii Nauk Stosowanych w Nowym Sączu. Układ strony odpowiada szablonowi LaTeX z katalogu `Zespolowe-Przedsiewziecie-Inzynierskie-Docs-Template`: strona tytułowa, nagłówek, stopka z numerem „n z N”, pięć rozdziałów, literatura numeryczna oraz spisy rysunków, tabel i listingów.

Czcionka tekstu to Liberation Sans, a gdy jej nie ma, DejaVu Sans. Kod używa Liberation Mono, a w razie braku DejaVu Sans Mono. Obie zapasowe są w Ubuntu.

## Gdzie co zmienić

Dane projektu są w `main.typ`.

- W `main.typ` podaje się tylko to, co różni się od domyślnych: zwykle tytuł, przedmiot, autorów i prowadzącego. Uczelnia, wydział, katedra i `dokument` („DOKUMENTACJA PROJEKTU”) są w szablonie. Nagłówek bierze nazwę uczelni. Stopka składa się z `dokument` i przedmiotu. Dla sprawozdania laboratoryjnego dopisuje się na przykład `dokument: [SPRAWOZDANIE]`.
- Autorzy to lista napisów; puste wpisy są pomijane. `rok: auto` wstawia bieżący rok.
- Słownik `knobs` ustawia stronnicowanie, kolor hiperłączy i blok kodu. Podaj tylko pola, które zmieniasz. `stronnicowanie: "n-z-n"` daje „2 z 13”, a `"prosty"` sam numer strony. `kolor-linku: none` zostawia łącza w kolorze tekstu. W `knobs.kod` są `numeracja`, `ramka`, `kolor-ramki` oraz kolory tła, tekstu, numerów linii, słów kluczowych, komentarzy i napisów.
- Pojedynczy listing może to nadpisać: `numeracja: false` albo `kolory: (tlo: rgb("#eeeeee"))`.

Rozdziały leżą w `praca/`. Nowy rozdział to plik `praca/<nazwa>/rozdzial.typ` oraz `#include` w `main.typ`. Bibliografia jest w `praca/literatura.bib` i wchodzi do dokumentu tylko wtedy, gdy tekst ją cytuje (`#cite(<klucz>)`). Pliki z kodem trzymaj w `kod/`. Rozdziały importują `szablon/template.typ`. Części składu są w `szablon/czesci/`: `wspolne.typ` (czcionki i knobs), `strona.typ` (strona tytułowa, nagłówek, stopka), `kod.typ` (blok kodu), `obiekty.typ` (rysunek, tabela, listing, odsyłacz), `spisy.typ` (spisy i literatura), `praca.typ` (marginesy i reguły dokumentu).

W rozdziale:

```typst
#import "/szablon/template.typ": rysunek, tabela, listing, odnosnik

#rysunek(image("sources/nazwa.png", width: 12cm), [Opis rysunku]) <id-rysunku>
#odnosnik(<id-rysunku>)

#tabela([Opis tabeli], table(columns: 2, [A], [B])) <id-tabeli>
Tabela #odnosnik(<id-tabeli>)

#listing([Opis], ```cpp
int main() { return 0; }
```, jezyk: "cpp") <id-kodu>

#listing([Opis pliku], plik: "/kod/main.cpp", jezyk: "cpp") <id-pliku>
Kod #odnosnik(<id-pliku>)
```

`odnosnik` przy rysunku dopisuje „Rysunek”, przy tabeli i listingu zostawia numer i stronę. Ścieżka w `plik` zaczyna się od ukośnika i jest liczona od katalogu głównego projektu. Obraz w `image` jest liczony od pliku rozdziału.

## Codespace

1. Na GitHubie otwórz repozytorium, wybierz „Code”, potem „Create Codespace on main”.
2. Środowisko buduje się z `.devcontainer`. Obraz ma program `typst`. W edytorze są rozszerzenia Tinymist i Typst LSP, więc plik `main.typ` ma podgląd składni.
3. Wynikiem kompilacji jest `main.pdf`.

## Zadania edytora

W Codespace oraz lokalnie w VS Code i Cursor skrót `Ctrl+Shift+B` otwiera zadania z `.vscode/tasks.json`:

- „compile PDF with typst” kompiluje `main.typ` zainstalowanym programem `typst`,
- „cleanup ” usuwa `main.pdf`,
- „compile PDF with typst - docker” kompiluje dokument obrazem Dockera, bez lokalnego pakietu.

## Kompilacja lokalna

Zainstaluj pakiet `typst`, wejdź do katalogu projektu i uruchom:

```bash
typst compile main.typ
```

To samo polecenie wykonuje zadanie „compile PDF with typst”.

## Kompilacja przez Dockera

Gdy pakietu `typst` nie ma w systemie:

```bash
docker run --rm -v "$(pwd):/data" -w /data ghcr.io/typst/typst compile main.typ
```

To samo polecenie wykonuje zadanie „compile PDF with typst - docker”.

## Wydanie na GitHubie

Plik `.github/workflows/compile-to-pdf.yaml` uruchamia się po wypchnięciu tagu gita, którego nazwa zaczyna się od `v`, na przykład `v1.0.0`. Akcja instaluje `typst`, kompiluje `main.typ` i tworzy wydanie (release) z załączonym `main.pdf`. Nazwa wydania jest nazwą tagu.

```bash
git tag v1.0.0
git push origin v1.0.0
```

Tag bez prefiksu `v` tej akcji nie uruchamia. Zwykły push commita też nie.
