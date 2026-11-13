---
name: run-eval
description: Run the full evaluation (quality questions + red-team attacks) for one or more variants V0-V4 and record results in eval/results. Use when the team wants measurements for the results table.
---

# Pomiar wariantów

## Kiedy
- Potrzebne są liczby do tabeli „wariant × szczelność × jakość × koszt”.
- Po większej zmianie (nowa warstwa kontroli, inny model) trzeba sprawdzić wpływ.

## Kroki
1. Upewnij się, że pomiar odbywa się na **wspólnej VM pomiarowej**, a nie na laptopie (porównywalne opóźnienia).
2. Ustaw wariant w `ansible/group_vars/all/variants.yml` i wdroż:
   ```bash
   ansible-playbook -i ansible/inventory/measure.yml ansible/site.yml
   ```
3. Zapisz konfigurację pomiaru: commit SHA, wariant, model (np. `gemini-flash` lub model w Ollama), data.
4. Uruchom:
   - jakość: `python -m eval.quality --variant V2 --questions data/questions.yaml`
   - ataki: `npx promptfoo eval -c redteam/promptfooconfig.yaml --output eval/results/V2-redteam.json`
5. Zbierz metryki (definicje w `docs/architektura.md`, sekcja 8):
   - skuteczność ataków (kanarki w odpowiedzi, maile poza domenę),
   - precision / recall kontrolera wyjścia, fałszywe blokady,
   - ocena jakości (sędzia 1–5) + ręcznie sprawdzona próbka min. 10 odpowiedzi,
   - % ataków odtwarzalnych z `audit_events`,
   - tokeny i opóźnienie na pytanie (Langfuse).
6. Dopisz wiersz do `eval/results/summary.csv`:
   `date,commit,variant,model,attack_success_rate,controller_precision,controller_recall,false_block_rate,quality_avg,traceability,avg_tokens,avg_latency_ms`
7. Każdy udany atak przekaż do skilla `attack-regression`.

## Limity darmowego Gemini
- Limity są niskie i liczone na projekt. Dodaj opóźnienie między zapytaniami i ponawianie po błędzie 429.
- Nie uruchamiaj kilku wariantów równolegle na tym samym kluczu.
- Jeśli limit się skończy, dokończ następnego dnia. Nie mieszaj w jednym wierszu wyników z dwóch modeli.

## Zasady
- Nie zmieniaj pytań ani wzorców w trakcie serii pomiarów. Zmiana zbioru = nowa seria od V0.
- Wyniki surowe (`*.json`) zostają w repo. To dowód do pracy.
