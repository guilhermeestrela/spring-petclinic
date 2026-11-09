# Sistema — Tarefas de Implementação

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-01:** Visita sempre deve conter data futura. Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário.
- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-05:** São Paulo, Brasil. Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso.
- 🟢 **D-04:** Não há política de cache. Confirmada ausência de política operacional a preservar. Não significa remover cache nem definir TTL=0; manter declaração atual e escolher política em evolução se necessária.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.
- 🟢 **SIS-D-04:** Kubernetes; Docker local se possível. Destino prioritário é Kubernetes. Docker para desenvolvimento local é preferência condicional a viabilidade, não promessa de ambiente pronto ou deploy autorizado.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


🟡 INFERIDO — Roteiro proposto para reimplementar os requisitos de [requirements.md](requirements.md) conforme [design.md](design.md). Decomposição/ordem são propostas; confiança das tarefas indica evidência de origem. Nenhuma tarefa de código, aplicação, banco ou implantação foi executada nesta extração.

## Pré-requisitos

- [ ] 🟢 Dependências de Boot/MVC/Thymeleaf/cache/JPA/validação e wrappers identificados em pom.xml/build.gradle. Pronto: ambiente de implementação definido sem presumir equivalência dos dois builds.
- [ ] 🟢 Classes BaseEntity/Person/Vet e repositório vet disponíveis conforme units relacionadas. Pronto: referências de hints/cache resolvidas.
- [ ] 🟢 Recursos e propriedades do perfil escolhido inventariados. Pronto: distinguir arquivos de exemplo de configuração efetiva, sem consultar/expor credenciais desnecessárias.
- [ ] 🔴 Identificar destino/ambiente antes de validar banco, cache, imagem ou política de acesso. Pronto: registrar contexto, sem inferir produção dos overrides de teste.

## Tarefas

- [ ] **T-01 — Configurar bootstrap e hints**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java`, `src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java`.

  Trabalho proposto: Preservar main delegando SpringApplication.run, importação de hints, padrões db/*, db/*/*, messages/* e mysql-default-conf, e JavaSerialization para BaseEntity/Person/Vet.

  Critério de pronto: SIS-CA-08/09 verificados; recursos presentes no artefato necessário e suporte nativo só declarado após caracterização quando aplicável.

  Rastreabilidade: SIS-RF-05. Confiança: 🟢 declarações confirmadas; 🔴 execução nativa pendente.

- [ ] **T-02 — Disponibilizar boas-vindas**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java`, `src/main/resources/templates/welcome.html`, `src/main/resources/templates/fragments/layout.html`.

  Trabalho proposto: Preservar GET / retornando welcome e integração com layout/recursos compartilhados.

  Critério de pronto: SIS-CA-01 e teste de view/status correspondentes atendidos; falha de renderização caracterizada separadamente se exigida, sem fallback inventado.

  Rastreabilidade: SIS-RF-01. Confiança: 🟢 comportamento confirmado.

- [ ] **T-03 — Preservar demonstração e apresentação de erro**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/system/CrashController.java`, `src/main/resources/templates/error.html`.

  Trabalho proposto: Implementar /oups lançando RuntimeException com mensagem definida, view de erro por status 404/500/outro e uso de message do contexto. Não promover override ALWAYS de teste a padrão de produção.

  Critério de pronto: SIS-CA-02/03/04 reproduzidos no contexto declarado; política de conteúdo de erro em implantação registrada em SIS-D-03.

  Rastreabilidade: SIS-RF-02. Confiança: 🟢 código/testes confirmados; 🔴 política implantada pendente.

- [ ] **T-04 — Configurar idioma por sessão**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java`, `src/main/resources/application.properties`.

  Trabalho proposto: Preservar SessionLocaleResolver com inglês padrão, interceptor lang e registro global; resolver bundles messages/messages e integrar views.

  Critério de pronto: SIS-CA-05/06 atendidos; troca com sessão, locale inválido e fallback caracterizados por TT-03 sem introduzir allowlist não existente.

  Rastreabilidade: SIS-RF-03. Confiança: 🟢 configuração confirmada; 🔴 runtime/fallback pendente.

- [ ] **T-05 — Integrar cache de dados**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `pom.xml`, `build.gradle`.

  Trabalho proposto: Preservar EnableCaching e JCacheManagerCustomizer declarando criação de vets com estatísticas. Registrar Caffeine runtime e identificar provedor ativo antes de afirmar aplicação do customizador.

  Critério de pronto: SIS-CA-07 verificado com contexto compatível; ambiente real identifica CacheManager e comportamento sem inventar TTL/capacidade/invalidação. Alinhar D-04 com veterinarios.

  Rastreabilidade: SIS-RF-04. Confiança: 🟢 declarações confirmadas; 🔴 D-04 operacional pendente.

- [ ] **T-06 — Preservar propriedades e perfis**

  Origem no legado: `src/main/resources/application.properties`, `src/main/resources/application-mysql.properties`, `src/main/resources/application-postgres.properties`.

  Trabalho proposto: Documentar database e SQL interpolados, ddl-auto none, open-in-view false, estratégia de nomes, batch 16, Thymeleaf HTML, mensagens, INFO e max-age estático 12h; preservar alternativas MYSQL/POSTGRES dos placeholders.

  Critério de pronto: SIS-CA-10/11 atendidos em configuração/ambiente isolado; seleção e precedência efetivas registradas. Não executar schema/seeds em base existente por rotina.

  Rastreabilidade: SIS-RF-06. Confiança: 🟢 propriedades confirmadas; 🔴 conexão e erros por perfil pendentes.

- [ ] **T-07 — Validar integração operacional conforme destino**

  Origem no legado: `k8s/petclinic.yml`, `k8s/db.yml`, `docker-compose.yml`, `.github/workflows/maven-build.yml`, `.github/workflows/gradle-build.yml`, `.github/workflows/deploy-and-test-cluster.yml`.

  Trabalho proposto: Usar manifests/workflows como evidência do legado: Compose inicia bancos; cluster referencia imagem externa e bindings/probes. Separar comportamento da imagem do checkout e definir contexto de acesso a Actuator/oups.

  Critério de pronto: SIS-D-04 e D-03/SIS-D-01 recebem evidência/decisão; não declarar deploy aprovado por existência de YAML. Não executar deploy nesta extração.

  Rastreabilidade: SIS-RF-05; SIS-RF-06; integração transversal. Confiança: 🟢 manifests confirmados; 🔴 correspondência de imagem/acesso pendente.

## Tarefas de Teste

🟡 INFERIDO — Executar verificações proporcionais à alteração, preservando testes úteis. Este documento não cria testes nem os executa; contagem de expectativas não significa aprovação.

- [ ] **TT-01 — Boas-vindas e views**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/system/WelcomeControllerTests.java`.

  Trabalho proposto: Preservar expectativa 200/view welcome; integrar template e recursos se alterados. Caracterizar ausência de view apenas quando necessária ao contrato.

  Critério de pronto: SIS-CA-01 validado; resultado de renderização distinguido do retorno do método.

  Confiança: 🟢 teste existente; 🟡 verificação ampliada proposta.

- [ ] **TT-02 — Erro demonstrativo e conteúdo**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java`.

  Trabalho proposto: Preservar testes diretos de CrashControllerTests e integração JSON/HTML. Registrar include-message ALWAYS, access.default none e exclusões datasource/JPA do contexto de teste; comparar ambiente normal apenas quando autorizado.

  Critério de pronto: SIS-CA-02/03/04 atendidos no contexto correto; mensagem/status de produção não inferidos.

  Confiança: 🟢 testes/overrides confirmados; 🔴 SIS-D-03 pendente.

- [ ] **TT-03 — Idioma e bundles**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java`.

  Trabalho proposto: Preservar busca de literais/chaves; caracterizar troca lang com sessão mantida/nova, locale inválido, chave ausente e idiomas requeridos.

  Critério de pronto: SIS-CA-05/06 e SIS-INV-02 registrados; sincronismo de chaves não tratado como tradução correta.

  Confiança: 🟢 testes/configuração confirmados; 🟡 cenários adicionais propostos.

- [ ] **TT-04 — Customizador e provedor cache**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`.

  Trabalho proposto: Verificar configuração de criação vets/estatísticas e em contexto integrado identificar cache ativo, comportamento com cache já existente e falha de criação; alinhar TT-06 de veterinarios.

  Critério de pronto: SIS-CA-07 e SIS-INV-03 documentados com evidência; não presumir que customizador JCache é usado com todo provedor.

  Confiança: 🟢 código confirmado; 🔴 runtime pendente.

- [ ] **TT-05 — Bootstrap, recursos e artefato**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java`.

  Trabalho proposto: Verificar delegação de main e padrões/tipos registrados; inspecionar recursos no artefato necessário e caracterizar execução nativa somente se requerida.

  Critério de pronto: SIS-CA-08/09 e SIS-INV-04 registrados; build nativo não declarado aprovado sem execução.

  Confiança: 🟢 declarações confirmadas; 🟡 validação proposta.

- [ ] **TT-06 — Perfis e falhas de infraestrutura**

  Origem no legado: `src/main/resources/application.properties`.

  Trabalho proposto: Em base isolada, validar perfil/SQL/JPA selecionados e caracterizar banco indisponível/schema incompatível se necessário. Verificar cache HTTP estático e Actuator/probes no ambiente escolhido sem alterar exposição por rotina.

  Critério de pronto: SIS-CA-10/11 e SIS-INV-05 registrados; contexto e headers/acesso efetivos separados das propriedades.

  Confiança: 🟢 fontes confirmadas; 🔴 conexão/acesso efetivos pendentes.

## Tarefas de Migração de Dados (se aplicável)

🟢 CONFIRMADO — Sistema não tem tabela de negócio própria. SQL de inicialização é compartilhado; não existe migração escolhida nesta extração.

- [ ] **TM-01 — Separar inicialização de exemplo de eventual importação.**

  Origem: `src/main/resources/application.properties`, perfis mysql/postgres e schemas/data em `src/main/resources/db/`.

  Trabalho proposto: 🟡 mapear acionamento de scripts e recursos por perfil; H2 schema contém DROP TABLE, enquanto scripts externos têm declarações de inicialização. Planejar importação com as units de domínio somente se destino for definido.

  Critério de pronto: 🔴 ambiente/destino, estratégia de preservação e recuperação definidos antes de carga; 🟡 execução de importação separada da inicialização de exemplo. Não rodar SQL/seed em dados existentes nesta extração.

  Confiança: 🟢 arquivos e configuração confirmados; 🟡 preparação proposta; 🔴 migração não definida.

## Ordem Sugerida

1. 🟡 T-01/T-06: estabelecer bootstrap e configuração do contexto de implementação.
2. 🟡 T-02/T-03: integrar views/erro; validar TT-01/02 no contexto adequado.
3. 🟡 T-04: idioma com TT-03; não tratar teste de chave como prova de toda a sessão.
4. 🟡 T-05: cache com TT-04 e integração de veterinários; evitar duplicar caracterizações D-04.
5. 🟡 T-07/TT-05/06: validar artefato/ambiente conforme necessidade real, sem publicar ou implantar por criação desta spec.
6. 🟡 TM-01: somente se houver cenário de importação após decisões de destino.

## Lacunas Pendentes (🔴)

| ID | Informação necessária | Tarefas afetadas |
|---|---|---|
| 🔴 D-03 / SIS-D-01 | Proteção de /oups, Actuator e probes por ambiente | T-03/07; TT-02/06 |
| 🔴 D-04 | Provedor, tamanho, expiração, frescor e invalidação do cache | T-05; TT-04; integração com vet |
| 🔴 SIS-D-02 / SIS-INV-02 | Idiomas, locale inválido, sessão e fallback | T-04; TT-03 |
| 🔴 SIS-D-03 | Política de conteúdo de erro fora do teste | T-03; TT-02 |
| 🔴 SIS-D-04 | Imagem externa versus checkout e service binding real | T-07; TT-05/06 |
| 🔴 SIS-INV-01/03/04/05 | Resultados de renderização/cache/startup/banco | Tarefas técnicas correspondentes; evidência por contexto |

## Rastreabilidade resumida

| Requisito | Tarefas propostas | Validação proposta |
|---|---|---|
| 🟢 SIS-RF-01 | T-02 | TT-01 |
| 🟢 SIS-RF-02 | T-03 | TT-02 |
| 🟢 SIS-RF-03 | T-04 | TT-03 |
| 🟢 SIS-RF-04 | T-05 | TT-04 |
| 🟢 SIS-RF-05 | T-01/07 | TT-05 |
| 🟢 SIS-RF-06 | T-06/07 | TT-06 |

🟡 INFERIDO — Relações entre requisitos e tarefas são plano de realização. Todas as caixas permanecem abertas até implementação/validação futuras. Exposição ampla no legado não é decisão de segurança de uma nova implantação.
