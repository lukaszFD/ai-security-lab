# Praca domowa 1: lista kontrolna

Ostatnie sprawdzenie: 15.11.2026, na podstawie listy z Primera 01 (rozdział 4) i plików `01`, `02`, `03`.
Termin: **środa 18.11.2026**. Oddaje opiekun use case'u: zdjęcia albo skan stron karty i kanwy A3, jeden komplet na grupę, Discord, kanał przedmiotu 05, wątek zjazdu 1.

Legenda: ✅ spełnione · ⚠️ spełnione z uwagą · ❌ do zrobienia

## Lista z Primera (13 punktów)

| # | Punkt | Stan | Gdzie / uwagi |
|---|---|---|---|
| 1 | Strona tytułowa: system jednym zdaniem, tabela członków z rolą i oczekiwaniami **każdej** osoby | ❌ | System ✅. W tabeli tylko jedna osoba, brak nazwy grupy. Uzupełnia grupa |
| 2 | Hipoteza ma wszystkie sześć części, w tym „bez pogorszenia” | ✅ | A2: warstwy poza modelem · pracownicy, których dane są w dokumentach · spada odsetek udanych ataków · z [wynik V0] do 0 na 100 w V3 · 3 miesiące · fałszywe blokady ≤ 5% |
| 3 | Pytanie decyzyjne ma adresata i termin | ✅ | A4: steering committee programu agentów AI · zjazd 10 |
| 4 | Jest jeden błąd, który zmienia warunki, i wiadomo, co wtedy zmieniacie | ⚠️ | B3 obejmuje porażki 1 i 3 jako jeden błąd „wyciek danych” (w firmie lub poza nią) → zakres i autonomia. Formalnie wzór ma jedną porażkę; uzasadnienie: to ten sam rodzaj błędu |
| 5 | Są dwa pytania „Skąd wiemy, że…” z odpowiedziami | ✅ | `PYTANIA`: 2 pytania, statusy CD i Z |
| 6 | „Nie służy do” ma co najmniej trzy pozycje | ✅ | A3 |
| 7 | Każdy krok procesu docelowego ma oznaczenie AI, człowiek albo AI + człowiek | ⚠️ | A5: „automat (kontroler)” i „AI + automat” wychodzą poza trzy oznaczenia. Zgodne z prawdą (kontroler nie jest AI); wzorzec też używa słowa „automat” |
| 8 | Są dwa przypadki i jeden wzorcowy wynik; dane syntetyczne oznaczone | ✅ | A7 z `(synt.)` |
| 9 | Jest rola „ponosi skutek błędu” i porażka, w której system działa, a wartości nie ma | ✅ | A6 (pracownicy, kontrahenci) · B2.2 |
| 10 | Każda liczba ma znacznik, a przy CD i D źródło | ⚠️ | A6 naprawione (`Status: Z`). A7 (CD): przy „30–50 pytań” brak wskazania źródła, np. „(architektura zespołu)” |
| 11 | Każda osoba ma wkład osobisty z inicjałami, grupa przejrzała wszystkie | ❌ | Tylko jedna osoba. Brak inicjałów „(JK)” przy A5, krok „Wyjaśnienie incydentu” |
| 12 | Kanwa ma wypełnione wszystkie 11 pól | ✅ | `03-kanwa.md` |
| 13 | W treści karty nie ma nazwy organizacji, nazwisk z organizacji ani danych poufnych | ⚠️ | Fikcyjne „Anna Nowak” w przypadku trudnym, z oznaczeniem `(synt.)`: dozwolone, ale bezpieczniej „pracownicy X” |

## Pozostałe wymagania

| Wymaganie | Stan | Uwagi |
|---|---|---|
| Kanwa pola 3 i 5 = A2 i B3 w jednym zdaniu, ze znacznikiem | ✅ | spójne po zmianach |
| Pole 11 kanwy w wersji 0: pierwsze przeczucie i co może je zmienić | ❌ | jest „bez rekomendacji” i znak zapytania, **brak zdania o pierwszym przeczuciu** |
| Nazwa grupy na karcie i kanwie | ❌ | `[DO USTALENIA]` |
| Opiekun use case'u | ❌ | `[DO USTALENIA]` (opis use case'u) |
| Czytelność PDF | ⚠️ | A4 „Adresat”: czcionka 5,8 pt; można skrócić do „steering committee programu AI” |
| Co najmniej 2 godziny wspólnej pracy grupy | ❌ | wspólny przegląd przed wysłaniem |
| Forma oddania: zdjęcia lub skan | ⚠️ | mamy PDF wypełniony komputerowo; zapytać prowadzącego |

## Spójność między plikami 01, 02, 03 ✅
Polecenie (strategia 2027, steering committee), adresat decyzji, nazwa systemu, hipoteza z liczbami, porażki i B3 mówią to samo w opisie, karcie i kanwie.

## Do zrobienia

**Od ręki (Jaromir / Claude):**
2. A5: dopisać „(JK)” przy kroku „Wyjaśnienie incydentu”.
3. Pole 11 kanwy: dopisać pierwsze przeczucie.
4. Decyzje: „automat” w A5, źródło przy „30–50 pytań”, „Anna Nowak”, skrót adresata w A4.

**Grupa:**
5. Nazwa grupy, opiekun use case'u.
6. Pozostali członkowie: rola, oczekiwania, wkład osobisty z inicjałami przy polu.
7. Wspólny przegląd (min. 2 h).

**Na końcu:**
8. `python generuj_pdf.py`, sprawdzenie PDF, wysłanie na Discord.
