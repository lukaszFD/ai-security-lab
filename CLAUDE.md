# ai-security-lab: instrukcje dla Claude Code

Projekt zespołowy na studia podyplomowe „Architektura AI w organizacji”. Asystent AI (RAG + agent z narzędziami) na fikcyjnych dokumentach firmy. Mierzymy, jak warstwy kontroli (tożsamość agenta, OPA, RLS, kontroler wyjścia, audyt) wpływają na szczelność, jakość i koszt.

## Najważniejsze dokumenty
- `docs/architektura.md`: architektura techniczna. Czytaj przed każdą większą zmianą.
- `docs/kto-pilnuje-agenta.md`: krótki opis pomysłu dla zespołu.
- `docs/adr/`: decyzje architektoniczne.

## Zasady bezwzględne
- **Nigdy nie rób `git push`.** Pushuje tylko człowiek. Commity lokalne tylko na prośbę.
- **Nie refaktoryzuj bez wyraźnej zgody.** Najpierw opisz zauważony problem jako obserwację, potem czekaj na decyzję.
- **Żadnych sekretów w repo** (klucze API, hasła, tokeny Vaulta). Sekrety tylko w Vaulcie lub w lokalnych plikach z `.gitignore`.
- **Decyzje dostępowe nigdy w prompcie modelu**, tylko w OPA, RLS i kontrolerze wyjścia.
- Tylko dane fikcyjne. Żadnych prawdziwych danych osobowych.

## Konwencje
- Dokumentacja i ADR-y po polsku, prosto. Bez nadmiaru linków.
- Komentarze w kodzie po angielsku.
- Ansible: nazwy tasków `[NN.S.T] Description`, taski idempotentne (skill `ansible-role`).
- OPA: każda zmiana polityki z testami (skill `opa-policy`).
- Udany atak → test regresji (skill `attack-regression`).
- Python: `ruff` do formatowania i lintowania, testy w `pytest`.

## Stos
Python + SDK Gemini (`google-genai`), Gemini Flash (darmowe API), Ollama (porównanie), PostgreSQL + pgvector + RLS, HashiCorp Vault, Keycloak, Open Policy Agent, Presidio, promptfoo, Langfuse, Mailpit, Docker Compose, Ansible.

## Praca krok po kroku
Budujemy etapami. Zanim zaczniesz nowy etap, sprawdź w repo, co już istnieje, i trzymaj się struktury z sekcji 13 w `docs/architektura.md`. Jeśli coś w repo nie zgadza się z architekturą, zgłoś to, zamiast po cichu zmieniać.
