# Kto pilnuje agenta? Kontrola uprawnień i tego, co agent AI ujawnia

Propozycja: Łukasz, uzupełnienie tematu Bartka

## Pomysł

Bartek buduje asystenta AI na dokumentach firmy i pokazuje, **jak chronić dane**. Ja dokładam drugą stronę: **jak sprawdzić, że ta ochrona działa**. Pytam, czy agent nie ma za dużych uprawnień i czy nie oddaje w odpowiedziach rzeczy, których pytający nie powinien zobaczyć.

**Przykład problemu:** pracownik serwisu pyta asystenta o coś niewinnego i dostaje fragment notatki HR o czyjejś pensji. Pracownik nie miał do niej dostępu, ale agent miał.

## Co sprawdzamy

1. **Tożsamość agenta.** Agent działający w imieniu użytkownika widzi tylko to, co widzi ten użytkownik.
2. **Przegląd uprawnień.** Porównujemy, do czego agent ma dostęp, z tym, czego faktycznie używa. Nadmiar usuwamy.
3. **Kontrola wyjścia.** Przed wysłaniem sprawdzamy, czy odpowiedź nie zawiera danych spoza uprawnień ani danych osobowych.
4. **Audyt.** Z logów da się odtworzyć, kto pytał, co agent pobrał i co oddał.

## Jak to działa

- **Asystent** (Gemini Flash) odpowiada pracownikom i korzysta z kilku narzędzi: szukaj dokumentów, czytaj dokument, wyślij maila.
- **Atakujący** (Gemini Flash) udaje pracownika i próbuje wyciągnąć cudze dane.
- **Sędzia** (Gemini Flash) ocenia jakość odpowiedzi i to, czy doszło do wycieku.
- Każde użycie narzędzia sprawdzają **reguły dostępu (OPA)** na podstawie roli pracownika.
- **Kontroler wyjścia** (bez AI) sprawdza odpowiedź przed wysłaniem.

Każdą warstwę kontroli można włączyć lub wyłączyć, więc widać, co która daje.

**Mierzymy:**
- ile ataków się udało,
- ile wycieków wyłapała kontrola,
- jak bardzo spadła jakość odpowiedzi,
- ile kosztuje i jak spowalnia każda warstwa.

Wynik to jedna tabela kompromisów.

## Stos (wszystko darmowe, w Dockerze)

| Do czego | Narzędzie |
|---|---|
| Kod i agenci | Python + SDK Gemini |
| Model | Gemini Flash (darmowe API), Ollama do porównania |
| Baza i wyszukiwanie | PostgreSQL + pgvector, Row-Level Security |
| Sekrety | HashiCorp Vault |
| Tożsamość i role | Keycloak |
| Reguły dostępu | Open Policy Agent |
| Dane osobowe | Presidio (+ PESEL, NIP) |
| Ataki | promptfoo |
| Logi i ślady | Langfuse |
| Wdrożenie | Ansible + Docker Compose |

n8n pomijamy, bo powtarzalne pomiary łatwiej robić w czystym Pythonie.

## Jak pracujemy

- **Jedno polecenie Ansible** stawia identyczne środowisko na VM u każdego. Końcowe pomiary robimy na jednej wspólnej maszynie.
- **GitHub z pull requestami** i automatycznymi testami: wycieki sekretów (gitleaks), podatności (Trivy), testy kodu.
- **Każdy znaleziony wyciek staje się testem.** Jeśli późniejsza zmiana znowu go otworzy, PR nie przejdzie.

## Połączenie z tematem Bartka

| Część | Pytanie | Osoby |
|---|---|---|
| Ochrona danych (Bartek) | Jak zabezpieczyć dane asystenta? | 2 |
| Kontrola agenta (ja) | Czy agent nie widzi i nie oddaje za dużo? | 2 |

Wspólne są dane, asystent, atakujący i tabela wyników.

Pracuję w IAM/PAM w banku, w tym nad bezpieczeństwem kont technicznych i agentów AI, więc tę część mogę wziąć na siebie.
