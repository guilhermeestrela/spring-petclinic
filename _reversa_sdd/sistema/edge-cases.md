# Sistema — Casos de borda

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

🟢 CONFIRMADO — Catálogo complementar a [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md). Nenhum caso foi executado; configuração declarada e expectativa de teste não equivalem a operação implantada.

🟢 CONFIRMADO — 🟢 identifica evidência de fonte; 🟡 inferência/proposta; 🔴 resultado/decisão ainda não comprovados. 🟡 A coluna de verificação propõe caracterização futura, não autoriza alterar ambiente, código ou exposição de endpoints.

## Catálogo

| ID | Condição | Comportamento e limite | Verificação proposta | Origem | Rastreabilidade |
|---|---|---|---|---|---|
| SIS-EC-01 | Troca de idioma com sessão nova ou mantida | 🟢 Resolver é SessionLocaleResolver com default inglês, interceptor usa lang. 🔴 Persistência de preferência/cookie real não caracterizada. | Comparar requisições com mesma sessão e sessão nova; não confundir sessão de idioma com autenticação. | src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java | SIS-RF-03; SIS-D-02; TT-03 |
| SIS-EC-02 | Locale inválido ou parâmetro repetido | 🟢 Não há allowlist/handler próprio de lang inválido. 🔴 Conversão, precedência e resposta dependem do framework. | Caracterizar valores inválidos, vazio e repetidos sem inventar lista de idiomas ou status 400. | src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java | SIS-INV-02; TT-03 |
| SIS-EC-03 | Chave ausente ou tradução incompleta | 🟢 basename é messages/messages; teste compara chaves e procura literais HTML. 🔴 Qualidade/fallback de runtime não comprovados. | Distinguir sincronismo de chaves, tradução semântica e texto renderizado por locale. | src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java | SIS-D-02; TT-03 |
| SIS-EC-04 | Mensagem de exceção fora do contexto de teste | 🟢 Teste de integração força include-message ALWAYS; application.properties não declara esse default. 🔴 Conteúdo efetivo implantado desconhecido. | Comparar ambientes de teste/implantação autorizada antes de definir política de detalhe de erro. | src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java | SIS-D-03; TT-02 |
| SIS-EC-05 | View welcome/error ausente ou falha de renderização | 🟢 Controllers não implementam fallback de view; template de erro usa layout/recursos. 🔴 Falha na própria apresentação não executada. | Caracterizar artefato com recursos e resolução de view; não pressupor resposta HTML/JSON fixa. | src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java | SIS-INV-01; TT-01/02 |
| SIS-EC-06 | Status não previsto no template | 🟢 error.html usa chaves específicas para 404/500 e error.general no ramo restante; message depende do contexto. | Testar renderização com status conhecidos/outro e message ausente, sem inferir mapeamento de exceções só pelo template. | src/main/resources/templates/error.html | SIS-RF-02; TT-02 |
| SIS-EC-07 | JCache não é provedor ativo | 🟢 Há customizador JCache e Caffeine runtime; isso não prova que createCache seja chamado. 🔴 Seleção efetiva desconhecida. | Identificar CacheManager/provedor real no contexto; não presumir erro nem aplicação do customizador a todo cache. | src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java | D-04; TT-04 |
| SIS-EC-08 | Cache vets já existe ou criação falha | 🟢 Customizador chama createCache sem catch; MutableConfiguration habilita estatísticas. 🔴 Resultado depende de aplicação/provedor. | Caracterizar aplicação do customizador em contexto compatível, cache preexistente e erro, sem inventar retry/fallback. | src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java | SIS-INV-03; TT-04 |
| SIS-EC-09 | Confusão entre max-age estático e cache de dados | 🟢 12h está em spring.web.resources.cache.cachecontrol.max-age; não configura TTL do cache vets. | Verificar headers de recursos separadamente do frescor de consultas; não transportar 12h para cache de domínio. | src/main/resources/application.properties | D-04; TT-04/06 |
| SIS-EC-10 | Perfil/datasource sobrescrito ou banco indisponível | 🟢 Perfis têm placeholders de ambiente e SQL init always. 🔴 Overrides efetivos, falhas de conexão e recuperação não caracterizados. | Registrar perfil/resolução sem expor segredos; validar em base isolada antes de afirmar conexão ou comportamento de falha. | src/main/resources/application-mysql.properties | SIS-INV-05; TT-06 |
| SIS-EC-11 | Inicialização H2 confundida com migração | 🟢 Schema H2 contém DROP TABLE; seleção SQL vem de database. Não existe plano de migração aprovado. | Inspecionar scripts e isolar ambiente; não executar seeds/DDL sobre dados existentes por criação deste documento. | src/main/resources/db/h2/schema.sql | SIS-RF-06; TM-01 |
| SIS-EC-12 | Recursos ausentes ou hints insuficientes no artefato | 🟢 Hints registram padrões e três tipos para serialização Java. 🔴 Presença de recursos e sucesso nativo não demonstrados. | Inspecionar artefato e testar runtime necessário; não inferir falha por ausência explícita de NamedEntity nos hints. | src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java | SIS-INV-04; TT-05 |
| SIS-EC-13 | Actuator selecionado, mas acesso efetivo desconhecido | 🟢 exposure.include=* é seleção declarada com comentário para dev/teste; não enumera disponibilidade real ou autenticação. | Levantar ambiente e controles de rede/identidade; não chamar todo endpoint nem publicar seleção ampla por suposição. | src/main/resources/application.properties | D-03; SIS-D-01; TT-06 |
| SIS-EC-14 | Probes declaradas em imagem diferente do checkout | 🟢 k8s usa dsyer/petclinic sem tag explícita, configura health e /livez,/readyz. 🔴 Correspondência imagem/checkout/bindings não comprovada. | Validar artefato/ambiente autorizado e conexão real antes de declarar probes 200 ou deploy correto. | k8s/petclinic.yml | SIS-D-04; TT-05/06 |
| SIS-EC-15 | Estatísticas declaradas interpretadas como métricas acessíveis | 🟢 Configuração JCache construída habilita estatísticas. 🔴 Aplicação do customizador, JMX/Actuator e coleta reais não foram observados. | Identificar provedor e canal de métricas; não tratar estatística em bean como audit trail ou monitoramento implantado. | src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java | D-04; TT-04 |
| SIS-EC-16 | Rota demonstrativa usada como health check | 🟢 /oups sempre lança exceção; probes do manifesto são /livez e /readyz, não /oups. | Manter distinção entre demonstração de erro e sinal de saúde operacional; não modificar sem decisão. | src/main/java/org/springframework/samples/petclinic/system/CrashController.java | SIS-RF-02; SIS-D-01; TT-02/06 |

## Cenários prioritários

### Erro demonstrativo — SIS-EC-04/16

🟢 CONFIRMADO — Contrato do método, sem presumir representação de produção.

```gherkin
Dado CrashController instanciado
Quando triggerException é chamado
Então lança RuntimeException com a mensagem demonstrativa e não retorna normalmente
```

🔴 SIS-D-03 — Caracterização de integração deve registrar configuração de include-message e Accept; resultado do teste com ALWAYS não prova exposição em outro ambiente.

### Preferência de idioma — SIS-EC-01/02

🔴 SIS-D-02 — Cenário coleta evidência do fluxo esperado pela configuração.

```gherkin
Dado um ambiente de teste com sessão identificada e idioma inicial conhecido
Quando lang é alterado e uma nova requisição usa a mesma sessão e outra usa sessão nova
Então registrar os locales e textos resolvidos em cada caso sem inferir autenticação a partir da sessão
```

### Cache preexistente — SIS-EC-07/08

🔴 D-04/SIS-INV-03 — Não antecipa erro específico ou retry.

```gherkin
Dado um contexto JCache compatível e cache vets já existente
Quando o customizador de CacheConfiguration é aplicado
Então registrar o resultado da criação e o tratamento efetivo do contexto antes de definir política de recuperação
```

### Perfis e scripts — SIS-EC-10/11

🟡 PROPOSTO — Caracterização só em ambiente isolado escolhido para a futura validação.

```gherkin
Dado um perfil e uma base de teste descartável explicitamente identificados
Quando a resolução de database e dos caminhos SQL é verificada
Então registrar os scripts selecionados e suas operações antes de qualquer execução de inicialização
```

## Vínculos das lacunas

| Lacuna | Casos | Evidência/decisão necessária |
|---|---|---|
| 🔴 D-03 / SIS-D-01 | SIS-EC-13/16 | Acesso efetivo a páginas/Actuator/probes e política de rota demonstrativa |
| 🔴 D-04 | SIS-EC-07/08/09/15 | Provedor, criação, limites, invalidação e métricas efetivas |
| 🔴 SIS-D-02 / SIS-INV-02 | SIS-EC-01/02/03 | Idiomas, fallback e sessão/locale inválido |
| 🔴 SIS-D-03 | SIS-EC-04/06 | Conteúdo de erro por ambiente, sem copiar override de teste |
| 🔴 SIS-D-04 | SIS-EC-14 | Imagem, binding e probes de implantação |
| 🔴 SIS-INV-01/04/05 | SIS-EC-05/10/11/12 | Renderização, artefato e inicialização por perfil |

## Limites da documentação

🟢 CONFIRMADO — Não foi encontrada política própria de retry, SLA, allowlist de idiomas, TTL de vets ou autenticação nas classes examinadas. 🟡 Eventuais melhorias exigem escopo explícito; não preencher lacunas com configuração imaginada.

🟢 CONFIRMADO — Asserções de CrashControllerIntegrationTests usam overrides e exclusões de autoconfiguração; I18nPropertiesSyncTest não valida toda a experiência de idioma. Não encerrar lacunas operacionais apenas por esses testes existirem.
