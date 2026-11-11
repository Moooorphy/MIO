# Dane do Laboratorium 2

Paczka służy do rozpoznania problemu klasyfikacji spamu i przygotowania planu rozwiązania. Zawiera dane oraz opis ich pochodzenia. Nie zawiera kodu, gotowych cech, modelu ani testów.

## Zawartość

- `data/train.csv` — wiadomości przeznaczone do uczenia.
- `data/validation.csv` — osobne wiadomości przeznaczone do oceny.
- `data/README.md` — opis kolumn, przygotowania danych i ograniczeń zbioru.
- `data/manifest.json` — informacje o pochodzeniu wiadomości i sumy kontrolne plików.

## Początek pracy

Otwórz folder `project/` w aplikacji agenta. Zapoznaj się z [opisem danych](data/README.md) i zleć agentowi odczyt plików CSV. Ustal, co zawierają i jakie obserwacje mają znaczenie dla planu.

Do odczytu danych nie trzeba instalować bibliotek ML. Wybór sposobu reprezentacji tekstu, modelu i miar oceny należy do zadania. Nie trzeba teraz implementować całego rozwiązania ani trenować końcowego modelu.

Zachowaj dostarczone pliki i podział danych. Jeśli wykonujesz małą próbę pomocną w podjęciu decyzji, zapisuj jej pliki osobno.
