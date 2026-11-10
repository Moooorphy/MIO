# Laboratorium 1 Pierwsze uruchomienie projektu z agentem

**Repozytorium:** https://github.com/Moooorphy/MIO/tree/master/lab1

**Claude Code Opus 5.5 with high effort**. Tryb uprawnień zmieniłem z auto na manual po pierwszym prompcie .

 Sesja: [sesje/sesja-1.jsonl](sesje/sesja-1.jsonl)

## 1. Zlecenie, granice, odbiór
- **Wynik:** raport dla data/exercise.csv zapisany w results/exercise-summary.json.
- **Granice:** w etapie 1 bez zmian kodu, danych i testów oraz bez instalacji. W etapie 2 wolno było zmienić tylko jedną etykietę w data/exercise.csv.
- **Odbiór:** miejsce i treść raportu, wejście, polecenia z kodami wyjścia, wynik testu i niewykonane kroki.
- **Propozycje agenta:** sprawdzić przed uruchomieniem, że raport jeszcze nie istnieje. Obie przyjąłem. Flagę -B do testu odrzuciłem, żeby uruchomić test dokładnie poleceniem z README.

## 2. Jak powstał raport

Polecenie `py -3 describe_data.py --data data/exercise.csv --out results/exercise-summary.json` wykonał Claude Code w PowerShell na moim komputerze (Windows 11, Python 3.13.1). Proces zakończył się kodem 0.

Przed uruchomieniem Test-Path zwrócił False dla katalogu results i dla pliku raportu, więc raport powstał w tym uruchomieniu. Pole input_file wskazuje data\exercise.csv.

Podział ról: model wybiera polecenie, Claude Code obsługuje narzędzie, komputer wykonuje skrypt, a wynik wraca do modelu.

Liczebności w etapie 1: 8 wiadomości, w tym 5 ham i 3 spam.

## 3. Wyjaśniony fragment

`describe_data.py`, linie 13–32. Skrypt czyta CSV przez csv.DictReader: tekst w cudzysłowie to jedno pole, więc jeden rekord to jedna wiadomość. Każdy rekord musi mieć 3 niepuste pola, unikalne message_id i etykietę dokładnie ham albo spam. Przy pierwszym błędzie skrypt kończy się kodem 1 i nie zapisuje raportu.

**Przypadek:** 3 rekordy, z których ostatni ma etykietę Ham.
- **Przewidywanie:** 9 linii, 3 rekordy, błąd Record 3: label must be ham or spam., raport nie powstaje.
- **Sprawdzenie:** analiza z agentem linii 26, gdzie porównanie rozróżnia wielkość liter.
- **Wniosek:** przewidywanie trafne, pominąłem tylko kod wyjścia 1.

W drugim przypadku przecinek bez cudzysłowu dawał błąd missing or extra field.

## 4. Test

`py -3 -m unittest discover -s tests -v` dał wynik Ran 1 test, OK, kod 0. Test uruchamia skrypt na 3 syntetycznych wiadomościach z tests/fixtures/messages.csv i zapisuje raport do katalogu tymczasowego. Sprawdza kod 0, poprawność JSON, input_file oraz liczby 3 / 2 / 1 wpisane na stałe. Nie dotyczy exercise-summary.json i potwierdza działanie tylko dla tego małego wejścia.

## 5. Etap 2

Zmieniłem etykietę rekordu exercise-003 z ham na spam.

**Przewidywanie:** stary raport jest nieaktualny, bo nie odświeża się sam. Liczebności po zmianie: ham 4, spam 4, total 8, bo jedna wiadomość przechodzi z ham do spam.

**Operacje:**
- Ponowne uruchomienie tym samym poleceniem, żeby raport powstał z aktualnych danych.
- Sprawdzenie kodu wyjścia, bo przy błędzie na dysku zostałby stary raport.
- Odczyt raportu i porównanie z przewidywaniem.
- Bez testu, bo nie czyta on exercise.csv, a kod się nie zmienił.

**Wynik:** kod 0. Plik raportu został zapisany na nowo: czas modyfikacji zmienił się z 12:40 na 13:14.

| | Etap 1 | Przewidywanie | Etap 2 |
|---|---|---|---|
| total | 8 | 8 | 8 |
| ham | 5 | 4 | 4 |
| spam | 3 | 4 | 4 |
