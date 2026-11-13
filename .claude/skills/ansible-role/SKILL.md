---
name: ansible-role
description: Add or modify an Ansible role or task in ansible/ following the team conventions ([NN.S.T] task names, idempotency, no secrets in Git). Use for any deployment or environment setup change.
---

# Ansible: role i taski

## Kiedy
- Nowa usługa w stacku, zmiana konfiguracji Vault/Keycloak/Postgres/OPA.
- Nowa zmienna przełączająca wariant (V0–V4).

## Konwencje
- Role numerowane: `01_common`, `02_docker`, `03_stack`, `04_vault`, `05_keycloak`, `06_data`, `07_opa`. Nowa rola dostaje kolejny numer.
- **Nazwy tasków: `[NN.S.T] Opis po angielsku`**
  - `NN`: numer roli,
  - `S`: numer sekcji w roli (pliki lub bloki logiczne),
  - `T`: numer taska w sekcji.
  - Przykład: `[04.2.1] Enable database secrets engine`.
- Komentarze w kodzie po angielsku.
- Taski muszą być **idempotentne**: drugie uruchomienie nie zmienia niczego (`changed=0`). Unikaj gołego `command`/`shell`. Jeśli są konieczne, użyj `creates`, `changed_when` albo sprawdzenia stanu.
- Przełączniki wariantów tylko w `ansible/group_vars/all/variants.yml`, np.:

```yaml
variant_delegation_enabled: true
variant_opa_enabled: true
variant_output_controller_enabled: false
variant_mail_approval_enabled: false
```

## Sekrety
- Żadnych sekretów w repo. Klucz Gemini każdy podaje sam (zmienna środowiskowa lub `ansible-vault` lokalnie, plik w `.gitignore`).
- Klucze unseal i root token Vaulta zapisuj tylko na VM (plik z prawami `0600`), nigdy w repo ani w logach. Użyj `no_log: true`.

## Przykład

```yaml
- name: "[04.2.1] Enable database secrets engine"
  community.hashi_vault.vault_write:
    path: sys/mounts/database
    data:
      type: database
  no_log: true
  register: db_engine
  failed_when: db_engine.failed and 'path is already in use' not in (db_engine.msg | default(''))
  changed_when: not db_engine.failed
```

## Weryfikacja
```bash
ansible-lint ansible/
ansible-playbook -i ansible/inventory/<vm>.yml ansible/site.yml --check --diff
ansible-playbook -i ansible/inventory/<vm>.yml ansible/site.yml   # 2x: second run must report changed=0
```

## Zasady
- Nie refaktoryzuj istniejących ról bez wyraźnej zgody. Najpierw opisz zauważony problem.
- Każda zmiana w stacku musi działać od zera na czystej VM z Ubuntu.
