# C4 — Containers

🟢 CONFIRMADO — Um executável Spring Boot atende HTML e dados; não há frontend SPA independente. Os caches e H2 são bibliotecas/armazenamento no processo, não containers de implantação separados. Provedor de cache efetivo permanece 🔴 LACUNA.

```mermaid
flowchart LR
    B[Navegador] -->|HTTP| APP
    subgraph PROCESSO[Processo Spring Boot Java 17]
      APP[MVC e Thymeleaf]
      JPA[Spring Data JPA]
      CACHE[Abstração cache vets: provedor a validar]
      H2[(H2 em memória: perfil padrão)]
      APP --> JPA
      JPA --> H2
      JPA --> CACHE
    end
    JPA -->|JDBC perfil alternativo| MYSQL[(MySQL)]
    JPA -->|JDBC perfil alternativo| PG[(PostgreSQL)]
```

🟢 CONFIRMADO — Processos locais usam Maven/Gradle; Kubernetes fornece app e PostgreSQL, enquanto Compose só declara bancos. Não há Redis, mensageria, serviço de autenticação ou armazenamento de objetos identificado no escopo.
