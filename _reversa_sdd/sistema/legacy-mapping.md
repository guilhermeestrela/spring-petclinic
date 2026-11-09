# Sistema — Mapeamento do legado

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


## Escopo e evidência

🟢 CONFIRMADO — Unit system → sistema e inicialização transversal do pacote raiz; checkout de referência `500158f732419217507c7656904b8e6aa1bcc0d6`. Referências de arquivo/linha foram verificadas localmente. Links são relativos ao documento; artefatos Reversa não versionados não fazem parte do commit citado.

🟢 CONFIRMADO — Mapeamento estático não comprova execução de teste, servidor, cache ou cluster. 🟡 Relação com tarefas indica plano proposto; não trabalho implementado.

## Fontes Java próprias e inicialização

| Origem | Responsabilidade confirmada | Requisitos / lacunas | Tarefas propostas |
|---|---|---|---|
| 🟢 [CacheConfiguration.java:1](../../src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java#L1) | EnableCaching, customizador JCache vets e estatísticas | SIS-RF-04; D-04 | 🟡 T-05; TT-04 |
| 🟢 [CrashController.java:1](../../src/main/java/org/springframework/samples/petclinic/system/CrashController.java#L1) | GET /oups e RuntimeException demonstrativa | SIS-RF-02; SIS-D-03 | 🟡 T-03; TT-02 |
| 🟢 [WebConfiguration.java:1](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L1) | LocaleResolver, idioma inglês, interceptor lang e registro | SIS-RF-03; SIS-D-02 | 🟡 T-04; TT-03 |
| 🟢 [WelcomeController.java:1](../../src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java#L1) | GET / e view welcome | SIS-RF-01 | 🟡 T-02; TT-01 |
| 🟢 [package-info.java:1](../../src/main/java/org/springframework/samples/petclinic/system/package-info.java#L1) | Declaração/licença do pacote; sem requisito independente | n/a | 🟡 n/a; inventário |
| 🟢 [PetClinicApplication.java:1](../../src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java#L1) | main e importação de RuntimeHints | SIS-RF-05 | 🟡 T-01; TT-05 |
| 🟢 [PetClinicRuntimeHints.java:1](../../src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java#L1) | Padrões de recursos e JavaSerialization BaseEntity/Person/Vet | SIS-RF-05; SIS-INV-04 | 🟡 T-01; TT-05 |

## Índice de símbolos

🟢 CONFIRMADO — Índice lexical de declarações, não grafo completo de execução/autoconfiguração.

### CacheConfiguration.java

- 🟢 `petclinicCacheConfigurationCustomizer` — [CacheConfiguration.java:36](../../src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java#L36).
- 🟢 `cacheConfiguration` — [CacheConfiguration.java:49](../../src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java#L49).

### CrashController.java

- 🟢 `triggerException` — [CrashController.java:32](../../src/main/java/org/springframework/samples/petclinic/system/CrashController.java#L32).

### WebConfiguration.java

- 🟢 `localeResolver` — [WebConfiguration.java:33](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L33).
- 🟢 `localeChangeInterceptor` — [WebConfiguration.java:45](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L45).
- 🟢 `addInterceptors` — [WebConfiguration.java:56](../../src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java#L56).

### WelcomeController.java

- 🟢 `welcome` — [WelcomeController.java:26](../../src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java#L26).

### PetClinicApplication.java

- 🟢 `main` — [PetClinicApplication.java:32](../../src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java#L32).

### PetClinicRuntimeHints.java

- 🟢 `registerHints` — [PetClinicRuntimeHints.java:28](../../src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java#L28).

## Configuração e views

| Arquivo | Papel confirmado | Vínculo |
|---|---|---|
| 🟢 [application.properties:1](../../src/main/resources/application.properties#L1) | Defaults de SQL/JPA/Thymeleaf/i18n/logging/Actuator/cache HTTP | SIS-RF-06; D-03/04 |
| 🟢 [application-mysql.properties:1](../../src/main/resources/application-mysql.properties#L1) | Perfil mysql e placeholders MYSQL_URL/USER/PASS | SIS-RF-06; SIS-INV-05 |
| 🟢 [application-postgres.properties:1](../../src/main/resources/application-postgres.properties#L1) | Perfil postgres e placeholders POSTGRES_URL/USER/PASS | SIS-RF-06; SIS-INV-05 |
| 🟢 [welcome.html:1](../../src/main/resources/templates/welcome.html#L1) | View da página inicial | SIS-RF-01 |
| 🟢 [error.html:1](../../src/main/resources/templates/error.html#L1) | Mensagens por status e message do contexto de erro | SIS-RF-02; SIS-D-03 |
| 🟢 [layout.html:1](../../src/main/resources/templates/fragments/layout.html#L1) | Layout e navegação transversais | Views de todas as units |
| 🟢 [banner.txt:1](../../src/main/resources/banner.txt#L1) | Recurso de apresentação de startup | Inicialização; sem requisito de domínio independente |

### Bundles de mensagem

🟢 CONFIRMADO — Arquivos existentes abaixo são recursos de i18n. Presença de arquivo não comprova qualidade de tradução, fallback efetivo ou lista de idiomas de produto aprovada.

- 🟢 [messages.properties:1](../../src/main/resources/messages/messages.properties#L1).
- 🟢 [messages_de.properties:1](../../src/main/resources/messages/messages_de.properties#L1).
- 🟢 [messages_en.properties:1](../../src/main/resources/messages/messages_en.properties#L1).
- 🟢 [messages_es.properties:1](../../src/main/resources/messages/messages_es.properties#L1).
- 🟢 [messages_fa.properties:1](../../src/main/resources/messages/messages_fa.properties#L1).
- 🟢 [messages_hi.properties:1](../../src/main/resources/messages/messages_hi.properties#L1).
- 🟢 [messages_ja.properties:1](../../src/main/resources/messages/messages_ja.properties#L1).
- 🟢 [messages_ko.properties:1](../../src/main/resources/messages/messages_ko.properties#L1).
- 🟢 [messages_pt.properties:1](../../src/main/resources/messages/messages_pt.properties#L1).
- 🟢 [messages_ru.properties:1](../../src/main/resources/messages/messages_ru.properties#L1).
- 🟢 [messages_tr.properties:1](../../src/main/resources/messages/messages_tr.properties#L1).

## Testes próprios

🟢 CONFIRMADO — Classes/métodos localizados abaixo; não executados nesta extração. Asserções e limites foram lidos nas etapas anteriores.

### CrashControllerIntegrationTests.java — 2 declarações Test

- 🟢 `triggerExceptionJson` — [CrashControllerIntegrationTests.java:61](../../src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java#L61).
- 🟢 `triggerExceptionHtml` — [CrashControllerIntegrationTests.java:77](../../src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java#L77).

### CrashControllerTests.java — 1 declarações Test

- 🟢 `triggerException` — [CrashControllerTests.java:35](../../src/test/java/org/springframework/samples/petclinic/system/CrashControllerTests.java#L35).

### I18nPropertiesSyncTest.java — 2 declarações Test

- 🟢 `checkNonInternationalizedStrings` — [I18nPropertiesSyncTest.java:39](../../src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java#L39).
- 🟢 `checkI18nPropertyFilesAreInSync` — [I18nPropertiesSyncTest.java:86](../../src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java#L86).

### WelcomeControllerTests.java — 1 declarações Test

- 🟢 `welcome` — [WelcomeControllerTests.java:38](../../src/test/java/org/springframework/samples/petclinic/system/WelcomeControllerTests.java#L38).

🟢 CONFIRMADO — WelcomeControllerTests espera 200/view welcome; CrashControllerTests espera exceção direta. CrashControllerIntegrationTests espera 500 JSON/HTML com include-message ALWAYS e access.default none, excluindo autoconfiguração datasource/JPA do contexto de teste. I18nPropertiesSyncTest verifica textos HTML/chaves, não experiência completa de sessão ou tradução semântica.

## Operação e fontes compartilhadas

| Origem | Responsabilidade confirmada | Limite |
|---|---|---|
| 🟢 [pom.xml:1](../../pom.xml#L1) | Build/dependências Maven, imagem via plugin no README | Não executado; não comprova equivalência Gradle |
| 🟢 [build.gradle:1](../../build.gradle#L1) | Build/dependências Gradle | Configuração não comprova build aprovado |
| 🟢 [README.md:1](../../README.md#L1) | Instruções de execução/perfis/imagem | Não é execução nem prova de imagem remota atual |
| 🟢 [docker-compose.yml:1](../../docker-compose.yml#L1) | Serviços MySQL/PostgreSQL, não aplicação | Não prova volumes/conexões implantados |
| 🟢 [petclinic.yml:1](../../k8s/petclinic.yml#L1) | Service/Deployment de aplicação, imagem, bindings e probes | SIS-D-04: imagem versus checkout não comprovados |
| 🟢 [db.yml:1](../../k8s/db.yml#L1) | Secret, Service e Deployment PostgreSQL | Configuração de exemplo; não prova persistência/recuperação |
| 🟢 [maven-build.yml:1](../../.github/workflows/maven-build.yml#L1) | CI com Maven verify | Não prova status da execução atual |
| 🟢 [gradle-build.yml:1](../../.github/workflows/gradle-build.yml#L1) | CI com Gradle build | Não prova status da execução atual |
| 🟢 [deploy-and-test-cluster.yml:1](../../.github/workflows/deploy-and-test-cluster.yml#L1) | Kind, apply e espera por readiness de pods | Não constrói imagem do checkout nem define teste HTTP funcional |
| 🟢 [VetRepository.java:1](../../src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java#L1) | Consumidor de cache vets | Não confirma provedor de runtime |
| 🟢 [BaseEntity.java:1](../../src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java#L1) | Tipo registrado para serialização Java | Contrato de dados pertence a modelo |
| 🟢 [Person.java:1](../../src/main/java/org/springframework/samples/petclinic/model/Person.java#L1) | Tipo registrado para serialização Java | Contrato de dados pertence a modelo |
| 🟢 [Vet.java:1](../../src/main/java/org/springframework/samples/petclinic/vet/Vet.java#L1) | Tipo registrado para serialização Java | Contrato de domínio pertence a veterinarios |

🟢 CONFIRMADO — SQL é compartilhado por perfis/units:

- 🟢 [data.sql:1](../../src/main/resources/db/h2/data.sql#L1).
- 🟢 [schema.sql:1](../../src/main/resources/db/h2/schema.sql#L1).
- 🟢 [data.sql:1](../../src/main/resources/db/mysql/data.sql#L1).
- 🟢 [schema.sql:1](../../src/main/resources/db/mysql/schema.sql#L1).
- 🟢 [user.sql:1](../../src/main/resources/db/mysql/user.sql#L1).
- 🟢 [data.sql:1](../../src/main/resources/db/postgres/data.sql#L1).
- 🟢 [schema.sql:1](../../src/main/resources/db/postgres/schema.sql#L1).

## Ligações das lacunas

| Lacuna | Fonte principal | Artefatos afetados |
|---|---|---|
| 🔴 D-03 / SIS-D-01 | application.properties, CrashController e ambiente externo desconhecido | Acesso/Actuator/probes; T-03/07 e TT-02/06 |
| 🔴 D-04 | CacheConfiguration, VetRepository, dependências | Cache declarado versus ativo; T-05/TT-04 |
| 🔴 SIS-D-02 | WebConfiguration, bundles e teste i18n | Idiomas/fallback/sessão; T-04/TT-03 |
| 🔴 SIS-D-03 | error.html e overrides de teste | Conteúdo de erro; T-03/TT-02 |
| 🔴 SIS-D-04 | k8s/petclinic.yml e imagem externa | Artefato/bindings/probes; T-07/TT-05/06 |
| 🔴 SIS-INV-01–05 | Views, locale, cache, hints e datasource | Falhas ainda não caracterizadas nos respectivos contextos |

## Cobertura e limites

🟢 CONFIRMADO — Mapeados cinco arquivos Java do pacote system, dois arquivos de inicialização no pacote raiz, quatro classes de teste próprias com 6 declarações Test, dois templates básicos e propriedades/recursos de configuração. Contagem é inventário, não cobertura de linhas nem validação de runtime.

🟢 CONFIRMADO — system não cria tabela de negócio nem endpoint de alteração de configuração/cache. Probes e Actuator são infraestrutura de framework/manifests, não handlers próprios adicionais desses controllers. package-info.java não contém requisito funcional independente.

🟡 INFERIDO — Recursos compartilhados mantêm referências às units consumidoras. A organização de specs não isola processo nem propriedade exclusiva de banco/idioma/layout; ver [matriz de impacto](../traceability/spec-impact-matrix.md).

## Artefatos da unit

🟢 CONFIRMADO — [requirements.md](requirements.md), [design.md](design.md), [tasks.md](tasks.md), [contracts.md](contracts.md), [edge-cases.md](edge-cases.md) e [questions.md](questions.md) foram gerados antes deste mapeamento. Este é o sétimo arquivo da unit. As perguntas e tarefas permanecem abertas; próximo estágio do plano gera artefatos globais.
