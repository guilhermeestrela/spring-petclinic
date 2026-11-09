# Sistema — Perguntas para validação

## Como preencher

🟢 CONFIRMADO — Cinco perguntas reúnem as decisões já identificadas nas specs de sistema. Não houve validação de runtime e nenhuma resposta foi presumida.

🟡 ORIENTAÇÃO — Preencha **Resposta** quando tiver informação; se não souber, registre “não sei / precisa investigar”. Distinga regra atual conhecida, documento/configuração de referência e decisão desejada. Não inclua credenciais ou segredos.

🟢 CONFIRMADO — D-03 e D-04 são transversais. Referenciar respostas de responsáveis/veterinários evita políticas conflitantes; não é necessário repetir a mesma decisão. Os próximos arquivos podem ser gerados com perguntas abertas, que serão retomadas na revisão.

## Índice

| Pergunta | Lacuna | Tema | Estado |
|---|---|---|---|
| 1 | D-03 / SIS-D-01 | Acesso a endpoints técnicos e demonstrativos | ✅ Processada |
| 2 | D-04 | Provedor e política do cache — decisão compartilhada | ✅ Processada |
| 3 | SIS-D-02 | Idiomas, preferência de sessão e fallback | ✅ Processada |
| 4 | SIS-D-03 | Conteúdo de erro por ambiente | ✅ Processada |
| 5 | SIS-D-04 | Destino de implantação e imagem de referência | ✅ Processada |

## Pergunta 1 — D-03 / SIS-D-01: Acesso a endpoints técnicos e demonstrativos

**Situação:** ✅ Respondida na pergunta consolidada 3; ver ../validated-decisions.md.

**Contexto:** 🟢 Configuração seleciona todos os endpoints Actuator; /oups sempre lança exceção; não há controle local de usuário nesses controllers. Seleção de exposição não prova acesso público.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/CrashController.java`, `src/main/resources/application.properties`, `k8s/petclinic.yml`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-13/16; T-03/07; TT-02/06; catálogo em [edge-cases.md](edge-cases.md).

**Pergunta:** Quais ambientes precisam manter /oups, Actuator e probes, e quem deve poder acessá-los? Indique controles externos conhecidos e exceções em relação à resposta D-03 das outras units.

**Impacto:** 🔴 Afeta contratos técnicos e integração; não promover configuração demonstrativa a política de produção nem criar papéis presumidos.

**Verificação técnica complementar:** 🟡 Conferir configuração de proxy/rede/identidade no ambiente autorizado e separar seleção de endpoint de sua disponibilidade e autorização.

**Resposta:** Sim, sistema só executado em rede local

**Origem:** usuário, pergunta consolidada 3.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 2 — D-04: Provedor e política do cache — decisão compartilhada

**Situação:** ✅ Respondida na pergunta consolidada 7; ver ../validated-decisions.md.

**Contexto:** 🟢 Há EnableCaching, customizador JCache e consultas vet com Cacheable; Caffeine runtime não comprova provedor ativo. Limites/expiração/invalidação desconhecidos.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `pom.xml`, `build.gradle`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-07/08/09/15; T-05; TT-04; catálogo em [edge-cases.md](edge-cases.md).

**Pergunta:** Há configuração operacional conhecida de CacheManager, limites, expiração e invalidação que precise ser preservada? Referencie a resposta D-04 em veterinarios/questions.md e informe apenas complementos de infraestrutura, se houver.

**Impacto:** 🔴 Afeta SIS-RF-04 e frescor do catálogo; intenção de atualização não comprova TTL/provedor atual. Manter decisão única entre units.

**Verificação técnica complementar:** 🟡 Identificar provedor, aplicação do customizador, cache preexistente e estatísticas no ambiente de validação; não pedir ao usuário que adivinhe runtime.

**Resposta:** Não tem politica de cache

**Origem:** usuário, pergunta consolidada 7.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 3 — SIS-D-02: Idiomas, preferência de sessão e fallback

**Situação:** ✅ Respondida na pergunta consolidada 12; ver ../validated-decisions.md.

**Contexto:** 🟢 Resolver padrão inglês, parâmetro lang e bundles declarados. Teste de sincronismo não prova tradução semântica ou comportamento de locale inválido.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java`, `src/main/resources/application.properties`, `src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-01/02/03; SIS-INV-02; catálogo em [edge-cases.md](edge-cases.md).

**Pergunta:** Quais idiomas devem ser suportados e qual comportamento deve ocorrer para idioma/chave indisponível ou lang inválido? A preferência deve durar somente a sessão atual, como indica o resolver, ou existe contrato adicional a preservar?

**Impacto:** 🔴 Afeta SIS-RF-03 e textos/navegação das demais units. Registrar regra desejada separadamente do fallback efetivo a caracterizar.

**Verificação técnica complementar:** 🟡 TT-03: testar sessão mantida/nova, locales requeridos, valor inválido e chave ausente; inventário de bundles não substitui aprovação de idioma.

**Resposta:** Português e Ingles

**Origem:** usuário, pergunta consolidada 12.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 4 — SIS-D-03: Conteúdo de erro por ambiente

**Situação:** ✅ Respondida na pergunta consolidada 13; ver ../validated-decisions.md.

**Contexto:** 🟢 Template exibe message quando fornecida; teste de integração força include-message ALWAYS e espera detalhe de exceção. Isso não consta como default do arquivo principal.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/system/CrashController.java`, `src/main/resources/templates/error.html`, `src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-04/05/06; SIS-CA-02–04; catálogo em [edge-cases.md](edge-cases.md).

**Pergunta:** Quais informações de erro devem aparecer para usuários em desenvolvimento/teste e no ambiente real? Há contrato conhecido de mensagem, HTML/JSON ou status que precise ser preservado, além das expectativas específicas do teste demonstrativo?

**Impacto:** 🔴 Afeta SIS-RF-02 e contratos de erro; não copiar ALWAYS para implantação como evidência do legado real. Uma política nova deve ter origem na decisão.

**Verificação técnica complementar:** 🟡 TT-02: registrar overrides, Accept, status e conteúdo efetivos nos ambientes necessários, distinguindo exceção de negócio e rota demonstrativa.

**Resposta:** Página generica de erro sem expor nenhuma informação

**Origem:** usuário, pergunta consolidada 13.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 5 — SIS-D-04: Destino de implantação e imagem de referência

**Situação:** ✅ Respondida na pergunta consolidada 14; ver ../validated-decisions.md.

**Contexto:** 🟢 Kubernetes usa dsyer/petclinic sem tag explícita, perfil postgres, bindings e probes; Compose só declara bancos. Não houve execução ou correspondência da imagem ao checkout.

**Origem no legado:** `k8s/petclinic.yml`, `k8s/db.yml`, `docker-compose.yml`, `README.md`.

**Specs afetadas:** [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md), nas seções correspondentes.

**Rastreabilidade:** SIS-EC-10/11/12/14; SIS-RF-05/06; catálogo em [edge-cases.md](edge-cases.md).

**Pergunta:** Qual forma de execução precisa ser preservada: local Maven/Gradle, imagem construída deste checkout, Kubernetes do exemplo ou outra? Se o manifesto for usado, qual imagem/tag/digest e configuração de binding são a referência aprovada?

**Impacto:** 🔴 Afeta bootstrap/empacotamento/perfis e validação de probes. Não declarar que imagem externa equivale ao código analisado nem iniciar deploy por esta resposta.

**Verificação técnica complementar:** 🟡 T-07/TT-05/06: verificar artefato, resolução de configuração e conexão/probes no destino autorizado; registrar o que não foi executado.

**Resposta:** Kubernetes e se possível usar docker para desenvolvimento local

**Origem:** usuário, pergunta consolidada 14.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Investigações técnicas sem pergunta adicional de produto

- 🔴 SIS-INV-01: caracterizar resolução/renderização de welcome/error quando necessário; falha de template não tem fallback local definido.
- 🔴 SIS-INV-03: identificar efeito de cache existente/falha de criação no provedor escolhido; isso exige evidência técnica, não palpite sobre erro.
- 🔴 SIS-INV-04: verificar recursos/hints no artefato e execução nativa se ela for requisito confirmado do destino.
- 🔴 SIS-INV-05: caracterizar inicialização com banco indisponível/schema incompatível em base isolada; não executar scripts sobre dados existentes para completar documento.

## Processamento posterior

🟡 ORIENTAÇÃO — Registrar o efeito de cada resposta nas specs e sua origem; uma decisão de produto/ambiente não transforma resultado técnico não executado em comportamento confirmado. Deixar pendente o que não foi respondido e não executar alterações de infraestrutura por preenchimento deste arquivo.

🟢 CONFIRMADO — Este arquivo não encerra revisão global nem gera relatório final de confiança. Verificações e tarefas permanecem abertas até execução futura no contexto apropriado.

## Revisão das respostas

🟢 As respostas foram consolidadas e processadas. Ver [decisões validadas](../validated-decisions.md) para intenção de destino e pendências residuais; evidências do código acima não foram transformadas em implementação nova.
