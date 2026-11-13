---
name: adr
description: Write or update an Architecture Decision Record in docs/adr. Use when the team makes or changes an architectural decision, or asks for an ADR.
---

# ADR: zapis decyzji architektonicznej

## Kiedy
- Zespół podjął decyzję, która wpływa na architekturę (wybór narzędzia, miejsce kontroli, model danych).
- Ktoś prosi o ADR albo pyta „dlaczego zrobiliśmy X”.

## Kroki
1. Przeczytaj `docs/architektura.md` (sekcja 10 zawiera listę planowanych ADR-ów) i istniejące pliki w `docs/adr/`.
2. Nadaj kolejny numer: `docs/adr/NNNN-krotki-tytul.md` (np. `0003-filtrowanie-przed-wyszukiwaniem.md`).
3. Wypełnij szablon poniżej. Maksymalnie około 1 strony.
4. Jeśli decyzja zastępuje wcześniejszą, zmień w starej status na `Zastąpiona przez NNNN`.
5. Jeśli decyzja zmienia architekturę, zaktualizuj też `docs/architektura.md`.

## Szablon

```markdown
# NNNN. Tytuł decyzji

- Status: Proponowana | Przyjęta | Zastąpiona przez NNNN
- Data: RRRR-MM-DD
- Autorzy: ...

## Kontekst
Jaki problem rozwiązujemy i jakie mamy ograniczenia (2–5 zdań).

## Rozważane opcje
1. Opcja A: plusy / minusy
2. Opcja B: plusy / minusy

## Decyzja
Co wybieramy, jednym zdaniem.

## Konsekwencje
- Pozytywne: ...
- Negatywne / koszty: ...
- Jak zmierzymy skutek (odwołanie do metryk z architektury, jeśli dotyczy).
```

## Zasady
- Pisz po polsku, prosto. Bez marketingu.
- Każda decyzja musi mieć co najmniej jedną konsekwencję negatywną. Decyzja bez kosztów to sygnał, że analiza była płytka.
- Odwołuj się do mierzalnych skutków (szczelność, jakość, koszt, opóźnienie), gdy to możliwe.
