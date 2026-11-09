# Veterinários — Perguntas para validação

## Como preencher

🟢 CONFIRMADO — Este documento reúne seis perguntas relativas às lacunas já registradas na unit veterinarios. São perguntas pendentes, não respostas inferidas. Não houve caracterização em execução nesta etapa.

🟡 ORIENTAÇÃO — Preencha **Resposta** quando tiver informação; use “não sei / precisa investigar” se necessário. Distinga regra atual conhecida, evidência operacional e comportamento desejado. Não inclua senhas/tokens ou dados sensíveis. As verificações técnicas complementares são propostas em [tasks.md](tasks.md), não pré-requisitos para responder sobre intenção de produto.

🟢 CONFIRMADO — D-03 e D-04 são IDs transversais do domínio; esta unit não cria uma decisão concorrente. Respostas prévias podem ser referenciadas. D-04 também será relevante ao módulo sistema.

🟡 ORIENTAÇÃO — Os próximos arquivos podem ser gerados com perguntas abertas; incorporação e classificação das respostas serão retomadas na revisão. Nenhuma resposta autoriza implicitamente modificação de código ou infraestrutura.

## Índice

| Pergunta | Lacuna | Tema | Estado |
|---|---|---|---|
| 1 | D-04 | Frescor e configuração do cache | ✅ Processada |
| 2 | VET-D-01 | Formatos e conteúdo de GET /vets | ✅ Processada |
| 3 | VET-D-02 | Contrato de erro das consultas | ✅ Processada |
| 4 | VET-D-03 | Ordem dos veterinários | ✅ Processada |
| 5 | VET-INV-01 | Política para especialidades inconsistentes | ✅ Processada |
| 6 | D-03 | Proteção externa de acesso — vínculo transversal | ✅ Processada |

## Pergunta 1 — D-04: Frescor e configuração do cache

**Situação:** ✅ Respondida na pergunta consolidada 7; ver ../validated-decisions.md.

**Contexto:** 🟢 As duas consultas findAll usam Cacheable("vets"); sistema habilita caching e declara customizador JCache. Provedor, expiração, capacidade e invalidação não foram comprovados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-06/14/15; RNF de cache; design/contratos de infraestrutura; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** Como os dados de veterinários/especialidades são atualizados no ambiente real e em quanto tempo precisam aparecer nas consultas HTML e de dados? Se houver política conhecida de cache, indique onde ela é configurada; se não houver, registre isso.

**Impacto:** 🔴 Afeta contrato de frescor e integração T-03/07; uma decisão de prazo não comprova o TTL atual. Não inventar invalidação nem provedor.

**Verificação técnica complementar:** 🟡 TT-06: identificar CacheManager e observar consultas completas/paginadas antes e depois de atualização em base isolada.

**Resposta:** Não tem politica de cache

**Origem:** usuário, pergunta consolidada 7.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 2 — VET-D-01: Formatos e conteúdo de GET /vets

**Situação:** ✅ Respondida na pergunta consolidada 8; ver ../validated-decisions.md.

**Contexto:** 🟢 Handler retorna Vets como body. Teste declara JSON, 200 e $.vetList[0].id; JAXB não comprova XML ativo nem schema completo.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/Vets.java`, `src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-11/12; VET-CA-06/07; T-05; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** Quais consumidores usam GET /vets e quais formatos/campos precisam ser preservados? Informe se há uso conhecido de XML além de JSON, e exemplos ou documentação sem dados sensíveis, se disponíveis.

**Impacto:** 🔴 Afeta VET-RF-03, contratos e OpenAPI futura. Necessidades de consumidores são distintas do comportamento atual a caracterizar; não declarar shape completo a partir de um JSONPath.

**Verificação técnica complementar:** 🟡 TT-02: capturar respostas de catálogo preenchido/vazio e Accept relevantes; identificar conversores e campos efetivos.

**Resposta:** Somente essa app consome, em uma refatoração campos desnecessários podem ser apagados

**Origem:** usuário, pergunta consolidada 8.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 3 — VET-D-02: Contrato de erro das consultas

**Situação:** ✅ Respondida na pergunta consolidada 9; ver ../validated-decisions.md.

**Contexto:** 🟢 Não há catch de DataAccessException nos handlers; conversão de page e falhas de cache dependem da infraestrutura MVC. A consulta precede correção da faixa.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-04/05/12; VET-CA-03/08; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** Existe um contrato de erro conhecido a preservar para page não numérico/fora de Integer e para falha de banco/cache? Descreva página, mensagem, status ou comportamento de recuperação esperado; se não existir contrato conhecido, registre que precisa ser caracterizado antes de decidir.

**Impacto:** 🔴 Afeta VET-RF-01/02/03 e respostas HTTP. Preferência por um status novo não é evidência do status atual; falha de banco não deve virar catálogo vazio por suposição.

**Verificação técnica complementar:** 🟡 TT-05: caracterizar GETs separadamente, conversões inválidas e falhas de consulta/cache, registrando body/status e eventual redirect.

**Resposta:** Não tem contrato definido

**Origem:** usuário, pergunta consolidada 9.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 4 — VET-D-03: Ordem dos veterinários

**Situação:** ✅ Respondida na pergunta consolidada 10; ver ../validated-decisions.md.

**Contexto:** 🟢 PageRequest não declara Sort e findAll completo não declara ordem. A ordenação de especialidades é separada da ordem dos veterinários.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-06; T-04/05/06; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** A listagem de veterinários deve seguir uma ordem específica? Se sim, informe campos, direção e desempate, e se a mesma regra vale para HTML e GET /vets; se a ordem atual não faz parte do contrato, registre isso.

**Impacto:** 🔴 Afeta VET-RF-01/03, paginação e expectativas de consumidores. Qualquer Sort novo deve ser registrado como decisão ou evolução, sem atribuí-lo ao legado atual.

**Verificação técnica complementar:** 🟡 TT-01/02/06: observar ordem com dados representativos e atualização entre páginas, distinguindo estabilidade desejada de ordem ocasional da base/cache.

**Resposta:** ordem alfabética

**Origem:** usuário, pergunta consolidada 10.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 5 — VET-INV-01: Política para especialidades inconsistentes

**Situação:** ✅ Respondida na pergunta consolidada 11; ver ../validated-decisions.md.

**Contexto:** 🟢 addSpecialty não rejeita null e getter usa comparador por nome sem guarda. MySQL/PostgreSQL declaram unicidade do par na junção e H2 não. Efeito de dados inconsistentes não foi executado.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`, `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** VET-EC-08/09/16; VET-CA-09/10; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** Há dados históricos de especialidade sem nome, vínculos repetidos ou importações que precisem ser preservados? Qual política deve valer para inconsistências encontradas: registrar para saneamento, rejeitar ou outra? Se não houver informação, deixe como investigação pendente.

**Impacto:** 🔴 Afeta VET-RF-04, representação e eventual TM-01. Não filtrar null, deduplicar por nome ou corrigir registros sem decisão; intenção de tratamento não comprova falha atual.

**Verificação técnica complementar:** 🟡 TT-04/05: inventariar dados no ambiente autorizado e testar múltiplos itens que forcem comparação, distinguindo objeto Java, SQL, HTML e body.

**Resposta:** não precisa preservar dados

**Origem:** usuário, pergunta consolidada 11.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 6 — D-03: Proteção externa de acesso — vínculo transversal

**Situação:** ✅ Respondida na pergunta consolidada 3; ver ../validated-decisions.md.

**Contexto:** 🟢 Nenhum handler vet implementa identidade/papel de usuário. Proteções de proxy/rede/autenticação externas não foram confirmadas.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** D-03 transversal; contratos de acesso; casos detalhados em [edge-cases.md](edge-cases.md).

**Pergunta:** O catálogo HTML e GET /vets têm a mesma política de acesso do cadastro de responsáveis, ou há diferença a preservar? Caso D-03 de responsaveis/questions.md já tenha sido respondida, referencie essa resposta e descreva apenas exceções.

**Impacto:** 🔴 Afeta integração de acesso dos dois contratos. Não inferir que catálogo é público ou restrito apenas pela ausência de segurança local.

**Verificação técnica complementar:** 🟡 Conferir configuração/documentação do ambiente quando disponível, sem pedir credenciais e sem criar papéis presumidos.

**Resposta:** Sim, sistema só executado em rede local

**Origem:** usuário, pergunta consolidada 3.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Investigações sem pergunta de produto imediata

- 🟡 Medir custo do catálogo completo e das especialidades EAGER se volume justificar; não inventar SLA/limite sem dado.
- 🟡 Diferenciar cópia estrutural da lista de especialidades e compartilhamento dos objetos; confirmar por teste conforme TT-03, sem pedir decisão para cada detalhe Java.
- 🟡 Registrar resultado da suite no perfil escolhido; mocks de repository não comprovam cache nem equivalência entre bancos.

## Processamento posterior

🟡 ORIENTAÇÃO — Ao revisar respostas, associar evidência e decisão ao ID, atualizar as specs afetadas e manter pendente qualquer resultado técnico ainda não observado. Uma preferência de produto não transforma configuração/erro de runtime desconhecido em comportamento legado confirmado. Campos vazios continuam pendentes.

🟢 CONFIRMADO — A origem de cada lacuna já consta em requirements/design/contracts. Este arquivo não conclui a revisão global, não gera relatório de confiança final e não executa tarefas de implementação.

## Revisão das respostas

🟢 As respostas foram consolidadas e processadas. Ver [decisões validadas](../validated-decisions.md) para intenção de destino e pendências residuais; evidências do código acima não foram transformadas em implementação nova.
