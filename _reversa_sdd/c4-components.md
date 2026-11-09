# C4 — Componentes

🟢 CONFIRMADO — Dependências de chamadas, herança e configuração extraídas dos imports, injeções e anotações. Model é compartilhado; system fornece infraestrutura transversal, não um serviço remoto.

```mermaid
flowchart TD
    WEB[WebConfiguration: locale da sessão e interceptor] --> MVC[Pipeline Spring MVC]
    MVC --> OC[OwnerController]
    MVC --> PC[PetController]
    MVC --> VC[VisitController]
    MVC --> VET[ VetController ]
    MVC --> HOME[WelcomeController e CrashController]
    OC --> OR[OwnerRepository]
    PC --> OR
    VC --> OR
    PC --> PTR[PetTypeRepository]
    PC --> PV[PetValidator]
    MVC --> FMT[PetTypeFormatter]
    FMT --> PTR
    VET --> VR[VetRepository]
    VR --> CACHE[Cache vets]
    CC[CacheConfiguration] -. customizador JCache .-> CACHE
    OR --> OWN[Owner e Pet e Visit]
    PTR --> TYPE[PetType]
    VR --> VE[Vet e Specialty e Vets]
    OWN --> MODEL[BaseEntity e Person e NamedEntity]
    VE --> MODEL
    TYPE --> MODEL
    OC --> TH[Templates Thymeleaf]
    PC --> TH
    VC --> TH
    VET --> TH
    HOME --> TH
```

🟢 CONFIRMADO — PetClinicApplication inicia o contexto e importa PetClinicRuntimeHints. Hints referenciam BaseEntity, Person e Vet para serialização. 🔴 LACUNA — Ativação concreta de JCache e conversores HTTP de XML não foi executada; seta pontilhada representa customização declarada, não confirmação de provedor ativo.
