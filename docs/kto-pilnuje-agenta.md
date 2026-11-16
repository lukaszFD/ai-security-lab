# Asystent AI na danych wrażliwych: ile jakości kosztuje ochrona?

Wspólny projekt zespołu (połączone propozycje Bartka i Łukasza). Projekt na studia podyplomowe z architektury AI. Największą wartością są decyzje architektoniczne i ich zmierzone skutki, a nie sam kod.

## Pomysł

Budujemy asystenta AI (RAG + agent z narzędziami) na fikcyjnych dokumentach firmy: zgłoszeniach, notatkach HR, mailach i umowach. Patrzymy na niego z dwóch stron:

- **ochrona danych**: jak sprawić, żeby dane wrażliwe nie wyciekły do dostawcy modelu ani do niepowołanych osób,
- **kontrola agenta**: jak sprawdzić, że agent nie ma za dużych uprawnień i nie oddaje w odpowiedziach rzeczy, których pytający nie powinien zobaczyć.

**Pytanie badawcze:** ile jakości i kosztu kosztuje każdy poziom ochrony prywatności i kontroli agenta?

**Przykład problemu:** pracownik serwisu pyta asystenta o coś niewinnego i dostaje fragment notatki HR o czyjejś pensji. Pracownik nie miał do niej dostępu, ale agent miał. Przy okazji cała notatka, z PESEL-em, poszła w prompcie do zewnętrznego API.

## Warstwy ochrony

Każdą warstwę można włączyć lub wyłączyć, więc widać, co która daje.

1. **Tożsamość agenta i uprawnienia do dokumentów.** Agent działa w imieniu użytkownika i widzi tylko to, co ten użytkownik. Wyszukiwanie jest filtrowane po roli (HR widzi więcej niż serwis).
2. **Reguły dostępu do narzędzi (OPA).** Każde użycie narzędzia (szukaj, czytaj, wyślij maila) jest sprawdzane poza modelem.
3. **Pseudonimizacja.** Dane osobowe są zamieniane na tokeny, zanim trafią do zewnętrznego modelu. Mapowanie leży w zaszyfrowanym „sejfie” w Postgresie. Na wyjściu tokeny wracają do postaci jawnej, ale tylko te, do których pytający ma prawo.
4. **Kontrola wyjścia (guardrails).** Przed wysłaniem odpowiedzi sprawdzamy źródła, dane osobowe i linki lub obrazki zewnętrzne (kanał wycieku).
5. **Człowiek w pętli.** Wysłanie maila wymaga akceptacji.

Dodatkowo porównujemy **model zewnętrzny** (Gemini Flash) z **modelem lokalnym** (Ollama), przy którym dane nie opuszczają maszyny.

Do tego **audyt**: z logów da się odtworzyć, kto pytał, co agent pobrał i co oddał. Na tej podstawie robimy **przegląd uprawnień agenta**: co ma przyznane, a czego naprawdę używa.

## Jak to działa

- **Asystent** (Gemini Flash lub Ollama) odpowiada pracownikom i korzysta z narzędzi: szukaj dokumentów, czytaj dokument, wyślij maila.
- **Atakujący** (LLM + promptfoo) udaje pracownika i próbuje wyciągnąć dane, do których nie ma dostępu.
- **Sędzia** (LLM-as-judge) ocenia jakość odpowiedzi. Wycieki wykrywamy regułowo, po „kanarkach” ukrytych w dokumentach poufnych.

## Mierzymy

- **Szczelność:** odsetek udanych ataków.
- **Wyciek do dostawcy:** ile danych osobowych trafiło w promptach do zewnętrznego API.
- **Jakość odpowiedzi:** 30–50 pytań z wzorcowymi odpowiedziami, ocena sędziego i ręczna próbka.
- **Wykrywanie danych osobowych:** precision i recall Presidio na polskim tekście, w tym odmiana nazwisk.
- **Skuteczność kontroli wyjścia:** ile wycieków wyłapała, ile poprawnych odpowiedzi zablokowała niepotrzebnie.
- **Koszt i opóźnienie:** ile dokłada każda warstwa.

Wynik końcowy: tabela „wariant × szczelność × jakość × koszt”, czyli kompromisy poparte danymi.

## Elementy architektoniczne w pracy

- Diagramy C4: kontekst, kontenery, przepływ danych z granicami zaufania.
- ADR-y: 8–12 krótkich decyzji, np. „dlaczego filtrowanie uprawnień przy wyszukiwaniu, a nie po nim”.
- Threat model według OWASP Top 10 for LLM: przed czym chronimy i czego świadomie nie obejmujemy.
- Aspekt RODO: dlaczego pseudonimizacja to nie anonimizacja (wątek krytyczny).

## Stos (wszystko darmowe, w Dockerze)

| Do czego | Narzędzie |
|---|---|
| Kod i agenci | Python + SDK Gemini, bez frameworka |
| Model | Gemini Flash (darmowe API), Ollama do porównania |
| Baza i wyszukiwanie | PostgreSQL + pgvector, Row-Level Security |
| Sejf pseudonimów | tabela w Postgresie, szyfrowana przez Vault Transit |
| Sekrety | HashiCorp Vault |
| Tożsamość i role | Keycloak |
| Reguły dostępu | Open Policy Agent |
| Dane osobowe | Presidio (+ PESEL, NIP, polska odmiana) |
| Ataki | promptfoo |
| Logi i ślady | Langfuse |
| Wdrożenie | Ansible + Docker Compose |

Świadomie pomijamy: LangChain i LlamaIndex (czysty kod daje lepszą naukę i pełną kontrolę nad tym, co trafia do modelu), Qdrant (pgvector daje RLS w tej samej bazie), n8n (powtarzalne pomiary łatwiej robić w Pythonie), Databricks i SQL Server (na tę skalę tylko komplikują).

## Jak pracujemy

- **Jedno polecenie Ansible** stawia identyczne środowisko na VM u każdego. Końcowe pomiary robimy na jednej wspólnej maszynie.
- **GitHub z pull requestami** i automatycznymi testami: wycieki sekretów (gitleaks), podatności (Trivy), testy kodu.
- **Każdy znaleziony wyciek staje się testem.** Jeśli późniejsza zmiana znowu go otworzy, PR nie przejdzie.

## Podział pracy

Jeden system, jedne dane, jeden atakujący i jedna tabela wyników. Każda para odpowiada za swoje warstwy.

| Obszar | Zakres | Osoby |
|---|---|---|
| Ochrona danych (Bartek) | pseudonimizacja i sejf, Presidio po polsku, model lokalny, RODO | 2 |
| Kontrola agenta (Łukasz) | tożsamość i delegacja, OPA, kontrola wyjścia, audyt, przegląd uprawnień | 2 |
| Wspólne | dane syntetyczne, baseline, pytania testowe, atakujący, ewaluacja | wszyscy |

Łukasz pracuje w IAM/PAM w banku, w tym nad bezpieczeństwem kont technicznych i agentów AI.

## Harmonogram (ok. 10 tygodni)

| Tydzień | Etap |
|---|---|
| 1–2 | Środowisko (Ansible, Docker), dane syntetyczne, baseline RAG z agentem |
| 3 | Pytania testowe, sędzia, pierwszy pomiar V0 |
| 4–5 | Tożsamość agenta, uprawnienia do dokumentów (RLS), OPA, pierwszy atakujący |
| 6–7 | Pseudonimizacja z sejfem i polską odmianą, Presidio |
| 8 | Kontrola wyjścia, akceptacja maili, model lokalny |
| 9 | Pełne pomiary wszystkich wariantów, przegląd uprawnień |
| 10 | Analiza wyników, ADR-y, prezentacja |
