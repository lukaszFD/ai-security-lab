# ai-security-lab

**Asystent AI na danych wrażliwych: ile jakości kosztuje ochrona?**

Projekt zespołowy na studia podyplomowe „Architektura AI w organizacji”. Budujemy asystenta AI (RAG + agent z narzędziami) na fikcyjnych dokumentach firmy i mierzymy, jak kolejne warstwy ochrony prywatności i kontroli agenta wpływają na szczelność, jakość odpowiedzi i koszt.

> Pracownik serwisu pyta asystenta o coś niewinnego i dostaje fragment notatki HR o czyjejś pensji. Pracownik nie miał do niej dostępu, ale agent miał. Przy okazji cała notatka, z PESEL-em, poszła w prompcie do zewnętrznego API.

Właśnie taki scenariusz chcemy wykryć, zablokować i zmierzyć.

## Warstwy ochrony

Warianty są kumulatywne: każdy dokłada jedną warstwę do poprzedniego.

| Wariant | Co dochodzi |
|---|---|
| V0 Baseline | brak ochrony |
| V1 Tożsamość | agent działa w imieniu użytkownika (delegacja z Keycloak), RLS w PostgreSQL filtruje dokumenty po roli |
| V2 Reguły | każde wywołanie narzędzia autoryzowane w OPA, poza modelem |
| V3 Pseudonimizacja | Presidio (PL) zamienia dane osobowe na tokeny przed wysłaniem do modelu, sejf szyfrowany przez Vault Transit |
| V4 Kontrola wyjścia | kontroler sprawdza źródła, dane osobowe i linki zewnętrzne przed oddaniem odpowiedzi |
| V5 Człowiek w pętli | wysłanie maila wymaga akceptacji |

Każdy wariant uruchamiamy na **Gemini Flash** (model zewnętrzny) albo na **Ollamie** (model lokalny). Przez wszystkie warstwy przechodzi **audyt** (kto pytał, co agent pobrał, co oddał), na którym opiera się **przegląd uprawnień agenta**: co ma przyznane, a czego naprawdę używa.

Zasada: decyzje dostępowe nigdy nie trafiają do promptu, tylko do OPA, RLS i kontrolera wyjścia.

## Jak mierzymy

- **Atakujący** (LLM + promptfoo) udaje pracownika i próbuje wyciągnąć dane, do których nie ma dostępu. Wycieki wykrywamy regułowo, po kanarkach ukrytych w dokumentach poufnych.
- **Sędzia** (LLM-as-judge) ocenia jakość odpowiedzi na zbiorze pytań z wzorcami, z ręczną weryfikacją próbki.

| Metryka | Co pokazuje |
|---|---|
| Skuteczność ataków | % scenariuszy, w których kanarek wyciekł lub mail wyszedł poza domenę |
| Wyciek do dostawcy | dane osobowe i kanarki w promptach do zewnętrznego API |
| Wykrywanie danych osobowych | precision / recall Presidio na polskim tekście, z odmianą nazwisk |
| Skuteczność kontrolera i fałszywe blokady | ile wycieków złapał, ile poprawnych odpowiedzi zablokował |
| Jakość | ocena sędziego 1–5 |
| Nadmiar uprawnień | uprawnienia przyznane, a nieużywane, przed przeglądem i po nim |
| Rozliczalność | % ataków w pełni odtwarzalnych z audytu |
| Koszt i opóźnienie | tokeny i ms na pytanie, z podziałem na warstwy |

Wynik końcowy: tabela „wariant × szczelność × jakość × koszt”.

## Stos

Python + `google-genai` (bez frameworka agentowego), Gemini Flash, Ollama, PostgreSQL + pgvector + RLS, HashiCorp Vault, Keycloak, Open Policy Agent, Presidio, promptfoo, Langfuse, Mailpit. Całość w Docker Compose, wdrażana jednym playbookiem Ansible.

## Dokumentacja

- [`docs/kto-pilnuje-agenta.md`](docs/kto-pilnuje-agenta.md): opis pomysłu, podział pracy, harmonogram.
- [`docs/architektura.md`](docs/architektura.md): architektura (C4, przepływ pytania, komponenty, threat model OWASP Top 10 for LLM, RODO, docelowa struktura repo).
- `docs/adr/`: decyzje architektoniczne.
- `subject/`: materiały i zadania z zajęć.

## Status

Etap projektowania: architektura i opis pomysłu są gotowe, kod, polityki i środowisko powstają etapami według `docs/architektura.md`.

## Zespół

Łukasz Dejko, Bartosz Lepa, Jaromir Komski, Piotr Czeczko.

Wszystkie dane w projekcie są fikcyjne. Licencja: MIT.
