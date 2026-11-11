# Lab 2 – Plan prototypu rozpoznającego spam

Repozytorium: https://github.com/Moooorphy/MIO/tree/master/lab2

Claude Code Opus 5.5 with high effort.

Sesja: [sesje/sesja-1.jsonl](sesje/sesja-1.jsonl)

## 1. Cel i zakres
Lokalny prototyp rozpoznający spam z tekstu wiadomości (temat + treść) przy wykorzystaniu etykiet spam/ham. Zwraca spam albo ham. 

Dane: train.csv (120 ham, 60 spam) do uczenia, validation.csv (40 ham, 20 spam) do walidacji. Pliki i podział zostają bez zmian. Zbiór jest mały i historyczny, więc wynik nie przenosi się na współczesną pocztę.

## 2. Podejście i uzasadnienie
- **Dane:** brak pustych pól i duplikatów, etykiety tylko spam/ham. 6 z 20 spamów walidacyjnych to prawie takie same wiadomości co w spamach treningowych, więc wynik powinien być dla całej walidacji (60) i bez niemal kopii (54). Walidacji użyłem tylko do wyznaczenia tych 6 wiadomości, dla zastosowania jako przykład dydaktyczny.
- **Reprezentacja: TF-IDF**, słownik (wszystkie słowa z train) i IDF dopasowane tylko na train, wektor normalizowany. Małe litery, ! i $ jako tokeny (także wielokrotne !), encje HTML zamieniane na znaki i znaczniki usuwane, słowa z adresów zostają jako zwykłe słowa, brak cechy „czy jest link”, link jest w 78% ham i 83% spamu, więc nie odróżnia klas, tagi list zostają. Odrzucone n-gramy znakowe: zbyt duża liczba cech przy małej próbce wiadomości grozi przeuczeniem, a dodatkowo trudniej odczytać decyzję.
- **Model: wielomianowy naiwny Bayes**, ustawienia domyślne (w tym proporcja klas 1/3 z train). Szacuje każde słowo osobno, wkład słów łatwo odczytać. Odrzucona regresja logistyczna: przy tylu cechach zależy od parametryzacji, której nie dobieram. Zaakceptowane zostało ryzyko przesadnego prawdopodobieństwa Bayesa, oceniam tylko decyzję spam/ham, bez przesuwania progu.

## 3. Kroki i zależności
1. Wczytanie i kontrola danych.
2. Sprawdzenie reguł przetwarzania na train ($, <…>, litery spoza ASCII).
3. Przetworzenie tekstu, dopasowanie słownika i IDF na train - reprezentacja.
4. Uczenie Bayesa na wektorach i etykietach train - model.
5. Zapis reprezentacji + modelu (potrzebne do klasyfikacji tą samą reprezentacją).
6. Klasyfikacja walidacji (bez etykiet) zapisaną reprezentacją, bez ponownego dopasowania → przewidywania z message_id.
7. Porównanie z etykietami walidacji po message_id - macierz pomyłek, lista pomyłek.
8. Miary dla 60 i 54 wiadomości (lista 6 message_id niemal kopii zapisana osobno) oraz wynik „zawsze ham”.

Walidacja nie bierze udziału w krokach 2–4. Ocena jednorazowa, bo poprawianie modelu po zobaczeniu wyniku byłoby strojeniem.

## 4. Planowana ocena
Model oceniam na validation.csv, którego nie używam do uczenia. Liczę wynik dwa razy: na wszystkich 60 wiadomościach i na 54, bez 6 spamów, które są prawie kopiami wiadomości z treningu.

Dla każdej wiadomości porównuję odpowiedź modelu z jej etykietą (po message_id). Z tego powstaje tabela trafień i pomyłek oraz lista błędnie ocenionych wiadomości.

Patrzę na dwie miary:
- **recall spamu** – jaką część spamu model złapał (reszta to spam przepuszczony),
- **odsetek fałszywych alarmów** – ile dobrych maili model uznał za spam.

Podaję je razem, bo każda osobno może wyglądać dobrze przy złym modelu. Obie nie zależą od proporcji klas. Pomijam precision, bo zależy od proporcji klas, i F1, bo łączy oba błędy w jedną liczbę. Podaję liczbę trafień  obok procentów ponieważ próbka jest niewielka. Dla porównania podaję wynik rozwiązania „zawsze ham”: 0% wykrytego spamu i 0% fałszywych alarmów w obu wariantach.

## 5. Niewiadome
| Założenie | Sprawdzenie | Korekta |
|---|---|---|
| Niemal kopie zawyżają recall | różnica recall 60 vs 54 | duża różnica mówi że wynik na całej walidacji jest zawyżony |
| Model nie opiera się na tagach list / adresach | najsilniejsze słowa po uczeniu (train) | tagi i adresy zostają, opisać jako ograniczenie |
| Rzadkie słowa nie dominują | najsilniejsze słowa po uczeniu (train) | słownik zostaje pełny, opisać jako ograniczenie |
| Proporcja klas 1/3 z train nie zaniża recall | na train: ile decyzji zmienia się przy równej proporcji | opisać, zmiana tylko jako wniosek na przyszłość |
| Bayes z TF-IDF i domyślnym wygładzaniem działa | dokumentacja przed implementacją | jeśli wymaga zliczeń: zliczenia zamiast TF-IDF |
| Tokenizacja zachowuje $, usuwanie <…> nie kasuje adresów, litery spoza ASCII się nie rozpadają | test na train przed implementacją | poprawić reguły |
| Ham pochodzi tylko z easy_ham | opis korpusu | zaznaczyć, że wynik dla ham może być optymistyczny |