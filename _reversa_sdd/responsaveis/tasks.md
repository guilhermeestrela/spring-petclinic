# Responsáveis, animais e visitas — Tarefas de Implementação

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


🟡 INFERIDO — Este é um roteiro proposto para reimplementar a unit `owner` com fidelidade ao legado, a partir de [requirements.md](requirements.md) e [design.md](design.md). A ordem e a decomposição são propostas; as marcações de confiança de cada tarefa qualificam a evidência do comportamento, não uma decisão de arquitetura nova.

🟢 CONFIRMADO — Nenhuma tarefa de código, teste de aplicação ou migração abaixo foi executada nesta extração. Caixas desmarcadas significam trabalho futuro; concluir este documento não significa implementar a unit. Arquivos legados não serão alterados nesta etapa.

## Pré-requisitos

- [ ] 🟢 Disponibilizar o contrato das superclasses BaseEntity, Person e NamedEntity. Origem: `src/main/java/org/springframework/samples/petclinic/model/`. Pronto: identidade, campos e validações correspondem a T-01.
- [ ] 🟢 Disponibilizar MVC, Thymeleaf, validação e JPA conforme `pom.xml` ou `build.gradle`. Pronto: componentes exigidos pelos controllers e repositórios são resolvidos; equivalência dos dois builds não é presumida.
- [ ] 🟢 Selecionar perfil/datasource e schema compatível. Origem: `src/main/resources/application.properties`, `application-mysql.properties` e `application-postgres.properties`. Pronto: documentar perfil e variáveis de ambiente da execução de validação, sem executar seeds contra dados existentes.
- [ ] 🟢 Integrar idioma/layout do módulo sistema. Origem: `src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java` e `src/main/resources/templates/fragments/layout.html`. Pronto: views conseguem resolver mensagens e layout.
- [ ] 🔴 Registrar decisões D-01/02/03/05/06 e RESP-D-07 antes de definir contratos novos para os pontos pendentes. Pronto: cada decisão distingue comportamento legado comprovado, caracterização pendente e mudança aprovada.

## Tarefas

- [ ] **T-01 — Preparar entidades e identidade do agregado**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`, `src/main/java/org/springframework/samples/petclinic/model/Person.java`, `src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java`, `src/main/java/org/springframework/samples/petclinic/owner/Pet.java`, `src/main/java/org/springframework/samples/petclinic/owner/PetType.java`, `src/main/java/org/springframework/samples/petclinic/owner/Visit.java`.

  Trabalho: Disponibilizar IDs Integer/IDENTITY e isNew por id null; mapear campos, tabelas, coleções e herança descritos no design. Preservar Owner.pets List com ALL/EAGER e ordem name, Pet.visits Set com ALL/EAGER e ordem date ASC e FK de tipo.

  Critério de pronto: Confirmar os campos e vínculos contra o dicionário e ERD; não criar atributo owner em Pet, pet em Visit, status ou veterinário em Visit que não existem no legado.

  Rastreabilidade: RESP-RF-09. Confiança: 🟢 CONFIRMADO.

- [ ] **T-02 — Reproduzir operações de coleção e pertencimento**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:100`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:129`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:147`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:176`, `src/main/java/org/springframework/samples/petclinic/owner/Pet.java`.

  Trabalho: Implementar addPet com descarte de null/instância/ID persistido repetido; getPet por ID apenas em persistidos e por nome com equalsIgnoreCase e ignoreNew; addVisit com asserts de parâmetros e animal encontrado.

  Critério de pronto: Validar objetos novos distintos, persistidos com mesmo ID, nome com caixa diferente e visita para pet inexistente; não deduplicar visitas por data/descrição.

  Rastreabilidade: RESP-RF-09. Confiança: 🟢 CONFIRMADO.

- [ ] **T-03 — Disponibilizar repositórios e catálogo de tipos**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java`, `src/main/java/org/springframework/samples/petclinic/owner/PetTypeRepository.java`, `src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java`.

  Trabalho: Expor findById e busca por prefixo paginada de Owner, save/saveAndFlush e consulta de tipos ORDER BY name; converter tipo pelo nome exato com ParseException em ausência e print de nome null como <null>.

  Critério de pronto: Consulta respeita Pageable e catálogo ordenado; conversão distingue cat de CAT quando apenas cat existe. Atender CA-12/13.

  Rastreabilidade: RESP-RF-02; RESP-RF-05. Confiança: 🟢 CONFIRMADO.

- [ ] **T-04 — Configurar carregamento de model e binding**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:60`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:65`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:63`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:68`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:76`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:90`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:95`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:52`.

  Trabalho: Carregar entidades pelos IDs de rota e bloquear id e *.id nos binders; instalar PetValidator no binder pet e preservar @ModelAttribute de cada controller.

  Critério de pronto: CA-25 confirma campos suprimidos; objeto persistido mantém ID carregado. Ausência de Owner lança a exceção observada; não inventar status HTTP ou autenticação.

  Rastreabilidade: RESP-RF-09. Confiança: 🟢 CONFIRMADO.

- [ ] **T-05 — Cadastrar e editar responsáveis**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:73`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:78`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:149`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:154`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:52`, `src/main/java/org/springframework/samples/petclinic/model/Person.java:31`.

  Trabalho: Implementar GET/POST de cadastro/edição e validação de nome/sobrenome até 30, endereço até 255, cidade até 80 e telefone de 10 dígitos. Em edição, validar BindingResult antes do mismatch de ID; usar save e redirects/flash documentados.

  Critério de pronto: CA-01/02 e CA-09/10/11 reproduzidos; ramos de erro não chamam save; mismatch é testado no nível do método sem presumir exploração por HTTP.

  Rastreabilidade: RESP-RF-01; RESP-RF-04. Confiança: 🟢 CONFIRMADO.

- [ ] **T-06 — Implementar busca, paginação e detalhe**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:90`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:95`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:141`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:179`.

  Trabalho: Normalizar lastName null/vazio/strip; consultar prefixo com PageRequest(max(page,1)-1,5), depois validar faixa. Produzir busca com notFound, redirect único, lista paginada ou detalhe conforme ramos do design.

  Critério de pronto: CA-03 a CA-08 reproduzidos, incluindo ordem consulta→faixa→vazio→único→lista e preservação de filtro no redirect inválido. Não acrescentar Sort explícito como comportamento legado.

  Rastreabilidade: RESP-RF-02; RESP-RF-03. Confiança: 🟢 CONFIRMADO.

- [ ] **T-07 — Validar e cadastrar animais**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java:39`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:101`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:108`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:202`.

  Trabalho: Implementar nome textual até 30, tipo requerido quando novo, nascimento requerido e não futuro. Verificar duplicidade contra persistidos ignorando caixa; adicionar pet e saveAndFlush; traduzir apenas violação com marcador unique_owner_pet_name.

  Critério de pronto: CA-14/15/16/17 reproduzidos; erros retornam formulário, outras violações são relançadas. No GET preservar os objetos novos separados criados pelo model e pelo handler, sem afirmar persistência.

  Rastreabilidade: RESP-RF-06. Confiança: 🟢 CONFIRMADO.

- [ ] **T-08 — Editar animais preservando identidade**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:140`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:145`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:186`.

  Trabalho: Carregar pet na coleção do responsável; aceitar nome do mesmo ID e rejeitar nome de outro ID; rejeitar nascimento futuro. Em updatePetDetails exigir ID não nulo, copiar nome/nascimento/tipo ao existente ou adicionar se ausente; saveAndFlush ao fim.

  Critério de pronto: CA-18/19/20 reproduzidos e assert de ID caracterizado; tipo null em edição não ganha nova regra de PetValidator. Pet ausente na rota permanece comportamento a caracterizar em TT-08.

  Rastreabilidade: RESP-RF-07. Confiança: 🟢 CONFIRMADO.

- [ ] **T-09 — Criar visitas e validar vínculos**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/Visit.java:50`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:64`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:84`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:91`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:98`, `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:176`.

  Trabalho: Carregar owner/pet, anexar nova Visit em memória com amanhã e publicar minVisitDate. Exigir descrição textual até 255 e rejeitar date não nula até hoje. Em sucesso addVisit e save(owner), com flash/redirect.

  Critério de pronto: CA-21/22/23/24 reproduzidos; GET e POST inválido não chamam save. Não introduzir @NotNull, idempotência ou bloqueio de agenda sem resolver lacunas correspondentes.

  Rastreabilidade: RESP-RF-08. Confiança: 🟢 CONFIRMADO.

- [ ] **T-10 — Integrar templates e feedback**

  Origem no legado: `src/main/resources/templates/owners/createOrUpdateOwnerForm.html`, `src/main/resources/templates/owners/findOwners.html`, `src/main/resources/templates/owners/ownersList.html`, `src/main/resources/templates/owners/ownerDetails.html`, `src/main/resources/templates/pets/createOrUpdatePetForm.html`, `src/main/resources/templates/pets/createOrUpdateVisitForm.html`, `src/main/resources/templates/fragments/inputField.html`, `src/main/resources/templates/fragments/selectField.html`.

  Trabalho: Disponibilizar views e seus model attributes, campos, mensagens de erro, links, seleção de tipo por nome e min de data. Usar configuração de idioma e layout compartilhados de sistema.

  Critério de pronto: Todos os nomes de view do design resolvem e formulários usam o objeto correto; detalhe apresenta animais/visitas e feedback. Registrar a omissão de lastName nos links de paginação e o script de mensagens sem corrigir silenciosamente.

  Rastreabilidade: RESP-RF-01; RESP-RF-02; RESP-RF-03; RESP-RF-04; RESP-RF-05; RESP-RF-06; RESP-RF-07; RESP-RF-08. Confiança: 🟢 CONFIRMADO quanto ao código; 🔴 intenção dos comportamentos de borda pendente.

## Tarefas de Teste

🟡 INFERIDO — Casos propostos para validar equivalência. A existência das classes citadas é 🟢 CONFIRMADA; testes existentes ainda precisam ser inspecionados integralmente e executados no contexto de implementação. Não se declara cobertura percentual.

- [ ] **TT-01 — Responsáveis e identidade**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java`.

  Trabalho: Exercitar cadastro/edição válidos e inválidos, limites 30/31, 255/256, 80/81 e telefone com 9/10/11 dígitos; verificar save e ramo de mismatch.

  Critério de pronto: CA-01/02/09/10/11; violações não persistem e IDs de rota são preservados.

  Confiança: 🟢 regras/fontes confirmadas; 🟡 expansão de casos proposta.

- [ ] **TT-02 — Busca, detalhe e navegação**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java`.

  Trabalho: Exercitar prefixo, null, vazio, espaços, zero/um/múltiplos resultados, page 0/negativa/fora do total e página 1 com base vazia.

  Critério de pronto: CA-03 a CA-08; capturar Pageable e redirects, sem assumir ordem não declarada. Caracterizar links HTML separadamente do redirect.

  Confiança: 🟢 comportamento de controller confirmado; 🟡 bateria ampliada proposta.

- [ ] **TT-03 — Conversão e validação de animal**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java`.

  Trabalho: Verificar catálogo, comparação exata de tipo, tipo desconhecido, supports, nome branco/30/31, nascimento null/futuro/hoje e diferença de tipo requerido em criação/edição.

  Critério de pronto: CA-12/13/15/20 e códigos required/size/typeMismatch.birthDate coerentes com cada camada.

  Confiança: 🟢 regras confirmadas; 🟡 limites adicionais propostos.

- [ ] **TT-04 — Persistência e edição de animais**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/OwnerTests.java`.

  Trabalho: Validar adição de persistido, repetição de objeto/ID, criação válida, nome duplicado com caixa diferente e edição com mesmo nome/próprio ID ou outro ID. Simular exceção reconhecida e não reconhecida.

  Critério de pronto: CA-14/16/17/18/19; verificar saveAndFlush e relançamento sem marcador; associações não desaparecem.

  Confiança: 🟢 comportamento confirmado; 🟡 extensão das verificações proposta.

- [ ] **TT-05 — Visitas e relação responsável-animal**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java`.

  Trabalho: Testar GET sem save, visita amanhã válida, hoje/passado inválidos, descrição vazia/255/256, responsável inexistente e pet de outro responsável.

  Critério de pronto: CA-21 a CA-24; verificar rejeições de campo, save somente no caminho válido e duplicação da mesma instância evitada pelo Set.

  Confiança: 🟢 regras confirmadas; 🟡 casos adicionais propostos.

- [ ] **TT-06 — Binding de identificadores**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java`.

  Trabalho: Testar binders com id e campo aninhado terminado em .id; confirmar supressão e ID carregado por rota.

  Critério de pronto: CA-25; teste de mismatch interno não é apresentado como demonstração de troca de ID por cliente HTTP.

  Confiança: 🟢 padrões declarados confirmados; 🟡 teste de binding proposto.

- [ ] **TT-07 — Duplicidade concorrente por perfil**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/PetClinicConcurrencyTests.java`, `src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java`.

  Trabalho: Caracterizar restrição e tratamento de duplicidade em base isolada H2, PostgreSQL e MySQL, incluindo nomes com diferença de caixa. Ler os testes existentes antes de expandir e não tratar mocks de mensagem como prova de erro real de driver.

  Critério de pronto: Registrar perfil, driver, constraint, mensagem e resultado de duas submissões conflitantes; D-02 só é encerrada com evidência concreta ou decisão explícita.

  Confiança: 🔴 resultado multi-banco pendente; 🟡 caracterização proposta.

- [ ] **TT-08 — Caracterização das lacunas de entrada e apresentação**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java`, `src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java`.

  Trabalho: Distinguir date ausente de date vazia com descrição válida; caracterizar petId inexistente na edição, erros de conversão e status efetivos. Verificar filtro nos links de paginação e ausência de elementos message/error no script do detalhe.

  Critério de pronto: Resultados observados registrados para D-01/D-06/RESP-D-07; intenção de produto não é inferida só do teste. Nenhuma correção automática é incluída nesta tarefa.

  Confiança: 🔴 contratos pendentes; 🟡 investigação proposta.

## Tarefas de Migração de Dados (se aplicável)

🟢 CONFIRMADO — Não há destino de migração escolhido nem plano de transformação de dados aprovado nesta extração. As tarefas abaixo são condicionais a uma reimplementação que importe dados; não autorizam execução de SQL nem exclusão de registros.

- [ ] **TM-01 — Inventariar vínculos e compatibilidade no destino, se houver importação.**

  Origem no legado: `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql` e entidades Owner/Pet/PetType/Visit.

  Trabalho proposto: mapear IDs, campos, FKs e nulabilidade; inventariar órfãos, nomes repetidos por responsável e diferenças de comparação de caixa antes de definir conversão. Planejar carga de owners/types antes de pets e de pets antes de visits, respeitando dependências.

  Critério de pronto: 🟡 mapa de origem/destino e verificações de contagem/vínculo aprovados; 🔴 destino, política para conflitos e remapeamento de IDs precisam ser definidos antes de importar. Não deduplicar ou corrigir dados sem decisão.

  Confiança: 🟢 schema e vínculos confirmados; 🟡 sequência de carga proposta; 🔴 transformação de destino não definida.

- [ ] **TM-02 — Separar inicialização demonstrativa de migração real, se houver importação.**

  Origem no legado: `src/main/resources/db/h2/schema.sql` (DROP TABLE), `src/main/resources/db/h2/data.sql`, `src/main/resources/db/mysql/data.sql`, `src/main/resources/db/postgres/data.sql` e `src/main/resources/application.properties`.

  Trabalho proposto: identificar scripts seed e seu acionamento por perfil sem aplicá-los a dados existentes. Planejar validação em ambiente isolado e estratégia de recuperação conforme o destino escolhido.

  Critério de pronto: 🟡 execução de importação documentada separadamente da inicialização de exemplo; 🔴 recuperação e persistência dependem do ambiente ainda não escolhido.

  Confiança: 🟢 scripts de origem confirmados; 🟡 preparação de migração proposta; 🔴 execução não autorizada por este documento.

## Ordem Sugerida

1. 🟡 **T-01 → T-02/T-03:** estruturar modelo, identidade, coleções e repositórios; validar TT-03/04 de forma incremental.
2. 🟡 **T-04 → T-05/T-06:** conectar carregamento/binding antes de cadastro, edição, busca e detalhe; validar TT-01/02/06.
3. 🟡 **T-07 → T-08:** implementar criação de animais antes da edição e compartilhar o tratamento de integridade comprovado; validar TT-03/04/07.
4. 🟡 **T-09:** concluir visitas quando o vínculo Owner/Pet e a persistência em cascata estiverem disponíveis; validar TT-05 e caracterizar date em TT-08.
5. 🟡 **T-10:** integrar e revisar views ao longo dos fluxos, concluindo navegação e apresentação no fim; TT-08 registra divergências de interface.
6. 🟡 **TM-01/TM-02:** somente se uma migração for necessária, após escolha de destino e decisões de dados. Não bloquear caracterização do legado por ausência de plano de migração.

🟢 CONFIRMADO — As dependências estáticas estão descritas em [design.md](design.md). 🟡 Nenhuma ordem acima exige introduzir uma camada service, API JSON ou esquema novo que não tenha sido autorizado em um ciclo de implementação.

## Lacunas Pendentes (🔴)

| ID | Decisão ou evidência necessária | Afeta | Critério para desbloquear contrato novo |
|---|---|---|---|
| 🔴 D-01 | Obrigatoriedade e resultado efetivo de data vazia/omitida de visita | T-09, TT-05/08 | Caracterização do binding e confirmação do comportamento desejado |
| 🔴 D-02 | Collation, nome e mensagem de violação MySQL; concorrência por perfil | T-07/08, TT-07, TM-01 | Evidências por perfil e decisão explícita caso haja divergência |
| 🔴 D-03 | Autenticação/ACL de implantação externa | T-04 e integração operacional | Confirmar proteção externa e escopo de eventual política nova; ID de rota não prova autorização |
| 🔴 D-05 | Fuso de negócio e regra na virada de dia | T-07/08/09 | Definir política de relógio; até lá, registrar chamadas LocalDate.now existentes |
| 🔴 D-06 | Status e apresentação para IDs ausentes e falhas de conversão | T-04/06/08/09, TT-08 | Caracterizar resposta atual sem inventar 404/409 |
| 🔴 RESP-D-07 | Intenção da omissão de lastName nos links da lista | T-06/10, TT-02/08 | Validar navegação e decidir preservação ou mudança do comportamento |

🟡 INFERIDO — O possível erro JavaScript do detalhe e o custo de carregamento EAGER descritos no design exigem evidência de execução antes de virar trabalho de correção. Não são correções incluídas implicitamente neste roteiro.

## Rastreabilidade resumida

| Requisito | Implementação proposta | Validação proposta |
|---|---|---|
| 🟢 RESP-RF-01 | T-01/04/05/10 | TT-01 |
| 🟢 RESP-RF-02 | T-03/06/10 | TT-02 |
| 🟢 RESP-RF-03 | T-06/10 | TT-02/08 |
| 🟢 RESP-RF-04 | T-04/05/10 | TT-01/06 |
| 🟢 RESP-RF-05 | T-03/10 | TT-03 |
| 🟢 RESP-RF-06 | T-01/02/04/07/10 | TT-03/04/07 |
| 🟢 RESP-RF-07 | T-02/04/08/10 | TT-03/04/08 |
| 🟢 RESP-RF-08 | T-01/02/09/10 | TT-05/08 |
| 🟢 RESP-RF-09 | T-01/02/04 | TT-04/05/06 |

🟢 CONFIRMADO — Os IDs de requisito são os de requirements.md; 🟡 a associação a tarefas é o plano proposto de realização. Todos os itens permanecem desmarcados até implementação e validação futuras.
