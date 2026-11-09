# Responsáveis — Mapeamento do legado

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-01:** Visita sempre deve conter data futura. Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário.
- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-05:** São Paulo, Brasil. Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso.
- 🟢 **D-06:** Não há contrato de erro conhecido. Confirmada ausência de contrato conhecido pelo usuário. Não impor 404/409 ou body específico como regra existente; definir contrato na evolução se necessário.
- 🟢 **RESP-D-07:** Não preservar filtro ao trocar páginas. Resposta Não interpretada para a primeira pergunta direta: filtro não precisa permanecer ativo. Comportamento atual dos links pode ser preservado, sem correção automática.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e leitura

🟢 CONFIRMADO — Mapeamento da unit `responsaveis` para o pacote Java `owner`, os templates específicos e seus testes. Referência de checkout: `500158f732419217507c7656904b8e6aa1bcc0d6`. Linhas foram lidas nos arquivos locais; arquivos não versionados de documentação não fazem parte desse commit.

🟢 CONFIRMADO — Referências usam caminhos relativos à raiz do projeto e links relativos a este documento. A marcação 🟢 significa arquivo/símbolo e relação documentados por leitura estática; não significa teste aprovado, cobertura de linhas medida ou requisito revisado por usuário. 🟡 Associações entre requisitos e tarefas são o plano de reimplementação proposto.

🟢 CONFIRMADO — Fontes e lacunas detalhadas estão em [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md), [tasks.md](tasks.md), [edge-cases.md](edge-cases.md) e [questions.md](questions.md). Este mapeamento não substitui a matriz global prevista no fim da geração.

## Arquivos Java próprios do módulo

| Arquivo e referência | Responsabilidade confirmada | Requisitos/contratos documentados | Tarefas propostas |
|---|---|---|---|
| 🟢 [Owner.java:72](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L72) | Responsável, validações, pets, busca e adição de visitas | RESP-RF-01/03/04/09; RESP-RN-01/06/08/11 | 🟡 T-01/02; TT-01/04/05 |
| 🟢 [OwnerController.java:55](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L55) | Cadastro/edição, busca paginada, detalhe e binder | RESP-RF-01/02/03/04/09; HTTP-01–07 | 🟡 T-04/05/06; TT-01/02/06/08 |
| 🟢 [OwnerRepository.java:45](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java#L45) | JpaRepository, prefixo paginado e Optional por ID | RESP-RF-01/02/03/04/06/07/08 | 🟡 T-03; TT-01/02/04/05 |
| 🟢 [Pet.java:61](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L61) | Nascimento/tipo e coleção de visitas com cascade | RESP-RF-03/06/07/08/09 | 🟡 T-01/02; TT-04/05 |
| 🟢 [PetController.java:57](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L57) | Model loaders, binders, criação/edição e catch de integridade | RESP-RF-05/06/07/09; HTTP-08–11 | 🟡 T-04/07/08; TT-03/04/06/07/08 |
| 🟢 [PetType.java:1](../../src/main/java/org/springframework/samples/petclinic/owner/PetType.java#L1) | Entidade de catálogo types, herança de NamedEntity | RESP-RF-05/06/07 | 🟡 T-01/03; TT-03 |
| 🟢 [PetTypeFormatter.java:41](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java#L41) | Conversão de tipo por nome exato e impressão | RESP-RF-05; CA-12/13 | 🟡 T-03; TT-03 |
| 🟢 [PetTypeRepository.java:37](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeRepository.java#L37) | Catálogo ordenado por nome via JPQL | RESP-RF-05 | 🟡 T-03; TT-03 |
| 🟢 [PetValidator.java:39](../../src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java#L39) | Nome até 30, tipo requerido em novo, nascimento requerido | RESP-RF-06/07; RESP-RN-05 | 🟡 T-07/08; TT-03 |
| 🟢 [Visit.java:50](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L50) | Entidade, descrição validada, data inicial amanhã | RESP-RF-08; RESP-RN-10/14 | 🟡 T-01/09; TT-05/08 |
| 🟢 [VisitController.java:47](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L47) | Vínculo owner/pet, formulário e submissão de visita | RESP-RF-08/09; HTTP-12/13 | 🟡 T-04/09; TT-05/06/08 |
| 🟢 [package-info.java:1](../../src/main/java/org/springframework/samples/petclinic/owner/package-info.java#L1) | Declaração do pacote e cabeçalho de licença, sem regra executável | n/a para requisito funcional independente | 🟡 n/a; preservar no inventário |

## Índice de símbolos Java

🟢 CONFIRMADO — Linhas indicam o começo da declaração; getters/setters e construtores foram incluídos para navegação. O índice lexical não representa grafo de chamadas nem análise sintática completa.

### Owner.java

- 🟢 `getAddress` — [Owner.java:72](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L72).
- 🟢 `setAddress` — [Owner.java:76](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L76).
- 🟢 `getCity` — [Owner.java:80](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L80).
- 🟢 `setCity` — [Owner.java:84](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L84).
- 🟢 `getTelephone` — [Owner.java:88](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L88).
- 🟢 `setTelephone` — [Owner.java:92](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L92).
- 🟢 `getPets` — [Owner.java:96](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L96).
- 🟢 `addPet` — [Owner.java:100](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L100).
- 🟢 `getPet` — [Owner.java:120](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L120).
- 🟢 `getPet` — [Owner.java:129](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L129).
- 🟢 `getPet` — [Owner.java:147](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L147).
- 🟢 `toString` — [Owner.java:160](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L160).
- 🟢 `addVisit` — [Owner.java:176](../../src/main/java/org/springframework/samples/petclinic/owner/Owner.java#L176).

### OwnerController.java

- 🟢 `OwnerController` — [OwnerController.java:55](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L55).
- 🟢 `setAllowedFields` — [OwnerController.java:60](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L60).
- 🟢 `findOwner` — [OwnerController.java:65](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L65).
- 🟢 `initCreationForm` — [OwnerController.java:73](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L73).
- 🟢 `processCreationForm` — [OwnerController.java:78](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L78).
- 🟢 `initFindForm` — [OwnerController.java:90](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L90).
- 🟢 `processFindForm` — [OwnerController.java:95](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L95).
- 🟢 `addPaginationModel` — [OwnerController.java:132](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L132).
- 🟢 `findPaginatedForOwnersLastName` — [OwnerController.java:141](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L141).
- 🟢 `initUpdateOwnerForm` — [OwnerController.java:149](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L149).
- 🟢 `processUpdateOwnerForm` — [OwnerController.java:154](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L154).
- 🟢 `showOwner` — [OwnerController.java:179](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java#L179).

### OwnerRepository.java

- 🟢 `findByLastNameStartingWith` — [OwnerRepository.java:45](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java#L45).
- 🟢 `findById` — [OwnerRepository.java:60](../../src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java#L60).

### Pet.java

- 🟢 `setBirthDate` — [Pet.java:61](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L61).
- 🟢 `getBirthDate` — [Pet.java:65](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L65).
- 🟢 `getType` — [Pet.java:69](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L69).
- 🟢 `setType` — [Pet.java:73](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L73).
- 🟢 `getVisits` — [Pet.java:77](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L77).
- 🟢 `addVisit` — [Pet.java:81](../../src/main/java/org/springframework/samples/petclinic/owner/Pet.java#L81).

### PetController.java

- 🟢 `PetController` — [PetController.java:57](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L57).
- 🟢 `populatePetTypes` — [PetController.java:63](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L63).
- 🟢 `findOwner` — [PetController.java:68](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L68).
- 🟢 `findPet` — [PetController.java:76](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L76).
- 🟢 `initOwnerBinder` — [PetController.java:90](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L90).
- 🟢 `initPetBinder` — [PetController.java:95](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L95).
- 🟢 `initCreationForm` — [PetController.java:101](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L101).
- 🟢 `processCreationForm` — [PetController.java:108](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L108).
- 🟢 `initUpdateForm` — [PetController.java:140](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L140).
- 🟢 `processUpdateForm` — [PetController.java:145](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L145).
- 🟢 `updatePetDetails` — [PetController.java:186](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L186).
- 🟢 `isDuplicatePetNameViolation` — [PetController.java:202](../../src/main/java/org/springframework/samples/petclinic/owner/PetController.java#L202).

### PetTypeFormatter.java

- 🟢 `PetTypeFormatter` — [PetTypeFormatter.java:41](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java#L41).
- 🟢 `print` — [PetTypeFormatter.java:46](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java#L46).
- 🟢 `parse` — [PetTypeFormatter.java:52](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java#L52).

### PetTypeRepository.java

- 🟢 `findPetTypes` — [PetTypeRepository.java:37](../../src/main/java/org/springframework/samples/petclinic/owner/PetTypeRepository.java#L37).

### PetValidator.java

- 🟢 `validate` — [PetValidator.java:39](../../src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java#L39).
- 🟢 `supports` — [PetValidator.java:65](../../src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java#L65).

### Visit.java

- 🟢 `Visit` — [Visit.java:50](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L50).
- 🟢 `getDate` — [Visit.java:54](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L54).
- 🟢 `setDate` — [Visit.java:58](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L58).
- 🟢 `getDescription` — [Visit.java:62](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L62).
- 🟢 `setDescription` — [Visit.java:66](../../src/main/java/org/springframework/samples/petclinic/owner/Visit.java#L66).

### VisitController.java

- 🟢 `VisitController` — [VisitController.java:47](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L47).
- 🟢 `setAllowedFields` — [VisitController.java:52](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L52).
- 🟢 `loadPetWithVisit` — [VisitController.java:64](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L64).
- 🟢 `minVisitDate` — [VisitController.java:84](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L84).
- 🟢 `initNewVisitForm` — [VisitController.java:91](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L91).
- 🟢 `processNewVisitForm` — [VisitController.java:98](../../src/main/java/org/springframework/samples/petclinic/owner/VisitController.java#L98).

## Templates próprios

| Arquivo | Comportamento / vínculo | Requisitos e casos |
|---|---|---|
| 🟢 [createOrUpdateOwnerForm.html:8](../../src/main/resources/templates/owners/createOrUpdateOwnerForm.html#L8) | Cadastro/edição com objeto owner e campos pessoais | RESP-RF-01/04; HTTP-01/02/06/07 |
| 🟢 [findOwners.html:9](../../src/main/resources/templates/owners/findOwners.html#L9) | Busca GET com lastName e apresentação de erros | RESP-RF-02; HTTP-03/04 |
| 🟢 [ownersList.html:20](../../src/main/resources/templates/owners/ownersList.html#L20) | Lista e paginação; links omitem lastName | RESP-RF-02; RESP-D-07; EC-04 |
| 🟢 [ownerDetails.html:17](../../src/main/resources/templates/owners/ownerDetails.html#L17) | Detalhe, pets/visits, links e feedback com script | RESP-RF-03; HTTP-05; EC-20 |
| 🟢 [createOrUpdatePetForm.html:11](../../src/main/resources/templates/pets/createOrUpdatePetForm.html#L11) | Objeto pet, tipo por nome e hidden id bloqueado no binder | RESP-RF-05/06/07; HTTP-08–11 |
| 🟢 [createOrUpdateVisitForm.html:30](../../src/main/resources/templates/pets/createOrUpdateVisitForm.html#L30) | Objeto visit, data/descrição e histórico | RESP-RF-08; HTTP-12/13; D-01 |

## Testes próprios localizados

🟢 CONFIRMADO — Foram localizadas as classes e declarações abaixo. Associação temática é 🟡 proposta de rastreabilidade, não afirmação de que todos os cenários planejados já existam. O inventário detalhado de testes é o próximo item do plano.

| Arquivo | Métodos anotados com @Test | Vínculo temático proposto |
|---|---:|---|
| 🟢 [OwnerControllerTests.java:106](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L106) | 18 | 🟡 RESP-RF-01/02/03/04; TT-01/02/06/08 |
| 🟢 [OwnerTests.java:25](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerTests.java#L25) | 2 | 🟡 RESP-RF-09; T-02; TT-04 |
| 🟢 [PetControllerTests.java:89](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L89) | 14 | 🟡 RESP-RF-06/07; TT-04/07/08 |
| 🟢 [PetTypeFormatterTests.java:55](../../src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java#L55) | 3 | 🟡 RESP-RF-05; TT-03 |
| 🟢 [PetValidatorTests.java:65](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L65) | 7 | 🟡 RESP-RF-06/07; TT-03 |
| 🟢 [VisitControllerTests.java:68](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L68) | 5 | 🟡 RESP-RF-08; TT-05/08 |

## Fontes compartilhadas e dependências

🟢 CONFIRMADO — Arquivos desta seção afetam a unit, mas não são exclusivos de responsáveis. Não duplicar sua propriedade nem criar uma unidade independente para cada arquivo.

| Arquivo | Relação com responsáveis | Responsabilidade transversal |
|---|---|---|
| 🟢 [BaseEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L1) | Identidade Integer, IDENTITY e isNew; sem igualdade de negócio | modelo |
| 🟢 [Person.java:1](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L1) | Nomes e restrições de tamanho herdados por Owner | modelo |
| 🟢 [NamedEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java#L1) | Nome herdado por Pet e PetType | modelo |
| 🟢 [WebConfiguration.java:1](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L1) | Idioma por sessão e interceptor lang | sistema |
| 🟢 [PetClinicRuntimeHints.java:1](../../src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java#L1) | Recursos SQL/i18n para inicialização nativa | sistema |
| 🟢 [layout.html:1](../../src/main/resources/templates/fragments/layout.html#L1) | Layout compartilhado | sistema / compartilhado |
| 🟢 [inputField.html:1](../../src/main/resources/templates/fragments/inputField.html#L1) | Binding/erros e minVisitDate do input de data | compartilhado com sistema |
| 🟢 [selectField.html:1](../../src/main/resources/templates/fragments/selectField.html#L1) | Select de tipo por representação textual | compartilhado com sistema |
| 🟢 [application.properties:1](../../src/main/resources/application.properties#L1) | Seleção SQL, JPA, mensagens e observabilidade global | sistema / compartilhado |
| 🟢 [application-mysql.properties:1](../../src/main/resources/application-mysql.properties#L1) | Datasource e inicialização do perfil MySQL | sistema / compartilhado |
| 🟢 [application-postgres.properties:1](../../src/main/resources/application-postgres.properties#L1) | Datasource e inicialização do perfil PostgreSQL | sistema / compartilhado |
| 🟢 [PetClinicConcurrencyTests.java:1](../../src/test/java/org/springframework/samples/petclinic/PetClinicConcurrencyTests.java#L1) | Teste de concorrência localizado; resultado por perfil não comprovado | testes transversais |
| 🟢 [ClinicServiceTests.java:1](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L1) | Testes de persistência localizados; nome não prova service de produção | testes transversais |
| 🟢 [ValidatorTests.java:1](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L1) | Validação de dados de entidade | modelo / testes transversais |
| 🟢 [pom.xml:1](../../pom.xml#L1) | Dependências e build Maven | build compartilhado |
| 🟢 [build.gradle:1](../../build.gradle#L1) | Dependências e build Gradle | build compartilhado |
| 🟢 [schema.sql:1](../../src/main/resources/db/h2/schema.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/h2/data.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |
| 🟢 [schema.sql:1](../../src/main/resources/db/mysql/schema.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/mysql/data.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |
| 🟢 [schema.sql:1](../../src/main/resources/db/postgres/schema.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |
| 🟢 [data.sql:1](../../src/main/resources/db/postgres/data.sql#L1) | Schema ou seed por perfil; inclui tabelas desta unit e de vet | dados compartilhados |

## Ligações das lacunas ao legado

| Lacuna | Origem principal | Spec afetada |
|---|---|---|
| 🔴 D-01 | Visit.date/Visit() e VisitController.processNewVisitForm | Regra e contrato de data vazia/omitida |
| 🔴 D-02 | Owner.getPet, PetController.isDuplicatePetNameViolation e schemas por perfil | Unicidade e erro de nome duplicado |
| 🔴 D-03 | Binders/controllers sem política de identidade de usuário e ambiente externo desconhecido | Autorização das operações |
| 🔴 D-05 | LocalDate.now em Visit, VisitController e PetController | Fuso e referência temporal |
| 🔴 D-06 | Model loaders, conversão MVC e findPet retornando null | Respostas HTTP de erro |
| 🔴 RESP-D-07 | ownersList.html e OwnerController.processFindForm | Filtro na paginação |
| 🟡 RESP-D-08 | Owner.pets, Pet.visits e PageRequest sem Sort | Medição de carga e ordem da navegação |

## Limites de cobertura

🟢 CONFIRMADO — Todos os 12 arquivos Java do pacote owner, inclusive package-info.java, os seis templates específicos de owners/pets e os seis arquivos Java de testes do pacote owner estão listados. Arquivos compartilhados são referências adicionais, sem denominador global de cobertura nesta etapa.

🟢 CONFIRMADO — package-info.java não define uma funcionalidade independente. Mensagens em src/main/resources/messages/, recursos CSS/imagens, scripts de build e implantação são transversais; sua associação global será consolidada na matriz código/spec. Não há PetRepository ou VisitRepository próprios nos arquivos examinados.

🟡 INFERIDO — A divisão de ownership entre units acompanha os pacotes e o plano aprovado, não uma separação de processos. A aplicação utiliza repositórios/infraestrutura comuns; mapear um arquivo a esta unit não prova isolamento operacional.
