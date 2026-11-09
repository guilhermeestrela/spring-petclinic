# Modelo compartilhado — Mapeamento do legado

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e evidência

🟢 CONFIRMADO — Unit model → modelo, relacionada ao checkout `500158f732419217507c7656904b8e6aa1bcc0d6`. Caminhos/linhas foram identificados nos arquivos locais. Links são relativos a este documento; arquivos de documentação não versionados não pertencem ao commit citado.

🟢 CONFIRMADO — Relação documental não significa teste executado ou cobertura medida. 🟡 Correspondência entre fonte e tarefa é plano de realização proposto. Nenhum código, schema ou comportamento de runtime foi alterado nesta etapa.

## Fontes próprias

| Arquivo | Responsabilidade confirmada | Requisitos/cenários | Tarefas propostas |
|---|---|---|---|
| 🟢 [BaseEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L1) | Identidade Integer/IDENTITY, Serializable e isNew por null | MOD-RF-01/04; MOD-CA-01–03 | 🟡 T-01/04/05; TT-01/04/05 |
| 🟢 [NamedEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L1) | Campo name, NotBlank, acesso direto e fallback textual | MOD-RF-02/04; MOD-CA-04–06 | 🟡 T-02/04; TT-02/03 |
| 🟢 [Person.java:1](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L1) | firstName/lastName com NotBlank e Size/Column 30 | MOD-RF-03/04; MOD-CA-07–09 | 🟡 T-03/04; TT-02/04 |
| 🟢 [package-info.java:1](../../src/main/java/org/springframework/samples/petclinic/model/package-info.java#L1) | Declaração e documentação do pacote; sem regra executável própria | n/a independente | 🟡 n/a; inventário |

## Índice de símbolos

🟢 CONFIRMADO — Declarações abaixo são índice lexical para navegação, sem pretensão de grafo de chamadas ou parser completo.

### BaseEntity.java

- 🟢 `getId` — [BaseEntity.java:39](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L39).
- 🟢 `setId` — [BaseEntity.java:43](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L43).
- 🟢 `isNew` — [BaseEntity.java:47](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L47).

### NamedEntity.java

- 🟢 `getName` — [NamedEntity.java:37](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L37).
- 🟢 `setName` — [NamedEntity.java:41](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L41).
- 🟢 `toString` — [NamedEntity.java:46](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L46).

### Person.java

- 🟢 `getFirstName` — [Person.java:41](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L41).
- 🟢 `setFirstName` — [Person.java:45](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L45).
- 🟢 `getLastName` — [Person.java:49](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L49).
- 🟢 `setLastName` — [Person.java:53](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L53).

## Consumidores por herança

| Arquivo | Base direta | Contrato herdado | Unit consumidora |
|---|---|---|---|
| 🟢 [Owner.java:50](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L50) | Person | id, firstName, lastName | responsaveis |
| 🟢 [Pet.java:46](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L46) | NamedEntity | id, name; formulário usa PetValidator próprio | responsaveis |
| 🟢 [PetType.java:28](../../src/main/java/org/springframework/samples/petclinic/owner/PetType.java#L28) | NamedEntity | id e name de catálogo | responsaveis |
| 🟢 [Visit.java:37](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L37) | BaseEntity | id; data/descrição pertencem à classe Visit | responsaveis |
| 🟢 [Vet.java:45](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L45) | Person | id, firstName, lastName e capacidade Serializable herdada | veterinarios |
| 🟢 [Specialty.java:30](../../src/main/java/org/springframework/samples/petclinic/vet/Specialty.java#L30) | NamedEntity | id, name; getter de Vet usa nome para ordenar | veterinarios |

🟢 CONFIRMADO — Person e NamedEntity herdam BaseEntity separadamente. As três são MappedSuperclass, não tabelas próprias; não há entidade/tabela BaseEntity, Person ou NamedEntity nos schemas analisados. Não confundir classe base compartilhada com serviço HTTP.

## Evidências de teste e integração

| Origem | Evidência confirmada | Limite |
|---|---|---|
| 🟢 [ValidatorTests.java:1](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L1) | Dois testes de firstName/lastName vazios: uma violação, path correto, mensagem inglesa | Não cobre todos os limites de Size/Unicode ou todos os consumidores |
| 🟢 [VetTests.java:1](../../src/test/java/org/springframework/samples/petclinic/vet/VetTests.java#L1) | serialization compara id/firstName/lastName de Vet após round trip Java | Sem especialidades, versões antigas, JSON/XML ou execução nativa |
| 🟢 [ClinicServiceTests.java:1](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L1) | Testes JPA consumidores de Owner/Vet/Pet/Visit e geração de ID de Pet | Não é teste isolado da superclasse; perfil e execução não comprovados nesta extração |
| 🟢 [PetClinicRuntimeHints.java:1](../../src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java#L1) | withJavaSerialization para BaseEntity, Person e Vet | Configuração declarada não prova build nativo aprovado |
| 🟢 [PetValidator.java:1](../../src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java#L1) | Limite 30 de Pet, separado de NamedEntity | Não generalizar a todos os nomes compartilhados |
| 🟢 [Owner.java:1](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L1) | addPet deduplica instância/ID persistido explicitamente | Regra de agregado não é equals/hashCode da classe base |
| 🟢 [Vet.java:1](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L1) | HashSet e comparador por NamedEntity.getName | Não constitui igualdade por ID/nome nem tratamento de null |

### Métodos de testes próprios

- 🟢 `shouldNotValidateWhenFirstNameEmpty` — [ValidatorTests.java:48](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L48).
- 🟢 `shouldNotValidateWhenLastNameEmpty` — [ValidatorTests.java:64](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L64).

## SQL e configuração compartilhados

🟢 CONFIRMADO — As colunas herdadas são materializadas nas tabelas das entidades consumidoras. application.properties usa ddl-auto=none; Column(length=30) não substitui a leitura do DDL por perfil.

| Arquivo | Relação com modelo |
|---|---|
| 🟢 [schema.sql:1](../../src/main/resources/db/h2/schema.sql#L1) | IDs/colunas e limites H2 dos consumidores |
| 🟢 [schema.sql:1](../../src/main/resources/db/mysql/schema.sql#L1) | IDs/colunas MySQL dos consumidores |
| 🟢 [schema.sql:1](../../src/main/resources/db/postgres/schema.sql#L1) | IDs/colunas PostgreSQL, inclusive TEXT em nomes |
| 🟢 [application.properties:1](../../src/main/resources/application.properties#L1) | SQL init, estratégia de nomes e ddl-auto |
| 🟢 [pom.xml:1](../../pom.xml#L1) | Dependências Persistence/Validation e teste |
| 🟢 [build.gradle:1](../../build.gradle#L1) | Configuração Gradle das dependências |

## Ligações com investigações e riscos

| Item | Origem | Artefatos afetados |
|---|---|---|
| 🔴 MOD-INV-01 — IDs/persistência | BaseEntity e schemas consumidores | MOD-RF-01/04; T-01/04; TT-04; TM-01 |
| 🔴 MOD-INV-01 — compatibilidade serializada | Serializable sem serialVersionUID explícito; hints e teste de Vet | T-05; TT-05; TM-02 |
| 🟡 Igualdade e collections | Sem equals/hashCode próprio nas três bases; regras de Owner/Vet | MOD-EC-09; integração de consumidores |
| 🟡 Normalização e validação | Setters diretos; NotBlank/Size declarativos | MOD-EC-03–08/10/14; TT-02/03 |
| 🟡 Concorrência | Sem Version na base | MOD-EC-15; avaliar por fluxo consumidor antes de inferir falha |

## Cobertura e limites

🟢 CONFIRMADO — Mapeados quatro arquivos Java do pacote model, um arquivo de teste próprio com dois métodos Test e seis entidades consumidoras, além de fontes compartilhadas. Contagem é inventário, não percentual de cobertura de linhas ou aprovação de teste.

🟢 CONFIRMADO — Não existem repository, controller, template ou contrato HTTP próprios nesta unit. A documentação não cria classes de serviço ou tabelas novas para preencher esses espaços. Arquivos transversais mantêm referências às units de origem, sem propriedade exclusiva do modelo.

🟡 INFERIDO — Mudanças na base podem afetar responsáveis/veterinários e serialização de sistema. Consultar a [matriz de impacto](../traceability/spec-impact-matrix.md); a organização em pastas de specs não implica isolamento de runtime.

## Artefatos da unit

🟢 CONFIRMADO — [requirements.md](requirements.md), [design.md](design.md), [tasks.md](tasks.md) e [edge-cases.md](edge-cases.md) foram gerados antes deste mapeamento. Este é o quinto arquivo previsto para modelo. Investigações continuam abertas; tarefas de implementação e testes não foram executadas.
