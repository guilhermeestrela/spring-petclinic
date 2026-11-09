# Dicionário de dados

## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


Escavação dos módulos owner, vet, model e system concluída. 🟢 CONFIRMADO por entidades Java e schemas em src/main/resources/db/{h2,mysql,postgres}/schema.sql. Obrigatoriedade de Bean Validation não equivale a NOT NULL SQL.

| Entidade / campo | Tipo Java | Regra de aplicação / padrão | Persistência |
|---|---|---|---|
| BaseEntity.id (herdado) | Integer | null em novo; isNew testa null | PK identity/autoincrement |
| Person.firstName / lastName | String | @NotBlank, máximo 30, padrão null | first_name / last_name; VARCHAR(30) H2/MySQL, TEXT PostgreSQL; SQL aceita null |
| NamedEntity.name | String | @NotBlank, padrão null | name; limites dependem da tabela |
| Owner.address | String | @NotBlank, máximo 255 | owners.address VARCHAR(255) H2/MySQL, TEXT PostgreSQL |
| Owner.city | String | @NotBlank, máximo 80 | owners.city VARCHAR(80) H2/MySQL, TEXT PostgreSQL |
| Owner.telephone | String | @NotBlank, regex de exatamente 10 dígitos | owners.telephone VARCHAR(20) H2/MySQL, TEXT PostgreSQL |
| Owner.pets | List<Pet> | ArrayList vazio, deduplicação por instância/ID persistido | OneToMany ALL EAGER, pets.owner_id; ordenado name |
| Pet.name (herdado) | String | formulário: texto obrigatório, máximo 30; único por owner sem diferenciar caixa na aplicação | pets.name: VARCHAR_IGNORECASE(30) H2, VARCHAR(30) MySQL, TEXT PostgreSQL |
| Pet.birthDate | LocalDate | formulário: obrigatório e não futuro; yyyy-MM-dd | pets.birth_date DATE, SQL aceita null |
| Pet.type | PetType | requerido por PetValidator apenas em novo | ManyToOne type_id NOT NULL, FK types |
| Pet.visits | Set<Visit> | LinkedHashSet vazio; não deduplica por dados de negócio | OneToMany ALL EAGER, visits.pet_id; date ASC |
| PetType.name (herdado) | String | @NotBlank; formatter usa nome exato | types.name VARCHAR(80) H2/MySQL, TEXT PostgreSQL |
| Visit.date | LocalDate | padrão amanhã; rejeita hoje/passado apenas se não nulo; yyyy-MM-dd | visits.visit_date DATE, nullable |
| Visit.description | String | @NotBlank, máximo 255 | visits.description VARCHAR(255) H2/MySQL, TEXT PostgreSQL |
| Vet.specialties | Set<Specialty> | HashSet inicializado sob demanda; getter expõe lista ordenada | ManyToMany EAGER via vet_specialties |
| Specialty.name (herdado) | String | @NotBlank, padrão null | specialties.name VARCHAR(80) H2/MySQL, TEXT PostgreSQL |
| Vets.vets | List<Vet> | null até primeiro getVetList; depois ArrayList | envelope, não é entidade JPA |

## Chaves, índices e diferenças

- owners, pets, types, visits, vets e specialties têm PK id. BaseEntity usa Integer com estratégia IDENTITY.
- pets.owner_id e visits.pet_id são nullable no SQL; pets.type_id não é nullable. Relações de owner/pet são unidirecionais via join column, sem propriedade owner em Pet ou pet em Visit.
- Unicidade por responsável e nome: constraint nomeada H2; índice LOWER(name) PostgreSQL; UNIQUE sem nome explícito em MySQL. Semântica de collation MySQL não confirmada.
- vet_specialties exige IDs não nulos e FKs. MySQL/PostgreSQL declaram UNIQUE(vet_id,specialty_id); H2 não declara essa unicidade, embora a coleção Java seja Set.
- Índices de sobrenome de owners/vets, nome de types/specialties/pets e chaves de relacionamento variam por perfil; ver schemas para inventário SQL literal.
- Constantes owner: pageSize=5, MAX_NAME_LENGTH=30, REQUIRED="required" e caminhos de formulário. vet também pagina em 5. Nenhuma feature flag foi encontrada nestes três módulos.

🔴 LACUNA — Validar em execução visita sem data, colisão de nomes sob concorrência, collation/mensagem MySQL e serialização dos relacionamentos. Dados seed não representam regras exaustivas do domínio.

## Configurações e dados transitórios de system

🟢 CONFIRMADO — Este módulo não cria tabelas próprias.

| Campo ou chave | Tipo | Padrão / uso | Fonte |
|---|---|---|---|
| locale da sessão | Locale | inglês; alterável pelo parâmetro lang | system/WebConfiguration.java |
| nome do cache | String | vets | system/CacheConfiguration.java; vet/VetRepository.java |
| estatísticas JCache | boolean | true na configuração criada | system/CacheConfiguration.java |
| database | String | h2; perfis mysql/postgres substituem | application*.properties |
| spring.jpa.hibernate.ddl-auto | String | none | application.properties |
| spring.jpa.open-in-view | boolean | false | application.properties |
| default_batch_fetch_size | inteiro | 16 | application.properties |
| cache HTTP estático | duração | 12h | application.properties |
| exposição Actuator | padrão de seleção | *; não é garantia de autorização | application.properties |
| datasource URL / usuário / senha | String | valores padrão de desenvolvimento, substituíveis por ambiente | application-mysql.properties; application-postgres.properties |
| minVisitDate | LocalDate | amanhã, atributo de model; não persistido | owner/VisitController.java |
| page / currentPage | int | página externa inicia em 1; tamanho 5 | OwnerController.java; VetController.java |

🔴 LACUNA — Não há TTL/capacidade de cache ou fuso explícito para LocalDate.now no código inspecionado. O comportamento temporal usa o relógio/fuso padrão da JVM.
