# Sistema — Contratos de interface e configuração

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

🟢 CONFIRMADO — Contratos extraídos de system, inicialização e propriedades compartilhadas. Complementam [requirements.md](requirements.md), [design.md](design.md) e [tasks.md](tasks.md). Não houve chamadas HTTP, execução de aplicação/testes ou inspeção de configuração secreta do ambiente.

🟢 CONFIRMADO — Configuração declarada, expectativa de teste e resposta efetiva são evidências diferentes. Expectativas de teste citadas não significam aprovação nesta sessão. Não há API de CRUD de configurações implementada nesses arquivos.

## SIS-HTTP-01 — GET /

| Item | Contrato extraído |
|---|---|
| 🟢 Entrada | GET /; welcome não declara parâmetros |
| 🟢 Handler | WelcomeController.welcome(): String |
| 🟢 Retorno | Nome de view welcome, correspondente a templates/welcome.html |
| 🟢 Dependência | Thymeleaf, layout e recursos compartilhados |
| 🟢 Expectativa de teste | WelcomeControllerTests espera 200 e view welcome |
| 🔴 Falha | Resolução/renderização indisponível não tem fallback local definido; SIS-INV-01 |

🟢 CONFIRMADO — Nenhum ResponseBody, ResponseEntity ou produces é declarado nesse handler. 🟡 HTML é a representação esperada da view; Content-Type, charset e headers finais não foram capturados.

```http
GET / HTTP/1.1
Accept: text/html
```

🟡 INFERIDO — Exemplo construído do mapping, sem execução. Não é exemplo de response body literal.

## SIS-HTTP-02 — GET /oups

| Item | Contrato extraído |
|---|---|
| 🟢 Entrada | GET /oups; triggerException não declara parâmetros |
| 🟢 Handler | CrashController.triggerException(): String, sem retorno normal |
| 🟢 Efeito | Lança RuntimeException |
| 🟢 Mensagem literal | Expected: controller used to showcase what happens when an exception is thrown |
| 🟢 Tratamento local | Nenhum catch/ResponseStatus próprio; erro é delegado ao framework |
| 🔴 Implantação | Conteúdo negociado, exposição da mensagem e controles de acesso não capturados no ambiente real |

### Expectativas no contexto de teste

🟢 CONFIRMADO — CrashControllerTests espera diretamente RuntimeException com trecho da mensagem. CrashControllerIntegrationTests usa servidor RANDOM_PORT e AutoConfigureTestRestTemplate com overrides:

```properties
spring.web.error.include-message=ALWAYS
management.endpoints.access.default=none
```

🟢 CONFIRMADO — TestConfiguration desse teste exclui DataSourceAutoConfiguration, DataSourceTransactionManagerAutoConfiguration e HibernateJpaAutoConfiguration. Portanto esse contexto não comprova inicialização do banco da aplicação completa.

| Cenário do teste | Expectativa declarada |
|---|---|
| 🟢 triggerExceptionJson | Status 500 e body com timestamp, status, error, message literal e path=/oups |
| 🟢 triggerExceptionHtml | Accept text/html, status 500, body com título Something happened... e mensagem demonstrativa; sem Whitelabel Error Page |

🔴 SIS-D-03 — include-message ALWAYS é override de teste. Não foi encontrado como default em application.properties; não se pode prometer que implantação expõe a mensagem. O teste JSON não informa tipos completos/obrigatoriedade de todos os campos possíveis nem define resposta universal para outras exceções.

### View de erro

🟢 CONFIRMADO — templates/error.html consome atributos do contexto de erro do framework, não payload de endpoint de domínio:

| Atributo / elemento | Uso |
|---|---|
| 🟢 status | 404 → chave error.404; 500 → error.500; demais → error.general |
| 🟢 message | Exibido via th:text em parágrafo |
| 🟢 somethingHappened | Chave de título traduzível |
| 🟢 Imagem/layout | Recurso pets.png e fragments/layout |

🟢 CONFIRMADO — A existência de ramo 404 no template não prova que IllegalArgumentException dos outros módulos é mapeada a 404. Não há @GetMapping("/error") próprio nas classes system lidas. 🔴 O resultado de falha do próprio template não foi caracterizado.

## SIS-CFG-01 — Idioma por sessão

| Contrato declarado | Valor / mecanismo |
|---|---|
| 🟢 Resolver | SessionLocaleResolver |
| 🟢 Padrão | Locale.ENGLISH |
| 🟢 Troca | LocaleChangeInterceptor, paramName=lang |
| 🟢 Registro | WebConfiguration.addInterceptors registra o interceptor |
| 🟢 Mensagens | spring.messages.basename=messages/messages |

🟢 CONFIRMADO — lang é parâmetro transversal de requisição MVC, não endpoint isolado nem campo de entidade persistida. Não há allowlist local ou vínculo com identidade autenticada nesse código. 🟡 Preferência mantida entre requisições da mesma sessão é o fluxo esperado pela configuração, ainda sem captura desta execução.

```http
GET /?lang=pt HTTP/1.1
Accept: text/html
```

🟡 INFERIDO — Exemplo de uso derivado do interceptor; não garante texto exato ou persistência de sessão sem caracterização. 🔴 SIS-D-02/SIS-INV-02 — Locale inválido, nova sessão, cookies, fallback de chave/idioma e parâmetros repetidos devem ser observados no contexto escolhido. Não inferir que presence de bundle garante tradução completa/correta.

## SIS-CFG-02 — Cache de dados

🟢 CONFIRMADO — CacheConfiguration tem EnableCaching e bean JCacheManagerCustomizer. O customizador, quando aplicado a JCache compatível, chama createCache("vets",configuration); configuração é MutableConfiguration com statisticsEnabled=true. Não há contrato HTTP de criação/invalidação do cache nessas classes.

🟢 CONFIRMADO — VetRepository anota findAll() e findAll(Pageable) com Cacheable("vets") e transação readOnly. Esse nome é dependência interna compartilhada. Não define ETag, Last-Modified ou TTL de cache HTTP de /vets.

🔴 D-04 — CacheManager ativo, TTL/capacidade/chaves/eviction, fallback de falha e invalidação não comprovados. Caffeine runtime e customizador JCache coexistem como declarações; não concluir que ambos são aplicados. Estatísticas configuradas não provam JMX ou métricas acessíveis.

## SIS-CFG-03 — Propriedades e ambientes

### SQL e datasource

| Origem | Configuração declarada | Limite |
|---|---|---|
| 🟢 application.properties | database=h2; schema/data em classpath*:db/${database}/schema.sql e data.sql | Conexão/startup não executados |
| 🟢 application-mysql.properties | database=mysql; URL ${MYSQL_URL:jdbc:mysql://localhost/petclinic}, usuário ${MYSQL_USER:petclinic}, senha ${MYSQL_PASS:petclinic}; SQL init always | Defaults demonstrativos não comprovam ambiente implantado |
| 🟢 application-postgres.properties | database=postgres; URL ${POSTGRES_URL:jdbc:postgresql://localhost/petclinic}, usuário ${POSTGRES_USER:petclinic}, senha ${POSTGRES_PASS:petclinic}; SQL init always | Não consultar credenciais reais para completar este contrato |

🟢 CONFIRMADO — Essas são entradas de configuração, não campos de API HTTP. Overrides externos podem alterar valores efetivos. H2 schema contém DROP TABLE; os scripts de seed e schema não são contrato de migração segura para base existente. 🔴 SIS-INV-05 — Falha de banco/credencial/schema e política de recuperação não caracterizadas.

### JPA, recursos e observabilidade

| Chave | Valor declarado | Significado limitado ao código |
|---|---|---|
| 🟢 spring.jpa.hibernate.ddl-auto | none | Usa SQL próprio, sem presumir geração de tabelas por anotações |
| 🟢 spring.jpa.open-in-view | false | Configuração declarada; perfil de consulta não medido |
| 🟢 spring.jpa.hibernate.naming.physical-strategy | PhysicalNamingStrategySnakeCaseImpl no pacote Boot Hibernate | Estratégia de nomes configurada |
| 🟢 spring.jpa.properties.hibernate.default_batch_fetch_size | 16 | Não é limite global de resultados nem SLA |
| 🟢 spring.thymeleaf.mode | HTML | Modo de templates |
| 🟢 logging.level.org.springframework | INFO | DEBUG/TRACE comentados não são valores ativos deste arquivo |
| 🟢 spring.web.resources.cache.cachecontrol.max-age | 12h | Configuração de recursos estáticos; headers não capturados e não é TTL de vets |
| 🟢 management.endpoints.web.exposure.include | * | Seleção de exposição; acesso/disponibilidade concretos dependem de framework/configuração |

🔴 D-03/SIS-D-01 — Propriedades de seleção de Actuator não definem, sozinhas, política de autenticação/ACL. Não enumerar todo endpoint Actuator como disponível/publicamente acessível por causa de *.

### Probes e implantação

🟢 CONFIRMADO — k8s/petclinic.yml declara probes HTTP /livez e /readyz e SPRING_APPLICATION_JSON com health probes adicionais. Não são handlers próprios dos controllers desta unit. Service petclinic expõe porta 80 para target 8080; imagem dsyer/petclinic sem tag explícita e perfil postgres são configuração do manifesto.

🔴 SIS-D-04 — Não se comprovou que essa imagem corresponde ao checkout, resolve SERVICE_BINDING_ROOT e funciona com o secret/probes declarados. Não prometer 200 nas probes sem execução. Ver [deployment.md](../deployment.md).

## SIS-CFG-04 — Inicialização e hints

🟢 CONFIRMADO — PetClinicApplication.main(String[]) delega a SpringApplication.run e importa PetClinicRuntimeHints. registerHints(RuntimeHints,ClassLoader) registra padrões de recurso db/*, db/*/*, messages/*, mysql-default-conf e JavaSerialization BaseEntity/Person/Vet. O parâmetro classLoader não é usado no corpo.

🔴 SIS-INV-04 — Registro não prova recursos no artefato, compatibilidade de objetos serializados antigos ou sucesso nativo. Nenhuma API remota de bootstrap/hints é criada por esse código.

## Falhas e garantias não estabelecidas

| Situação | Garantia local | Pendência |
|---|---|---|
| 🟢 /oups | Exceção demonstrativa sempre lançada | 🔴 Representação/segurança de ambiente real |
| 🟢 View ausente | Sem fallback de WelcomeController | 🔴 SIS-INV-01 |
| 🟢 Locale inválido | Sem tratamento específico de WebConfiguration | 🔴 SIS-INV-02 |
| 🟢 Cache existente/indisponível | Sem catch no customizador | 🔴 SIS-INV-03 / D-04 |
| 🟢 Recurso/hint ausente | Sem verificação própria de artefato | 🔴 SIS-INV-04 |
| 🟢 Banco indisponível | Conexão delegada à infraestrutura | 🔴 SIS-INV-05 |

🟢 CONFIRMADO — Não há contrato local de retry, idempotency-key, auditoria de acesso ou versionamento HTTP nessas classes. 🟡 Um contrato de destino que exija tais comportamentos deve distinguir evolução desejada de extração do legado.

## Rastreabilidade

| Contrato | Requisitos / verificações propostas |
|---|---|
| 🟢 SIS-HTTP-01 | SIS-RF-01; SIS-CA-01; T-02/TT-01 |
| 🟢 SIS-HTTP-02 | SIS-RF-02; SIS-CA-02–04; T-03/TT-02 |
| 🟢 SIS-CFG-01 | SIS-RF-03; SIS-CA-05/06; T-04/TT-03 |
| 🟢 SIS-CFG-02 | SIS-RF-04; SIS-CA-07; T-05/TT-04 |
| 🟢 SIS-CFG-03 | SIS-RF-06; SIS-CA-10/11; T-06/07 e TT-06 |
| 🟢 SIS-CFG-04 | SIS-RF-05; SIS-CA-08/09; T-01/TT-05 |

🟡 INFERIDO — Vínculos a tarefas são plano de realização. 🟢 Fontes primárias: system/{WelcomeController,CrashController,WebConfiguration,CacheConfiguration}.java no pacote petclinic, PetClinicApplication.java, PetClinicRuntimeHints.java, application*.properties, templates/error.html, testes system e manifests k8s. Caminhos completos em requirements.md; todos os resultados de runtime não observados permanecem identificados.
