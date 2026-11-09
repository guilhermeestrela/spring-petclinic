# Veterinários — Mapeamento do legado

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-04:** Não há política de cache. Confirmada ausência de política operacional a preservar. Não significa remover cache nem definir TTL=0; manter declaração atual e escolher política em evolução se necessária.
- 🟢 **VET-D-01:** Somente a aplicação consome; campos desnecessários podem ser removidos numa refatoração. Não há consumidor externo informado. Simplificação de campos é permitida no planejamento de refatoração após verificar uso; nenhum campo concreto foi declarado desnecessário nem removido.
- 🟢 **VET-D-02:** Não há contrato de erro definido. Não há contrato conhecido a preservar para falhas da consulta; não atribuir status novo como comportamento do legado.
- 🟢 **VET-D-03:** Ordem alfabética. Ordenação alfabética passa a ser requisito desejado confirmado para veterinários; é diferença em relação a findAll/PageRequest sem Sort explícito no legado.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e evidência

🟢 CONFIRMADO — Unit vet → veterinarios, vinculada ao checkout `500158f732419217507c7656904b8e6aa1bcc0d6`. Linhas e símbolos abaixo foram identificados nas fontes locais. Referências relativas partem deste documento para a raiz do projeto. Documentos Reversa não versionados não pertencem ao commit indicado.

🟢 CONFIRMADO — Mapeamento documental não equivale a cobertura de testes medida. 🟡 Ligações entre requisito e tarefa representam plano de realização proposto; as caixas de implementação permanecem abertas. Nenhum teste ou endpoint foi executado nesta etapa.

## Arquivos próprios

| Arquivo | Responsabilidade confirmada | Requisitos/contratos | Tarefas propostas |
|---|---|---|---|
| 🟢 [Specialty.java:1](../../src/main/java/org/springframework/samples/petclinic/vet/Specialty.java#L1) | Entidade specialties e herança de NamedEntity | VET-RF-04 | 🟡 T-01; TT-04 |
| 🟢 [Vet.java:52](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L52) | Entidade Vet, ManyToMany e lista derivada de especialidades | VET-RF-04; VET-INV-01 | 🟡 T-01/02; TT-03/04/05 |
| 🟢 [VetController.java:41](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L41) | HTML paginado, correção de faixa e body completo | VET-RF-01/02/03; VET-HTTP-01/02 | 🟡 T-04/05; TT-01/02/05 |
| 🟢 [VetRepository.java:46](../../src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java#L46) | Duas consultas findAll, readOnly e cache vets | VET-RF-01/03; D-04 | 🟡 T-03/07; TT-04/06 |
| 🟢 [Vets.java:36](../../src/main/java/org/springframework/samples/petclinic/vet/Vets.java#L36) | Envelope JAXB e lista mutável inicializada sob demanda | VET-RF-03; VET-D-01 | 🟡 T-05; TT-02 |
| 🟢 [package-info.java:1](../../src/main/java/org/springframework/samples/petclinic/vet/package-info.java#L1) | Declaração de pacote e licença, sem comportamento funcional próprio | n/a independente | 🟡 n/a; inventário |

## Índice de símbolos

🟢 CONFIRMADO — Índice lexical de declarações para navegação; não é grafo de chamadas nem parser Java completo. Sobrecargas findAll são diferenciadas por sua assinatura na fonte.

### Vet.java

- 🟢 `getSpecialtiesInternal` — [Vet.java:52](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L52).
- 🟢 `getSpecialties` — [Vet.java:60](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L60).
- 🟢 `getNrOfSpecialties` — [Vet.java:66](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L66).
- 🟢 `addSpecialty` — [Vet.java:70](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L70).

### VetController.java

- 🟢 `VetController` — [VetController.java:41](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L41).
- 🟢 `showVetList` — [VetController.java:46](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L46).
- 🟢 `addPaginationModel` — [VetController.java:56](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L56).
- 🟢 `findPaginated` — [VetController.java:65](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L65).
- 🟢 `showResourcesVetList` — [VetController.java:73](../../src/main/java/org/springframework/samples/petclinic/vet/VetController.java#L73).

### VetRepository.java

- 🟢 `findAll` — [VetRepository.java:46](../../src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java#L46).
- 🟢 `findAll` — [VetRepository.java:56](../../src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java#L56).

### Vets.java

- 🟢 `getVetList` — [Vets.java:36](../../src/main/java/org/springframework/samples/petclinic/vet/Vets.java#L36).

## Template próprio

🟢 [vetList.html:9](../../src/main/resources/templates/vets/vetList.html#L9) — tabela com nome completo e especialidades; chave none quando quantidade zero; navegação quando totalPages > 1. Cobre VET-RF-01/04 e usa layout/i18n compartilhados. A ordem dos veterinários não é definida por esse template.

## Testes próprios

🟢 CONFIRMADO — Há dois arquivos Java de testes em vet. Asserções listadas são expectativas do código, ainda sem execução.

| Arquivo/método | Verificação presente | Limite de evidência |
|---|---|---|
| 🟢 [VetControllerTests.java:84](../../src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java#L84) `showVetListHtml` | Status 200, listVets presente e view vets/vetList | Não afirma atributos completos nem Pageable tamanho 5 |
| 🟢 [VetControllerTests.java:94](../../src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java#L94) `showVetListRedirectsOutOfBoundsPageToFirstPage` | Laço page 0/2: 3xx e URL /vets.html?page=1 | Não testa negativos, overflow ou falha de repository |
| 🟢 [VetControllerTests.java:103](../../src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java#L103) `showResourcesVetList` | Accept JSON: 200, application/json e $.vetList[0].id=1 | Repository mockado; não prova shape completo/XML/cache |
| 🟢 [VetTests.java:28](../../src/test/java/org/springframework/samples/petclinic/vet/VetTests.java#L28) `serialization` | Serialização Java preserva id, firstName e lastName | Vet sem especialidades; não comprova JSON/XML |

## Dependências e fontes compartilhadas

| Arquivo | Relação com a unit | Responsabilidade transversal |
|---|---|---|
| 🟢 [BaseEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L1) | IDs Integer/IDENTITY, isNew e serialização; sem igualdade de negócio | modelo |
| 🟢 [Person.java:1](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L1) | Nome e sobrenome herdados por Vet | modelo |
| 🟢 [NamedEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L1) | Nome herdado por Specialty e usado no comparador | modelo |
| 🟢 [CacheConfiguration.java:1](../../src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java#L1) | EnableCaching e customizador JCache para vets com estatísticas | sistema |
| 🟢 [WebConfiguration.java:1](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L1) | Idioma por sessão/interceptor | sistema |
| 🟢 [PetClinicRuntimeHints.java:1](../../src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java#L1) | Registro de serialização Java de Vet | sistema |
| 🟢 [layout.html:1](../../src/main/resources/templates/fragments/layout.html#L1) | Layout e navegação compartilhados | sistema / compartilhado |
| 🟢 [application.properties:1](../../src/main/resources/application.properties#L1) | Configuração JPA, SQL, i18n e observabilidade global | sistema / compartilhado |
| 🟢 [application-mysql.properties:1](../../src/main/resources/application-mysql.properties#L1) | Datasource por perfil MySQL | sistema / compartilhado |
| 🟢 [application-postgres.properties:1](../../src/main/resources/application-postgres.properties#L1) | Datasource por perfil PostgreSQL | sistema / compartilhado |
| 🟢 [ClinicServiceTests.java:1](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L1) | shouldFindVets espera Douglas, duas especialidades e ordem dentistry/surgery | teste JPA transversal |
| 🟢 [EntityUtils.java:1](../../src/test/java/org/springframework/samples/petclinic/service/EntityUtils.java#L1) | Helper de busca por ID usado nos testes JPA | suporte a teste transversal |
| 🟢 [pom.xml:1](../../pom.xml#L1) | Dependências JPA/MVC/cache/JAXB/Thymeleaf | build compartilhado |
| 🟢 [build.gradle:1](../../build.gradle#L1) | Dependências equivalentes em configuração Gradle, sem execução de equivalência | build compartilhado |
| 🟢 [schema.sql:1](../../src/main/resources/db/h2/schema.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/h2/data.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |
| 🟢 [schema.sql:1](../../src/main/resources/db/mysql/schema.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/mysql/data.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |
| 🟢 [schema.sql:1](../../src/main/resources/db/postgres/schema.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/postgres/data.sql#L1) | Tabelas/seeds de vets, specialties e vet_specialties; também contém dados de owner | dados compartilhados |

🟢 CONFIRMADO — Bundles em src/main/resources/messages/ fornecem textos do template. Mensagens, estilos, fontes, build e implantação são transversais e serão consolidados na matriz global; a unit não assume propriedade exclusiva desses arquivos.

## Mapa das decisões pendentes

| Lacuna | Origem no legado | Artefatos afetados |
|---|---|---|
| 🔴 D-04 | VetRepository e CacheConfiguration; runtime não confirmado | Cache/readOnly nos RNFs, design e contratos; T-03/07, TT-06 |
| 🔴 VET-D-01 | ResponseBody em VetController, Vets/Vet e teste JSON parcial | Representação de VET-RF-03; contratos, TT-02 |
| 🔴 VET-D-02 | Conversão de page, consultas sem catch e infraestrutura de cache | Falhas dos contratos; TT-05 |
| 🔴 VET-D-03 | PageRequest sem Sort e findAll sem ordem declarada | Ordem da lista HTML/body; TT-01/02 |
| 🔴 VET-INV-01 | Set/comparador sem guarda null e diferenças de schema da junção | Especialidades, dados inconsistentes e TM-01 |
| 🔴 D-03 | Ausência de controle de usuário no controller; ambiente externo desconhecido | Contrato de acesso, integração |

## Cobertura e limites

🟢 CONFIRMADO — Foram mapeados os seis arquivos Java do pacote vet, um template específico e dois arquivos de testes com quatro declarações @Test. package-info.java não define requisito independente. Essa contagem é um inventário do escopo, sem percentual global ou cobertura de linhas.

🟢 CONFIRMADO — Há seis entidades JPA no sistema, mas apenas Vet/Specialty pertencem a esta unit; Vets não é entidade e vet_specialties não tem classe JPA própria. Relação com owner é infraestrutura compartilhada, sem vínculo Visit→Vet no código examinado.

🟡 INFERIDO — A associação por pacote é organização de specs, não separação de serviços ou processos. Eventual mudança em modelo/cache também pode afetar responsáveis/sistema; consultar a [matriz de impacto](../traceability/spec-impact-matrix.md).

## Artefatos da unit

🟢 CONFIRMADO — [requirements.md](requirements.md), [design.md](design.md), [tasks.md](tasks.md), [contracts.md](contracts.md), [edge-cases.md](edge-cases.md) e [questions.md](questions.md) compõem a documentação já gerada; este mapeamento é o sétimo arquivo previsto da unit. Lacunas continuam abertas, sem implementação ou validação dinâmica presumida.
