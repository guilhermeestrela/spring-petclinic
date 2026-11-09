# C4 — Contexto

🟢 CONFIRMADO — Sistema e bancos derivados dos manifests/configurações. 🟡 INFERIDO — Persona de operação da clínica; não há papel autenticado implementado. MySQL e PostgreSQL são alternativas, não dependências simultâneas. H2 pertence ao processo da aplicação.

```mermaid
flowchart LR
    U[Pessoa operando a clínica: persona inferida] -->|HTTP: páginas, formulários e dados| S[Spring PetClinic]
    S -->|JDBC: perfil mysql| M[(MySQL)]
    S -->|JDBC: perfil postgres| P[(PostgreSQL)]
    O[Operação técnica: persona inferida] -->|HTTP: Actuator e probes conforme implantação| S
```

Fontes: README.md, docker-compose.yml, k8s/*.yml, application*.properties e controllers. Sem integração de negócio externa adicional identificada. Persona responsável pelo animal não é conta de usuário no sistema.
