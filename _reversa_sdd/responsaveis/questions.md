# Responsáveis — Perguntas para validação

## Como preencher

🟢 CONFIRMADO — Este arquivo é o sexto item do plano do Redator, referente à unit `owner` → `responsaveis`. Reúne seis lacunas de decisão já identificadas nos artefatos desta unit. Nenhuma resposta foi presumida e nenhum teste de aplicação foi executado.

🟡 ORIENTAÇÃO — Preencha o campo **Resposta** de cada pergunta quando tiver a informação. Se não souber, escreva “não sei / precisa investigar”. Distinga **regra atual conhecida**, **evidência do ambiente** e **comportamento desejado**, especialmente se sua resposta propuser mudança no legado. Não inclua senhas, tokens ou credenciais.

🟢 CONFIRMADO — As perguntas de produto não substituem as caracterizações técnicas propostas em [tasks.md](tasks.md). Só evidência de código/execução sustenta uma afirmação de comportamento atual confirmado; uma decisão humana pode definir requisito desejado e deve ter essa origem registrada.

🟡 ORIENTAÇÃO — A geração dos próximos arquivos pode continuar com estas perguntas em aberto. A coleta e a incorporação das respostas serão retomadas na revisão. Este documento não solicita autorização para alterar código ou infraestrutura.

## Índice

| Pergunta | ID de lacuna | Tema | Situação |
|---|---|---|---|
| 1 | D-01 | Obrigatoriedade da data da visita | ✅ Processada |
| 2 | D-02 | Bancos suportados e equivalência de duplicidade | ✅ Processada |
| 3 | D-03 | Proteção de acesso fora do módulo | ✅ Processada |
| 4 | D-05 | Fuso e referência de dia para datas | ✅ Processada |
| 5 | D-06 | Respostas para IDs inexistentes e entradas inválidas | ✅ Processada |
| 6 | RESP-D-07 | Preservação do filtro ao navegar páginas | ✅ Processada |

## Pergunta 1 — D-01: Obrigatoriedade da data da visita

**Situação:** ✅ Respondida na pergunta consolidada 1; ver ../validated-decisions.md.

**Contexto:** 🟢 Visit inicia date em amanhã, não tem @NotNull e o controller só rejeita data até hoje quando não nula. Parâmetro ausente e vazio ainda não foram caracterizados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Visit.java:50`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:98`.

**Specs afetadas:** requirements.md: RESP-RN-10/14 e CA-21–24; design.md: fluxo 6; contracts.md: entrada de visita; tasks.md: T-09/TT-08.

**Rastreabilidade:** EC-16; CA-21–24 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** A visita deve sempre ter uma data futura informada, pode usar amanhã quando a data é omitida, ou pode existir sem data? Especifique o comportamento desejado para campo omitido e campo enviado vazio.

**Impacto:** 🔴 Definição muda validação, valores padrão, mensagens e critérios de aceite de RESP-RF-08. Até a decisão, preservar a lacuna; não declarar null aceito/rejeitado como resultado HTTP comprovado.

**Verificação técnica complementar:** 🟡 TT-08 propõe executar submissões isoladas com descrição válida, registrar objeto após binding, BindingResult e persistência; isso demonstra o comportamento atual, mas não decide a regra de produto.

**Resposta:** A visita deve sempre conter uma data futura

**Origem:** usuário, pergunta consolidada 1.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 2 — D-02: Bancos suportados e equivalência de duplicidade

**Situação:** ✅ Respondida na pergunta consolidada 2; ver ../validated-decisions.md.

**Contexto:** 🟢 Java compara nomes ignorando caixa; H2/PostgreSQL possuem mecanismos explícitos para unicidade correspondente. MySQL declara UNIQUE(owner_id,name) sem o nome textual que o catch procura e sem collation no schema. Não houve execução por perfil.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Owner.java:147`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:202`, `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql`.

**Specs afetadas:** requirements.md: RESP-RN-06/07; design.md: fluxos 4/5 e Estado Interno; contracts.md: erro SQL; tasks.md: T-07/08 e TT-07.

**Rastreabilidade:** EC-09/12/14/15; CA-16–19 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** Quais perfis de banco precisam ser preservados na reimplementação? A regra esperada é impedir nomes que diferem apenas por maiúsculas/minúsculas para o mesmo responsável e mostrar erro de campo também quando o conflito é detectado pelo banco? Informe divergências intencionais, se houver.

**Impacto:** 🔴 Afeta RESP-RF-06/07, constraints, conversão de erros e matriz de testes. Informar bancos usados não comprova collation/mensagem real; esses detalhes continuam pendentes até evidência técnica.

**Verificação técnica complementar:** 🟡 TT-07 propõe validar por perfil a collation, o nome da restrição, a mensagem do driver e concorrência em base isolada. Não pedir ao usuário que adivinhe mensagens de exceção.

**Resposta:** Manter somente postgresql

**Origem:** usuário, pergunta consolidada 2.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 3 — D-03: Proteção de acesso fora do módulo

**Situação:** ✅ Respondida na pergunta consolidada 3; ver ../validated-decisions.md.

**Contexto:** 🟢 Controllers bloqueiam binding de IDs e verificam pertencimento do pet, mas não implementam autenticação/papéis de usuário. Não há evidência local de política de proxy ou rede.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:60`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:90`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:64`.

**Specs afetadas:** requirements.md: RESP-RF-09; design.md: D-03; contracts.md: Binding, acesso e repetição; tarefas de integração T-04.

**Rastreabilidade:** EC-07/08; CA-24/25 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** Existe autenticação ou restrição de acesso aplicada fora da aplicação que faça parte do sistema a preservar? Se sim, descreva quem acessa cadastros/visitas, como o acesso é controlado e onde essa configuração pode ser consultada; se não existe, registre isso.

**Impacto:** 🔴 Afeta o contrato de acesso das operações e dependências de implantação. Não transformar ownerId ou sessão de idioma em prova de autorização, nem introduzir papéis como se já existissem.

**Verificação técnica complementar:** 🟡 Conferir configuração e documentação do ambiente autorizado quando disponíveis. Acesso de rede, autenticação e permissão por responsável são controles distintos e devem ser registrados separadamente.

**Resposta:** Sim, sistema só executado em rede local

**Origem:** usuário, pergunta consolidada 3.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 4 — D-05: Fuso e referência de dia para datas

**Situação:** ✅ Respondida na pergunta consolidada 4; ver ../validated-decisions.md.

**Contexto:** 🟢 Construtor, data mínima e validação usam LocalDate.now em chamadas separadas, sem Clock/ZoneId explícitos nos arquivos analisados.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/Visit.java:50`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:84`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:98`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:108`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:145`.

**Specs afetadas:** requirements.md: RESP-RN-05/10; design.md: D-05; contracts.md: dados temporais; tasks.md: T-07/08/09.

**Rastreabilidade:** EC-13/17; CA-20–23 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** Qual fuso deve definir hoje e amanhã para nascimento e visita: o da clínica, o do servidor ou outro? Informe o identificador de fuso, se conhecido, e se a validação deve usar uma única referência de dia durante a requisição.

**Impacto:** 🔴 Afeta limites de data de RESP-RF-06/07/08, valor padrão amanhã e testes na virada do dia. Definição desejada deve ser distinguida da configuração efetiva atual da JVM.

**Verificação técnica complementar:** 🟡 Registrar fuso efetivo do ambiente e caracterizar fronteiras temporais conforme TT-03/05/08 antes de afirmar equivalência. Não substituir datas em código nesta extração.

**Resposta:** São Paulo - Brasil

**Origem:** usuário, pergunta consolidada 4.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 5 — D-06: Respostas para IDs inexistentes e entradas inválidas

**Situação:** ✅ Respondida na pergunta consolidada 5; ver ../validated-decisions.md.

**Contexto:** 🟢 Ausência de Owner e pet de outra coleção na visita lançam IllegalArgumentException; findPet da edição pode retornar null. Controllers não fixam status HTTP de negócio para esses ramos; conversões não foram caracterizadas.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:65`, `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:179`, `src/main/java/org/springframework/samples/petclinic/owner/PetController.java:76`, `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java:64`.

**Specs afetadas:** requirements.md: D-06; design.md: Fluxos Alternativos; contracts.md: Erros e limites; tasks.md: TT-08.

**Rastreabilidade:** EC-03/08/12/13; CA-08/24 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** Há um comportamento de erro que precise ser mantido para responsável/animal inexistente ou não pertencente ao responsável e para parâmetros inválidos? Descreva página/mensagem/redirect/status esperado, se houver contrato conhecido; se não houver, registre que precisa ser caracterizado antes de decidir.

**Impacto:** 🔴 Afeta RESP-RF-02/03/07/08/09 e a descrição HTTP das falhas. Uma preferência por 404 ou outro status é requisito desejado, não prova do retorno atual do legado.

**Verificação técnica complementar:** 🟡 TT-08 propõe caracterizar separadamente GET/POST de edição e visita, parâmetros não numéricos/fora de Integer e conversão de tipo/data; registrar status, body/Location e persistência sem normalizar resultados por suposição.

**Resposta:** não tem contrato conhecido

**Origem:** usuário, pergunta consolidada 5.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Pergunta 6 — RESP-D-07: Preservação do filtro ao navegar páginas

**Situação:** ✅ Respondida na pergunta consolidada 6; ver ../validated-decisions.md.

**Contexto:** 🟢 Redirect de página fora de faixa mantém lastName normalizado quando não vazio. Links de paginação em ownersList.html montam somente /owners?page=..., sem lastName.

**Origem no legado:** `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java:95`, `src/main/resources/templates/owners/ownersList.html`.

**Specs afetadas:** requirements.md: RESP-RF-02; design.md: fluxo 3; contracts.md: HTTP-04; tasks.md: T-06/10 e TT-02/08.

**Rastreabilidade:** EC-04; CA-03/06 em [edge-cases.md](edge-cases.md) e [requirements.md](requirements.md), conforme o tipo de ID.

**Pergunta:** Ao buscar por sobrenome e mudar de página, o filtro deve permanecer ativo? A omissão de lastName nos links é um comportamento intencional a preservar ou uma divergência a registrar para uma correção futura?

**Impacto:** 🔴 Afeta RESP-RF-02, links e critérios de navegação. Preservar filtro em uma futura versão pode ser uma mudança funcional; esta documentação não altera os links existentes.

**Verificação técnica complementar:** 🟡 TT-02/08 propõe navegar uma busca com várias páginas e comparar query da URL, filtro da consulta e resultados. A observação comprova o efeito, não a intenção de negócio.

**Resposta:** Não

**Origem:** usuário, pergunta consolidada 6.

**Origem da resposta / evidência (opcional):** <!-- regra atual, documento, ambiente ou decisão desejada; sem segredos -->

## Investigações que não exigem decisão imediata do usuário

- 🟡 **RESP-D-08 / EC-21:** medir custo de carregamento EAGER e ordem da navegação antes de propor otimização ou SLA; sem métrica atual não inventar limites de animais/visitas.
- 🟡 **EC-20:** caracterizar script de feedback com message/error ausentes e apresentação de flash sem redirect. Uma suspeita de falha visual não é correção aprovada nem pergunta obrigatória de produto neste estágio.
- 🔴 **EC-12/19:** caracterizar tipo vazio na edição e efeito de alterações em memória, sem confundir ausência de chamada save com prova de comportamento sob qualquer configuração transacional externa.

## Processamento posterior das respostas

🟡 ORIENTAÇÃO — Na revisão, associar cada resposta ao ID de lacuna, atualizar as specs afetadas com a origem da decisão e manter explícita qualquer execução ainda pendente. Uma resposta sobre intenção não transforma um resultado de driver/binding não medido em evidência confirmada.

🟡 ORIENTAÇÃO — Marcar a pergunta como respondida apenas depois de ler sua resposta e registrar seu efeito; campos vazios permanecem pendentes. A extração atual preserva os artefatos existentes e não executa as tarefas de implementação por preencher este arquivo.

## Revisão das respostas

🟢 As respostas foram consolidadas e processadas. Ver [decisões validadas](../validated-decisions.md) para intenção de destino e pendências residuais; evidências do código acima não foram transformadas em implementação nova.
