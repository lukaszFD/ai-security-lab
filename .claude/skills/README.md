# Skille dla Claude Code

Claude Code automatycznie wykrywa skille z tego katalogu, gdy pracujemy w repozytorium.

| Skill | Do czego |
|---|---|
| `adr` | zapis decyzji architektonicznej w `docs/adr/` |
| `opa-policy` | zmiana reguł dostępu OPA zawsze razem z testami |
| `attack-regression` | udany atak → trwały test regresji w promptfoo |
| `ansible-role` | zmiany we wdrożeniu zgodne z konwencją `[NN.S.T]` |
| `run-eval` | pomiar wariantów V0–V4 do tabeli wyników |

Wywołanie ręczne: `/adr`, `/opa-policy` itd. Można też po prostu opisać zadanie, a Claude sam dobierze skill.
