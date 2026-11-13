---
name: opa-policy
description: Add or change an OPA (Rego) access policy for agent tools in policies/, always together with tests. Use when changing who can use which tool or access which document classification.
---

# Reguły dostępu OPA dla narzędzi agenta

## Kiedy
- Dodajemy nowe narzędzie agenta.
- Zmieniamy, kto ma dostęp do jakiej klasyfikacji dokumentów.
- Atak przeszedł przez warstwę OPA i trzeba to załatać.

## Kroki
1. Przeczytaj `docs/architektura.md`, sekcja 5.4 (format wejścia i przykłady).
2. Edytuj lub dodaj plik w `policies/` (pakiet `agent.tools`).
3. **Zawsze** dodaj lub zaktualizuj testy w `policies/*_test.rego`:
   - co najmniej jeden test `allow` dla uprawnionej roli,
   - co najmniej jeden test `deny` dla nieuprawnionej roli,
   - test przypadku brzegowego (brak roli, nieznane narzędzie, pusta klasyfikacja).
4. Uruchom:
   ```bash
   opa fmt -w policies/
   opa test policies/ -v
   ```
5. Jeśli zmiana dotyczy klasyfikacji dokumentów, sprawdź spójność z polityką RLS w Postgresie (tabela `role_permissions`). OPA i RLS muszą mówić to samo.

## Zasady
- `default allow := false`. Nigdy nie zmieniaj tego na `true`.
- Reguły dostępu żyją tylko w OPA i RLS, nigdy w prompcie modelu.
- Każda reguła ma komentarz po angielsku, co dopuszcza.
- Odmowa powinna zwracać powód (`reason`), który trafia do `audit_events`.

## Przykład testu

```rego
package agent.tools_test

import data.agent.tools

# Service staff must not read HR documents
test_service_cannot_read_hr if {
    not tools.allow with input as {
        "user": {"id": "u1", "roles": ["serwis"]},
        "agent": {"id": "agent-assistant"},
        "tool": "czytaj_dokument",
        "resource": {"id": "doc-1", "classification": "hr_confidential"}
    }
}
```
