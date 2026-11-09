# Histórias de usuário — Operação da clínica

## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](../validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


## Escopo e confiança

🟡 INFERIDO — As personas e o formato “Como / quero / para” são síntese dos fluxos existentes, não resultado de entrevista com usuários. “Pessoa operando a clínica” não é papel autenticado implementado. “Consumidor de dados” não prova integração externa já existente.

🟢 CONFIRMADO — Comportamentos e ramos abaixo são vinculados às specs extraídas do código. Critérios não foram executados. Não há histórias de cobrança, bloqueio de agenda, vínculo visita-veterinário, exclusão ou cancelamento implementados nos handlers examinados; sua ausência não define política definitiva de produto.

🟡 INFERIDO — Decomposição em histórias é organização funcional proposta; tarefas referidas são planos de reimplementação ainda abertos.

## Índice

| História | Fluxo | Unit | Requisitos |
|---|---|---|---|
| US-01 | Cadastrar responsável | responsaveis | RESP-RF-01; CA-01/02 |
| US-02 | Encontrar responsável | responsaveis | RESP-RF-02; CA-03–06 |
| US-03 | Consultar e atualizar responsável | responsaveis | RESP-RF-03/04; CA-07–11 |
| US-04 | Cadastrar animal | responsaveis | RESP-RF-05/06; CA-12–17 |
| US-05 | Editar animal | responsaveis | RESP-RF-07; CA-18–20 |
| US-06 | Registrar visita futura | responsaveis | RESP-RF-08/09; CA-21–25 |
| US-07 | Consultar veterinários no navegador | veterinarios | VET-RF-01/02/04; VET-CA-01–05/09/10 |
| US-08 | Consumir catálogo em dados | veterinarios | VET-RF-03; VET-CA-06–08 |
| US-09 | Usar idioma na interface | sistema | SIS-RF-03; SIS-CA-05/06 |

## US-01 — Cadastrar responsável

🟡 **História:** Como pessoa operando a clínica, quero cadastrar dados do responsável para associar seus animais ao cadastro.

🟢 **Origem:** OwnerController.processCreationForm; Owner; model/Person. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-01; CA-01/02. 🟡 Tarefas propostas: T-05/10; TT-01, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado um novo responsável com campos válidos
Quando o cadastro é submetido sem erros
Então o responsável é salvo e o handler redireciona ao detalhe com mensagem de criação

Dado um novo responsável com cidade de 81 caracteres
Quando o cadastro é submetido
Então o formulário retorna com erro de cidade sem chamada save no ramo inválido
```

## US-02 — Encontrar responsável

🟡 **História:** Como pessoa operando a clínica, quero buscar por início do sobrenome para localizar o cadastro que preciso consultar.

🟢 **Origem:** OwnerController.processFindForm; OwnerRepository. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-02; CA-03–06. 🟡 Tarefas propostas: T-06; TT-02, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado mais de um resultado para o prefixo Silva
Quando a página 1 é buscada com espaços nas bordas do prefixo
Então a consulta usa Silva, tamanho 5, e a view recebe os dados da lista/paginação

Dado nenhum resultado para o prefixo na página 1
Quando a busca é realizada
Então retorna owners/findOwners com lastName/notFound
```

## US-03 — Consultar e atualizar responsável

🟡 **História:** Como pessoa operando a clínica, quero consultar dados, animais e visitas do responsável e atualizar seus dados pessoais para manter o cadastro utilizável.

🟢 **Origem:** OwnerController.showOwner/processUpdateOwnerForm; ownerDetails.html. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-03/04; CA-07–11. 🟡 Tarefas propostas: T-05/06/10; TT-01/02, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado um responsável existente com ID coerente e dados de edição válidos
Quando a edição é submetida
Então save usa esse ID e retorna redirect ao detalhe com mensagem de atualização

Dado uma edição com endereço ou telefone inválido
Quando a edição é submetida
Então retorna formulário com erros sem salvar
```

## US-04 — Cadastrar animal

🟡 **História:** Como pessoa operando a clínica, quero cadastrar animal com nome, nascimento e tipo para vinculá-lo ao responsável correto.

🟢 **Origem:** PetController.processCreationForm; PetValidator; PetTypeFormatter. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-05/06; CA-12–17. 🟡 Tarefas propostas: T-03/07/10; TT-03/04/07, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado um responsável existente, tipo conhecido e animal com dados válidos sem nome conflitante
Quando o novo animal é submetido
Então é associado e persistido via saveAndFlush de Owner com redirect ao detalhe

Dado um responsável com animal persistido Luna
Quando outro animal chamado luna é submetido para ele
Então name recebe duplicate e retorna formulário antes de saveAndFlush
```

## US-05 — Editar animal

🟡 **História:** Como pessoa operando a clínica, quero atualizar nome, nascimento e tipo do animal para corrigir seus dados sem criar outro cadastro.

🟢 **Origem:** PetController.processUpdateForm/updatePetDetails. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-07; CA-18–20. 🟡 Tarefas propostas: T-08; TT-03/04/08, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado um animal carregado do responsável com dados válidos e nome próprio mantido
Quando a edição é submetida
Então os dados do animal existente são atualizados e o responsável é salvo com flush

Dado outro animal do mesmo responsável com nome já usado
Quando a edição passa a usar esse nome de outro ID
Então name recebe duplicate e o formulário retorna sem salvar
```

## US-06 — Registrar visita futura

🟡 **História:** Como pessoa operando a clínica, quero registrar uma visita para um animal do responsável para manter data e descrição do atendimento agendado.

🟢 **Origem:** VisitController.loadPetWithVisit/processNewVisitForm; Visit. Fontes completas e critérios detalhados em [requisitos da unit](../responsaveis/requirements.md) e [design](../responsaveis/design.md).

🟢 **Rastreabilidade:** RESP-RF-08/09; CA-21–25. 🟡 Tarefas propostas: T-09/10; TT-05/08, em [tasks.md](../responsaveis/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado um animal pertencente ao responsável e uma visita com amanhã e descrição válida
Quando a visita é submetida sem erros
Então o responsável é salvo e o handler redireciona ao detalhe com mensagem de agendamento

Dado um animal válido e descrição válida
Quando a data de visita enviada é hoje
Então date recebe typeMismatch.visitDate e o formulário retorna sem salvar
```

## US-07 — Consultar veterinários no navegador

🟡 **História:** Como pessoa consultando o catálogo da clínica, quero navegar pelos veterinários e suas especialidades para conhecer os profissionais cadastrados.

🟢 **Origem:** VetController.showVetList; Vet.getSpecialties; vetList.html. Fontes completas e critérios detalhados em [requisitos da unit](../veterinarios/requirements.md) e [design](../veterinarios/design.md).

🟢 **Rastreabilidade:** VET-RF-01/02/04; VET-CA-01–05/09/10. 🟡 Tarefas propostas: T-04/06; TT-01/03/06, em [tasks.md](../veterinarios/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado uma página válida de veterinários com especialidades não nulas
Quando GET /vets.html solicita page 1
Então retorna vets/vetList com até 5 veterinários e especialidades ordenadas por nome

Dado uma consulta concluída normalmente com página externa 0
Quando a faixa é avaliada pelo handler
Então o retorno é redirect de /vets.html com page 1
```

## US-08 — Consumir catálogo em dados

🟡 **História:** Como consumidor de dados do catálogo, quero consultar o envelope de veterinários para obter a lista disponibilizada pela aplicação.

🟢 **Origem:** VetController.showResourcesVetList; Vets. Fontes completas e critérios detalhados em [requisitos da unit](../veterinarios/requirements.md) e [design](../veterinarios/design.md).

🟢 **Rastreabilidade:** VET-RF-03; VET-CA-06–08. 🟡 Tarefas propostas: T-05; TT-02/05, em [tasks.md](../veterinarios/tasks.md).

🟢 Os critérios seguintes representam ramos documentados do código, sem execução nesta extração.

```gherkin
Dado findAll sem Pageable retornando coleção válida
Quando GET /vets é processado pelo handler
Então todos os itens entram em Vets.getVetList e o envelope é retornado como body

Dado findAll sem Pageable lançando DataAccessException
Quando o handler é chamado
Então a falha não é convertida localmente em envelope vazio
```

## US-09 — Usar idioma na interface

🟡 **História:** Como pessoa usando a interface, quero selecionar idioma para visualizar os textos de acordo com a preferência disponível na aplicação.

🟢 **Origem:** WebConfiguration; application.properties. Fontes completas e critérios detalhados em [requisitos da unit](../sistema/requirements.md) e [design](../sistema/design.md).

🟢 **Rastreabilidade:** SIS-RF-03; SIS-CA-05/06. 🟡 Tarefas propostas: T-04; TT-03, em [tasks.md](../sistema/tasks.md).

🔴 O segundo cenário é investigação pendente, não resultado de falha definido. A experiência completa de sessão/fallback ainda precisa ser caracterizada.

```gherkin
Dado o resolver e interceptor produzidos pelos beans de WebConfiguration
Quando sua configuração é inspecionada
Então o padrão é inglês e o parâmetro de mudança registrado é lang

Dado uma requisição com lang inválido em ambiente de caracterização
Quando a resolução de idioma é executada
Então registrar o resultado real antes de definir o contrato de falha (SIS-D-02)
```

## Restrições e decisões que atravessam as histórias

| Tema | Impacto na história | Situação |
|---|---|---|
| Data de visita vazia/omitida | US-06 | 🔴 D-01: padrão amanhã e falta de NotNull não resolvem obrigatoriedade externa |
| Duplicidade por banco | US-04/05 | 🔴 D-02: collation/mensagem e concorrência exigem evidência por perfil |
| Autorização | Todas as operações | 🔴 D-03: identidade/papel não implementados localmente; proteção externa desconhecida |
| Cache/frescor | US-07/08 | 🔴 D-04: provedor, expiração e invalidação não caracterizados |
| Fuso de negócio | US-04/05/06 | 🔴 D-05: LocalDate.now padrão da JVM, sem política explícita |
| IDs/erros inválidos | US-02/03/05/06/07/08 | 🔴 D-06/VET-D-02: não inventar status HTTP |
| Ordem e formatos do catálogo | US-07/08 | 🔴 VET-D-01/03: shape completo/XML e ordem de veterinários pendentes |
| Idioma e fallback | US-09 e views demais | 🔴 SIS-D-02: experiência integral ainda não executada |
| Paginação com filtro | US-02 | 🔴 RESP-D-07: links omitem lastName embora redirect inválido preserve filtro |

🟢 CONFIRMADO — [Perguntas de responsáveis](../responsaveis/questions.md), [veterinários](../veterinarios/questions.md) e [sistema](../sistema/questions.md) reúnem decisões pendentes; respostas não foram presumidas.

## Habilitadores técnicos relacionados

- 🟢 [Modelo compartilhado](../modelo/requirements.md): identidade, nomes e validação sustentam as entidades; não é uma persona ou um fluxo HTTP independente.
- 🟢 [Sistema](../sistema/requirements.md): bootstrap, hints, configuração por perfil e cache são dependências dos fluxos; operação técnica não cria novos casos de negócio por inferência.
- 🟢 GET / retorna welcome e /oups é demonstração de exceção; constam nos contratos de sistema sem serem transformados em histórias de atendimento clínico.
- 🟡 Mudanças de comportamento propostas por respostas futuras devem ser distinguidas da fidelidade ao legado; este arquivo não autoriza implementação ou implantação.

## Limites da validação

🟢 CONFIRMADO — Links e IDs foram verificados documentalmente. Nenhuma história foi validada por usuário final ou executada de ponta a ponta nesta etapa. O artefato não substitui as regras/contratos completos das units nem conclui revisão de confiança.
