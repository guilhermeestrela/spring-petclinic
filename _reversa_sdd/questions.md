# Perguntas consolidadas — spring-petclinic

🟢 Revisão estática concluída; 14 respostas recebidas e processadas. As 17 entradas das units foram consolidadas em **14 perguntas**, unificando D-03/D-04 transversais. Respostas preservadas; interpretação e pendências em [validated-decisions.md](validated-decisions.md).

Preencha **Resposta** e avise com `reversa` ou “respondi as perguntas”. Se não souber, escreva “não sei / precisa investigar”. Distinga regra atual, evidência e mudança desejada; não inclua segredos. Respostas já preenchidas nas units podem ser referenciadas e foram preservadas.

🟡 Decisão humana define intenção/contrato desejado; não comprova resultado técnico ainda não observado. Caracterizações de driver, binding, cache e artefato permanecem pendentes quando necessárias.

## Índice

| Pergunta | ID | Unit principal | Estado |
|---|---|---|---|
| 1 | D-01 | responsaveis | ✅ Processada |
| 2 | D-02 | responsaveis | ✅ Processada |
| 3 | D-03 | responsaveis | ✅ Processada |
| 4 | D-05 | responsaveis | ✅ Processada |
| 5 | D-06 | responsaveis | ✅ Processada |
| 6 | RESP-D-07 | responsaveis | ✅ Processada |
| 7 | D-04 | veterinarios | ✅ Processada |
| 8 | VET-D-01 | veterinarios | ✅ Processada |
| 9 | VET-D-02 | veterinarios | ✅ Processada |
| 10 | VET-D-03 | veterinarios | ✅ Processada |
| 11 | VET-INV-01 | veterinarios | ✅ Processada |
| 12 | SIS-D-02 | sistema | ✅ Processada |
| 13 | SIS-D-03 | sistema | ✅ Processada |
| 14 | SIS-D-04 | sistema | ✅ Processada |

## Pergunta 1 — D-01: Obrigatoriedade da data da visita

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Visit inicia date em amanhã, não tem @NotNull e o controller só rejeita data até hoje quando não nula. Parâmetro ausente e vazio ainda não foram caracterizados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Visit.java:50`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:98`.

**Specs afetadas:** requirements.md: RESP-RN-10/14 e CA-21–24; design.md: fluxo 6; contracts.md: entrada de visita; tasks.md: T-09/TT-08.

**Rastreabilidade:** EC-16; CA-21–24 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** A visita deve sempre ter uma data futura informada, pode usar amanhã quando a data é omitida, ou pode existir sem data? Especifique o comportamento desejado para campo omitido e campo enviado vazio.

**Impacto:** 🔴 Definição muda validação, valores padrão, mensagens e critérios de aceite de RESP-RF-08. Até a decisão, preservar a lacuna; não declarar null aceito/rejeitado como resultado HTTP comprovado.

**Verificação técnica complementar:** 🟡 TT-08 propõe executar submissões isoladas com descrição válida, registrar objeto após binding, BindingResult e persistência; isso demonstra o comportamento atual, mas não decide a regra de produto.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** A visita deve sempre conter uma data futura

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (D-01):** 🟢 Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário. 🔴 Residual: Binding atual e forma exata de entrada omitida ainda não executados.

## Pergunta 2 — D-02: Bancos suportados e equivalência de duplicidade

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Java compara nomes ignorando caixa; H2/PostgreSQL possuem mecanismos explícitos para unicidade correspondente. MySQL declara UNIQUE(owner_id,name) sem o nome textual que o catch procura e sem collation no schema. Não houve execução por perfil.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:147`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:202`, `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql`.

**Specs afetadas:** requirements.md: RESP-RN-06/07; design.md: fluxos 4/5 e Estado Interno; contracts.md: erro SQL; tasks.md: T-07/08 e TT-07.

**Rastreabilidade:** EC-09/12/14/15; CA-16–19 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** Quais perfis de banco precisam ser preservados na reimplementação? A regra esperada é impedir nomes que diferem apenas por maiúsculas/minúsculas para o mesmo responsável e mostrar erro de campo também quando o conflito é detectado pelo banco? Informe divergências intencionais, se houver.

**Impacto:** 🔴 Afeta RESP-RF-06/07, constraints, conversão de erros e matriz de testes. Informar bancos usados não comprova collation/mensagem real; esses detalhes continuam pendentes até evidência técnica.

**Verificação técnica complementar:** 🟡 TT-07 propõe validar por perfil a collation, o nome da restrição, a mensagem do driver e concorrência em base isolada. Não pedir ao usuário que adivinhe mensagens de exceção.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Manter somente postgresql

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (D-02):** 🟢 Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL. 🔴 Residual: Confirmar tratamento de violação e concorrência no PostgreSQL; nenhum dado foi removido.

## Pergunta 3 — D-03: Proteção de acesso fora do módulo

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Controllers bloqueiam binding de IDs e verificam pertencimento do pet, mas não implementam autenticação/papéis de usuário. Não há evidência local de política de proxy ou rede.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:60`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:90`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:64`.

**Specs afetadas:** requirements.md: RESP-RF-09; design.md: D-03; contracts.md: Binding, acesso e repetição; tarefas de integração T-04.

**Rastreabilidade:** EC-07/08; CA-24/25 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** Existe autenticação ou restrição de acesso aplicada fora da aplicação que faça parte do sistema a preservar? Se sim, descreva quem acessa cadastros/visitas, como o acesso é controlado e onde essa configuração pode ser consultada; se não existe, registre isso.

**Impacto:** 🔴 Afeta o contrato de acesso das operações e dependências de implantação. Não transformar ownerId ou sessão de idioma em prova de autorização, nem introduzir papéis como se já existissem.

**Verificação técnica complementar:** 🟡 Conferir configuração e documentação do ambiente autorizado quando disponíveis. Acesso de rede, autenticação e permissão por responsável são controles distintos e devem ser registrados separadamente.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Sim, sistema só executado em rede local

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->


**Complementos transversais da mesma decisão:**

- [veterinarios/questions.md](veterinarios/questions.md): O catálogo HTML e GET /vets têm a mesma política de acesso do cadastro de responsáveis, ou há diferença a preservar? Caso D-03 de responsaveis/questions.md já tenha sido respondida, referencie essa resposta e descreva apenas exceções.
- [sistema/questions.md](sistema/questions.md): Quais ambientes precisam manter /oups, Actuator e probes, e quem deve poder acessá-los? Indique controles externos conhecidos e exceções em relação à resposta D-03 das outras units.


**Processamento da revisão (D-03):** 🟢 Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units. 🔴 Residual: Exposição e restrições concretas de /oups, Actuator e probes no cluster não foram informadas.

## Pergunta 4 — D-05: Fuso e referência de dia para datas

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Construtor, data mínima e validação usam LocalDate.now em chamadas separadas, sem Clock/ZoneId explícitos nos arquivos analisados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Visit.java:50`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:84`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:98`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:108`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:145`.

**Specs afetadas:** requirements.md: RESP-RN-05/10; design.md: D-05; contracts.md: dados temporais; tasks.md: T-07/08/09.

**Rastreabilidade:** EC-13/17; CA-20–23 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** Qual fuso deve definir hoje e amanhã para nascimento e visita: o da clínica, o do servidor ou outro? Informe o identificador de fuso, se conhecido, e se a validação deve usar uma única referência de dia durante a requisição.

**Impacto:** 🔴 Afeta limites de data de RESP-RF-06/07/08, valor padrão amanhã e testes na virada do dia. Definição desejada deve ser distinguida da configuração efetiva atual da JVM.

**Verificação técnica complementar:** 🟡 Registrar fuso efetivo do ambiente e caracterizar fronteiras temporais conforme TT-03/05/08 antes de afirmar equivalência. Não substituir datas em código nesta extração.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** São Paulo - Brasil

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (D-05):** 🟢 Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso. 🔴 Residual: Referência única de dia por requisição não foi decidida; implementação temporal atual não alterada.

## Pergunta 5 — D-06: Respostas para IDs inexistentes e entradas inválidas

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Ausência de Owner e pet de outra coleção na visita lançam IllegalArgumentException; findPet da edição pode retornar null. Controllers não fixam status HTTP de negócio para esses ramos; conversões não foram caracterizadas.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:65`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:179`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:76`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:64`.

**Specs afetadas:** requirements.md: D-06; design.md: Fluxos Alternativos; contracts.md: Erros e limites; tasks.md: TT-08.

**Rastreabilidade:** EC-03/08/12/13; CA-08/24 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** Há um comportamento de erro que precise ser mantido para responsável/animal inexistente ou não pertencente ao responsável e para parâmetros inválidos? Descreva página/mensagem/redirect/status esperado, se houver contrato conhecido; se não houver, registre que precisa ser caracterizado antes de decidir.

**Impacto:** 🔴 Afeta RESP-RF-02/03/07/08/09 e a descrição HTTP das falhas. Uma preferência por 404 ou outro status é requisito desejado, não prova do retorno atual do legado.

**Verificação técnica complementar:** 🟡 TT-08 propõe caracterizar separadamente GET/POST de edição e visita, parâmetros não numéricos/fora de Integer e conversão de tipo/data; registrar status, body/Location e persistência sem normalizar resultados por suposição.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** não tem contrato conhecido <!-- preencha aqui -->
**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (D-06):** 🟢 Confirmada ausência de contrato conhecido pelo usuário. Não impor 404/409 ou body específico como regra existente; definir contrato na evolução se necessário. 🔴 Residual: Status/conteúdo efetivos ainda exigem caracterização quando relevantes.

## Pergunta 6 — RESP-D-07: Preservação do filtro ao navegar páginas

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Redirect de página fora de faixa mantém lastName normalizado quando não vazio. Links de paginação em ownersList.html montam somente /owners?page=..., sem lastName.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:95`, `src/main/resources/templates/owners/ownersList.html`.

**Specs afetadas:** requirements.md: RESP-RF-02; design.md: fluxo 3; contracts.md: HTTP-04; tasks.md: T-06/10 e TT-02/08.

**Rastreabilidade:** EC-04; CA-03/06 em [edge-cases.md](responsaveis/edge-cases.md) e [requirements.md](responsaveis/requirements.md), conforme o tipo de ID.

**Pergunta:** Ao buscar por sobrenome e mudar de página, o filtro deve permanecer ativo? A omissão de lastName nos links é um comportamento intencional a preservar ou uma divergência a registrar para uma correção futura?

**Impacto:** 🔴 Afeta RESP-RF-02, links e critérios de navegação. Preservar filtro em uma futura versão pode ser uma mudança funcional; esta documentação não altera os links existentes.

**Verificação técnica complementar:** 🟡 TT-02/08 propõe navegar uma busca com várias páginas e comparar query da URL, filtro da consulta e resultados. A observação comprova o efeito, não a intenção de negócio.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Não

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->


**Processamento da revisão (RESP-D-07):** 🟢 Resposta Não interpretada para a primeira pergunta direta: filtro não precisa permanecer ativo. Comportamento atual dos links pode ser preservado, sem correção automática. 🔴 Residual: A resposta não define outras mudanças de navegação; interpretação explicitada para futura revisão.

## Pergunta 7 — D-04: Frescor e configuração do cache

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 As duas consultas findAll usam Cacheable("vets"); sistema habilita caching e declara customizador JCache. Provedor, expiração, capacidade e invalidação não foram comprovados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`.

**Specs afetadas:** [requirements.md](veterinarios/requirements.md), [design.md](veterinarios/design.md), [contracts.md](veterinarios/contracts.md) e [tasks.md](veterinarios/tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-06/14/15; RNF de cache; design/contratos de infraestrutura; casos detalhados em [edge-cases.md](veterinarios/edge-cases.md).

**Pergunta:** Como os dados de veterinários/especialidades são atualizados no ambiente real e em quanto tempo precisam aparecer nas consultas HTML e de dados? Se houver política conhecida de cache, indique onde ela é configurada; se não houver, registre isso.

**Impacto:** 🔴 Afeta contrato de frescor e integração T-03/07; uma decisão de prazo não comprova o TTL atual. Não inventar invalidação nem provedor.

**Verificação técnica complementar:** 🟡 TT-06: identificar CacheManager e observar consultas completas/paginadas antes e depois de atualização em base isolada.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Não tem politica de cache

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->


**Complementos transversais da mesma decisão:**

- [sistema/questions.md](sistema/questions.md): Há configuração operacional conhecida de CacheManager, limites, expiração e invalidação que precise ser preservada? Referencie a resposta D-04 em veterinarios/questions.md e informe apenas complementos de infraestrutura, se houver.


**Processamento da revisão (D-04):** 🟢 Confirmada ausência de política operacional a preservar. Não significa remover cache nem definir TTL=0; manter declaração atual e escolher política em evolução se necessária. 🔴 Residual: CacheManager efetivo, capacidade, expiração e invalidação não comprovados.

## Pergunta 8 — VET-D-01: Formatos e conteúdo de GET /vets

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Handler retorna Vets como body. Teste declara JSON, 200 e $.vetList[0].id; JAXB não comprova XML ativo nem schema completo.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/Vets.java`, `src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java`.

**Specs afetadas:** [requirements.md](veterinarios/requirements.md), [design.md](veterinarios/design.md), [contracts.md](veterinarios/contracts.md) e [tasks.md](veterinarios/tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-11/12; VET-CA-06/07; T-05; casos detalhados em [edge-cases.md](veterinarios/edge-cases.md).

**Pergunta:** Quais consumidores usam GET /vets e quais formatos/campos precisam ser preservados? Informe se há uso conhecido de XML além de JSON, e exemplos ou documentação sem dados sensíveis, se disponíveis.

**Impacto:** 🔴 Afeta VET-RF-03, contratos e OpenAPI futura. Necessidades de consumidores são distintas do comportamento atual a caracterizar; não declarar shape completo a partir de um JSONPath.

**Verificação técnica complementar:** 🟡 TT-02: capturar respostas de catálogo preenchido/vazio e Accept relevantes; identificar conversores e campos efetivos.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Somente essa app consome, em uma refatoração campos desnecessários podem ser apagados

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (VET-D-01):** 🟢 Não há consumidor externo informado. Simplificação de campos é permitida no planejamento de refatoração após verificar uso; nenhum campo concreto foi declarado desnecessário nem removido. 🔴 Residual: Shape completo, campos usados e necessidade de XML não determinados pela resposta.

## Pergunta 9 — VET-D-02: Contrato de erro das consultas

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Não há catch de DataAccessException nos handlers; conversão de page e falhas de cache dependem da infraestrutura MVC. A consulta precede correção da faixa.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`.

**Specs afetadas:** [requirements.md](veterinarios/requirements.md), [design.md](veterinarios/design.md), [contracts.md](veterinarios/contracts.md) e [tasks.md](veterinarios/tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-04/05/12; VET-CA-03/08; casos detalhados em [edge-cases.md](veterinarios/edge-cases.md).

**Pergunta:** Existe um contrato de erro conhecido a preservar para page não numérico/fora de Integer e para falha de banco/cache? Descreva página, mensagem, status ou comportamento de recuperação esperado; se não existir contrato conhecido, registre que precisa ser caracterizado antes de decidir.

**Impacto:** 🔴 Afeta VET-RF-01/02/03 e respostas HTTP. Preferência por um status novo não é evidência do status atual; falha de banco não deve virar catálogo vazio por suposição.

**Verificação técnica complementar:** 🟡 TT-05: caracterizar GETs separadamente, conversões inválidas e falhas de consulta/cache, registrando body/status e eventual redirect.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Não tem contrato definido

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (VET-D-02):** 🟢 Não há contrato conhecido a preservar para falhas da consulta; não atribuir status novo como comportamento do legado. 🔴 Residual: Conversão/erros de banco/cache continuam não executados.

## Pergunta 10 — VET-D-03: Ordem dos veterinários

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 PageRequest não declara Sort e findAll completo não declara ordem. A ordenação de especialidades é separada da ordem dos veterinários.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`.

**Specs afetadas:** [requirements.md](veterinarios/requirements.md), [design.md](veterinarios/design.md), [contracts.md](veterinarios/contracts.md) e [tasks.md](veterinarios/tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-06; T-04/05/06; casos detalhados em [edge-cases.md](veterinarios/edge-cases.md).

**Pergunta:** A listagem de veterinários deve seguir uma ordem específica? Se sim, informe campos, direção e desempate, e se a mesma regra vale para HTML e GET /vets; se a ordem atual não faz parte do contrato, registre isso.

**Impacto:** 🔴 Afeta VET-RF-01/03, paginação e expectativas de consumidores. Qualquer Sort novo deve ser registrado como decisão ou evolução, sem atribuí-lo ao legado atual.

**Verificação técnica complementar:** 🟡 TT-01/02/06: observar ordem com dados representativos e atualização entre páginas, distinguindo estabilidade desejada de ordem ocasional da base/cache.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** ordem alfabética

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (VET-D-03):** 🟢 Ordenação alfabética passa a ser requisito desejado confirmado para veterinários; é diferença em relação a findAll/PageRequest sem Sort explícito no legado. 🔴 Residual: Campo principal (nome/sobrenome), desempate, collation e consistência entre HTML/body ainda precisam de definição no ciclo de evolução; não inventados.

## Pergunta 11 — VET-INV-01: Política para especialidades inconsistentes

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 addSpecialty não rejeita null e getter usa comparador por nome sem guarda. MySQL/PostgreSQL declaram unicidade do par na junção e H2 não. Efeito de dados inconsistentes não foi executado.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`, `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql`.

**Specs afetadas:** [requirements.md](veterinarios/requirements.md), [design.md](veterinarios/design.md), [contracts.md](veterinarios/contracts.md) e [tasks.md](veterinarios/tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-08/09/16; VET-CA-09/10; casos detalhados em [edge-cases.md](veterinarios/edge-cases.md).

**Pergunta:** Há dados históricos de especialidade sem nome, vínculos repetidos ou importações que precisem ser preservados? Qual política deve valer para inconsistências encontradas: registrar para saneamento, rejeitar ou outra? Se não houver informação, deixe como investigação pendente.

**Impacto:** 🔴 Afeta VET-RF-04, representação e eventual TM-01. Não filtrar null, deduplicar por nome ou corrigir registros sem decisão; intenção de tratamento não comprova falha atual.

**Verificação técnica complementar:** 🟡 TT-04/05: inventariar dados no ambiente autorizado e testar múltiplos itens que forcem comparação, distinguindo objeto Java, SQL, HTML e body.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** não precisa preservar dados

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (VET-INV-01):** 🟢 Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente. 🔴 Residual: Integridade de novos dados e política para Specialty null continuam dependentes de implementação; dispensa de migração não define tratamento de null.

## Pergunta 12 — SIS-D-02: Idiomas, preferência de sessão e fallback

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Resolver padrão inglês, parâmetro lang e bundles declarados. Teste de sincronismo não prova tradução semântica ou comportamento de locale inválido.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java`, `src/main/resources/application.properties`, `src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java`.

**Specs afetadas:** [requirements.md](sistema/requirements.md), [design.md](sistema/design.md), [contracts.md](sistema/contracts.md) e [tasks.md](sistema/tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-01/02/03; SIS-INV-02; catálogo em [edge-cases.md](sistema/edge-cases.md).

**Pergunta:** Quais idiomas devem ser suportados e qual comportamento deve ocorrer para idioma/chave indisponível ou lang inválido? A preferência deve durar somente a sessão atual, como indica o resolver, ou existe contrato adicional a preservar?

**Impacto:** 🔴 Afeta SIS-RF-03 e textos/navegação das demais units. Registrar regra desejada separadamente do fallback efetivo a caracterizar.

**Verificação técnica complementar:** 🟡 TT-03: testar sessão mantida/nova, locales requeridos, valor inválido e chave ausente; inventário de bundles não substitui aprovação de idioma.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Português e Ingles

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (SIS-D-02):** 🟢 Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default. 🔴 Residual: Fallback, lang inválido, variante regional e duração desejada de preferência não respondidos.

## Pergunta 13 — SIS-D-03: Conteúdo de erro por ambiente

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Template exibe message quando fornecida; teste de integração força include-message ALWAYS e espera detalhe de exceção. Isso não consta como default do arquivo principal.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/CrashController.java`, `src/main/resources/templates/error.html`, `src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java`.

**Specs afetadas:** [requirements.md](sistema/requirements.md), [design.md](sistema/design.md), [contracts.md](sistema/contracts.md) e [tasks.md](sistema/tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-04/05/06; SIS-CA-02–04; catálogo em [edge-cases.md](sistema/edge-cases.md).

**Pergunta:** Quais informações de erro devem aparecer para usuários em desenvolvimento/teste e no ambiente real? Há contrato conhecido de mensagem, HTML/JSON ou status que precise ser preservado, além das expectativas específicas do teste demonstrativo?

**Impacto:** 🔴 Afeta SIS-RF-02 e contratos de erro; não copiar ALWAYS para implantação como evidência do legado real. Uma política nova deve ter origem na decisão.

**Verificação técnica complementar:** 🟡 TT-02: registrar overrides, Accept, status e conteúdo efetivos nos ambientes necessários, distinguindo exceção de negócio e rota demonstrativa.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:** Página generica de erro sem expor nenhuma informação

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->



**Processamento da revisão (SIS-D-03):** 🟢 Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação. 🔴 Residual: Status/código e eventual body de erro da API não foram definidos; detalhes podem ser registrados em logs operacionais apropriados sem serem expostos na página.

## Pergunta 14 — SIS-D-04: Destino de implantação e imagem de referência

**Situação:** ✅ Respondida e processada; pendências técnicas permanecem quando indicadas.

**Contexto:** 🟢 Kubernetes usa dsyer/petclinic sem tag explícita, perfil postgres, bindings e probes; Compose só declara bancos. Não houve execução ou correspondência da imagem ao checkout.

**Origem no legado:** `k8s/petclinic.yml`, `k8s/db.yml`, `docker-compose.yml`, `README.md`.

**Specs afetadas:** [requirements.md](sistema/requirements.md), [design.md](sistema/design.md), [contracts.md](sistema/contracts.md) e [tasks.md](sistema/tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-10/11/12/14; SIS-RF-05/06; catálogo em [edge-cases.md](sistema/edge-cases.md).

**Pergunta:** Qual forma de execução precisa ser preservada: local Maven/Gradle, imagem construída deste checkout, Kubernetes do exemplo ou outra? Se o manifesto for usado, qual imagem/tag/digest e configuração de binding são a referência aprovada?

**Impacto:** 🔴 Afeta bootstrap/empacotamento/perfis e validação de probes. Não declarar que imagem externa equivale ao código analisado nem iniciar deploy por esta resposta.

**Verificação técnica complementar:** 🟡 T-07/TT-05/06: verificar artefato, resolução de configuração e conexão/probes no destino autorizado; registrar o que não foi executado.

✅ Respondida — origem: usuário, arquivo de perguntas.

**Resposta:**  Kubernetes e se possível usar docker para desenvolvimento local <!-- preencha aqui -->

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

**Processamento da revisão (SIS-D-04):** 🟢 Destino prioritário é Kubernetes. Docker para desenvolvimento local é preferência condicional a viabilidade, não promessa de ambiente pronto ou deploy autorizado. 🔴 Residual: Imagem/tag/digest, service binding, probes e desenho do ambiente local ainda não validados; suporte nativo não foi exigido.


## Após responder

Respostas processadas e specs atualizadas. Consulte confidence-report.md e gaps.md; resultados técnicos não comprovados continuam explicitados.
