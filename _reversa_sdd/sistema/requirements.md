# Sistema e infraestrutura compartilhada — Requisitos

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


## Visão Geral

🟢 CONFIRMADO — A unit system → sistema cobre idioma, cache, boas-vindas e erro demonstrativo. Também referencia inicialização no pacote raiz e configuração operacional transversal, sem criar módulo funcional novo. Extração estática: aplicação, testes, banco e cluster não foram executados.

## Responsabilidades

- 🟢 Atender GET / com view welcome e GET /oups com exceção demonstrativa.
- 🟢 Configurar idioma padrão inglês, resolução por sessão e troca por lang.
- 🟢 Habilitar infraestrutura de cache consumida pelo módulo vet.
- 🟢 Inicializar Spring Boot, hints e propriedades compartilhadas de runtime.

## Regras de Negócio e Configuração

| ID | Declaração extraída | Fonte |
|---|---|---|
| SIS-RN-01 | 🟢 WelcomeController.welcome retorna welcome; CrashController.triggerException lança RuntimeException com mensagem demonstrativa. | Controllers de system |
| SIS-RN-02 | 🟢 SessionLocaleResolver tem default Locale.ENGLISH; LocaleChangeInterceptor usa lang e é registrado no InterceptorRegistry. Sem allowlist local de idiomas. | WebConfiguration |
| SIS-RN-03 | 🟢 EnableCaching e JCacheManagerCustomizer declaram criação de vets com MutableConfiguration e estatísticas true quando aplicado; isso não prova provedor ativo. | CacheConfiguration |
| SIS-RN-04 | 🟢 main delega SpringApplication.run e importa PetClinicRuntimeHints. | PetClinicApplication |
| SIS-RN-05 | 🟢 Hints incluem db/*, db/*/*, messages/*, mysql-default-conf e JavaSerialization para BaseEntity, Person e Vet. | PetClinicRuntimeHints |
| SIS-RN-06 | 🟢 database=h2 por padrão; schema/data interpolam database. Perfis mysql/postgres substituem database e datasource, com SQL init always. | application*.properties |
| SIS-RN-07 | 🟢 JPA: ddl-auto=none, open-in-view=false, PhysicalNamingStrategySnakeCaseImpl, batch_fetch_size=16. | application.properties |
| SIS-RN-08 | 🟢 Thymeleaf HTML, basename messages/messages, Spring logging INFO e cache HTTP estático max-age=12h. | application.properties |
| SIS-RN-09 | 🟢 Actuator exposure.include=* é seleção declarada, com comentário para desenvolvimento/teste; acesso real não foi comprovado. | application.properties |
| SIS-RN-10 | 🟢 error.html escolhe mensagens para status 404/500/outro e exibe message quando disponível no contexto. | templates/error.html |

## Requisitos Funcionais

🟢 CONFIRMADO — Comportamentos abaixo são declarações de código. 🟡 Prioridades propostas, sem métricas de uso; nomes de fonte são detalhados na rastreabilidade.

| ID | Requisito | Prioridade proposta | Origem | Critérios |
|---|---|---|---|---|
| SIS-RF-01 | 🟢 Servir welcome em GET /. | 🟡 Must | WelcomeController.java | SIS-CA-01 |
| SIS-RF-02 | 🟢 Disparar exceção demonstrativa em /oups e integrar apresentação de erro. | 🟡 Could | CrashController.java | SIS-CA-02/03/04 |
| SIS-RF-03 | 🟢 Configurar idioma inglês padrão, preferência em sessão e parâmetro lang. | 🟡 Must | WebConfiguration.java | SIS-CA-05/06 |
| SIS-RF-04 | 🟢 Habilitar caching e declarar customizador JCache vets com estatísticas. | 🟡 Must | CacheConfiguration.java | SIS-CA-07 |
| SIS-RF-05 | 🟢 Inicializar Spring Boot e registrar hints de recursos/serialização. | 🟡 Must | PetClinicApplication.java; PetClinicRuntimeHints.java | SIS-CA-08/09 |
| SIS-RF-06 | 🟢 Preservar configurações SQL/JPA/i18n e alternativas de datasource por perfil. | 🟡 Must | application*.properties | SIS-CA-10/11 |

## Requisitos Não Funcionais

| Tipo | Configuração extraída | Evidência / limite |
|---|---|---|
| 🟢 Cache HTTP | Recursos estáticos com max-age=12h | application.properties; headers reais não capturados. Não é TTL do cache vets. |
| 🟢 Persistência | SQL próprio, ddl-auto=none e open-in-view=false | application.properties; execução por perfil pendente |
| 🟢 Carregamento JPA | batch_fetch_size=16 | Declaração, sem ganho de desempenho medido |
| 🟢 Observabilidade | Actuator seleciona endpoints, Spring INFO e estatísticas na configuração JCache | 🔴 Acesso/provedor/estatísticas efetivos não comprovados |
| 🟢 Empacotamento nativo | Hints de recursos e serialização Java | 🔴 Build/execução nativa não comprovados |

🟢 CONFIRMADO — Não há SLA, retry próprio, autenticação local ou limite/TTL explícito do cache de dados nas fontes examinadas. Nenhuma garantia quantitativa nova foi inferida.

## Critérios de Aceitação

🟢 CONFIRMADO — Cenários derivados de configuração/métodos ou expectativas de teste; não executados nesta extração. Quando o teste tem override, o contexto é parte obrigatória do critério.

```gherkin
Cenário SIS-CA-01: Página inicial
Dado o WelcomeController disponível
Quando welcome atende GET /
Então o método retorna a view welcome
```

```gherkin
Cenário SIS-CA-02: Exceção demonstrativa
Dado CrashController instanciado
Quando triggerException é invocado
Então lança RuntimeException com a mensagem demonstrativa definida no código
```

```gherkin
Cenário SIS-CA-03: Erro JSON no teste legado
Dado a configuração de CrashControllerIntegrationTests com include-message ALWAYS e servidor de teste
Quando o cenário triggerExceptionJson é executado
Então o teste espera status 500 e campos timestamp, status, error, message demonstrativa e path de /oups
```

```gherkin
Cenário SIS-CA-04: Erro HTML no teste legado
Dado a mesma configuração de teste com view error disponível
Quando triggerExceptionHtml solicita Accept text/html
Então o teste espera 500, título e mensagem demonstrativa no HTML e ausência da página Whitelabel
```

```gherkin
Cenário SIS-CA-05: Padrão de idioma
Dado o SessionLocaleResolver produzido pelo bean localeResolver
Quando sua configuração padrão é inspecionada
Então defaultLocale é Locale.ENGLISH
```

```gherkin
Cenário SIS-CA-06: Parâmetro de troca
Dado o interceptor produzido por localeChangeInterceptor
Quando sua configuração e registro são verificados
Então o parâmetro é lang e o interceptor é registrado em addInterceptors
```

```gherkin
Cenário SIS-CA-07: Customizar JCache
Dado um CacheManager JCache compatível com criação de cache
Quando o customizador de CacheConfiguration é aplicado
Então solicita criar vets com configuração MutableConfiguration e estatísticas habilitadas
```

```gherkin
Cenário SIS-CA-08: Inicialização
Dado a classe PetClinicApplication
Quando main recebe argumentos
Então delega a SpringApplication.run com essa classe e os argumentos
```

```gherkin
Cenário SIS-CA-09: Hints
Dado um RuntimeHints para registro
Quando registerHints é chamado
Então registra padrões db/*, db/*/*, messages/*, mysql-default-conf e JavaSerialization para BaseEntity, Person e Vet
```

```gherkin
Cenário SIS-CA-10: Configuração padrão
Dado application.properties sem overrides
Quando suas propriedades são carregadas
Então database é h2 e caminhos SQL usam db/${database}/schema.sql e data.sql com ddl-auto none
```

```gherkin
Cenário SIS-CA-11: Alternativa de banco
Dado os arquivos de perfil mysql e postgres
Quando o perfil correspondente é selecionado
Então database e datasource usam propriedades desse perfil e placeholders definidos, com SQL init always
```

🟢 CONFIRMADO — include-message ALWAYS e management.endpoints.access.default=none são overrides de CrashControllerIntegrationTests, não regra global de produção. WelcomeControllerTests espera 200/view welcome; CrashControllerTests espera a exceção diretamente. Expectativas não significam aprovação nesta sessão.

### Falhas ainda não caracterizadas

- 🔴 SIS-INV-01 — Falha de resolução/renderização de welcome: sem fallback local; caracterizar se necessária ao destino.
- 🔴 SIS-INV-02 — Locale inválido, sessão nova e fallback de chave ausente: configuração não comprova resposta/fallback de runtime.
- 🔴 SIS-INV-03 — Cache existente/falha de criação/provedor indisponível: sem tratamento local definido; não escolher status HTTP arbitrário.
- 🔴 SIS-INV-04 — Falha de startup/hints/serialização nativa: validar artefato/dependências se exigido, sem declarar build aprovado.
- 🔴 SIS-INV-05 — Banco indisponível, credenciais inválidas ou schema incompatível: propriedades não comprovam conexão nem política de recuperação.

## Prioridade (MoSCoW)

| Requisito | Proposta | Justificativa |
|---|---|---|
| SIS-RF-01 | 🟡 Must | Entrada de navegação com teste dedicado |
| SIS-RF-02 | 🟡 Could | Rota explicitamente demonstrativa, fora do fluxo principal de cadastro |
| SIS-RF-03 | 🟡 Must | Idioma transversal às views |
| SIS-RF-04 | 🟡 Must | Consultas vet usam cache anotado |
| SIS-RF-05 | 🟡 Must | Inicialização e empacotamento |
| SIS-RF-06 | 🟡 Must | Persistência e configurações transversais |

## Riscos e Lacunas

- 🔴 D-03 / SIS-D-01: confirmar proteção de Actuator e /oups nos ambientes reais; ausência de autenticação local não prova exposição pública.
- 🔴 D-04: identificar CacheManager, limites, expiração, invalidação e aplicação do customizador.
- 🔴 SIS-D-02: confirmar idiomas/fallback e lang inválido; teste de chaves não comprova tradução correta nem troca de sessão.
- 🔴 SIS-D-03: confirmar política de conteúdo de erro; override ALWAYS de teste não é requisito de produção.
- 🔴 SIS-D-04: confirmar correspondência da imagem dsyer/petclinic ao checkout e service binding efetivo; manifests não comprovam conexão/execução da imagem local.
- 🟢 Configurações do legado não são autorização para publicar endpoints, executar SQL ou alterar credenciais.

## Rastreabilidade de Código

| Origem | Cobertura |
|---|---|
| 🟢 src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java | SIS-RF-01 |
| 🟢 src/main/java/org/springframework/samples/petclinic/system/CrashController.java | SIS-RF-02 |
| 🟢 src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java | SIS-RF-03 |
| 🟢 src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java | SIS-RF-04 |
| 🟢 src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java | SIS-RF-05 |
| 🟢 src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java | SIS-RF-05 |
| 🟢 src/main/resources/application.properties | SIS-RF-06 e RNFs |
| 🟢 src/main/resources/application-mysql.properties | Perfil MYSQL_URL/USER/PASS |
| 🟢 src/main/resources/application-postgres.properties | Perfil POSTGRES_URL/USER/PASS |
| 🟢 src/main/resources/templates/error.html | Apresentação de erro |
| 🟢 src/main/resources/templates/welcome.html | Boas-vindas |
| 🟢 src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java | Expectativas JSON/HTML de erro em contexto de teste |
| 🟢 src/test/java/org/springframework/samples/petclinic/system/CrashControllerTests.java | Exceção direta |
| 🟢 src/test/java/org/springframework/samples/petclinic/system/WelcomeControllerTests.java | View/status esperados |
| 🟢 src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java | Verifica textos HTML/chaves, não tradução semântica |

🟢 CONFIRMADO — Contexto adicional em [deployment.md](../deployment.md), [arquitetura](../architecture.md) e [análise técnica](../code-analysis.md). Build e implantação são compartilhados; nenhum comando de aplicação foi executado.
