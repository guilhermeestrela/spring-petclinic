# Responsáveis, animais e visitas — Requisitos

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-01:** Visita sempre deve conter data futura. Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário.
- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-05:** São Paulo, Brasil. Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso.
- 🟢 **D-06:** Não há contrato de erro conhecido. Confirmada ausência de contrato conhecido pelo usuário. Não impor 404/409 ou body específico como regra existente; definir contrato na evolução se necessário.
- 🟢 **RESP-D-07:** Não preservar filtro ao trocar páginas. Resposta Não interpretada para a primeira pergunta direta: filtro não precisa permanecer ativo. Comportamento atual dos links pode ser preservado, sem correção automática.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Visão Geral

🟢 CONFIRMADO — Esta unit corresponde ao pacote Java `owner` e permite cadastrar, buscar, consultar e editar responsáveis e seus animais, além de registrar visitas futuras com data não nula. Owner, Pet, PetType e Visit são as entidades envolvidas. Fontes: S01–S11 na rastreabilidade abaixo.

🟡 INFERIDO — A pessoa operando a clínica é a persona principal. Não existe papel autenticado de recepcionista, veterinário ou administrador implementado neste módulo. A prioridade MoSCoW é uma proposta derivada dos caminhos de código e testes existentes, não de métricas de uso ou aprovação de produto.

🟢 CONFIRMADO — Esta especificação registra o comportamento observado estaticamente; os cenários não foram executados. As lacunas ficam explícitas, sem substituir o comportamento existente por melhorias presumidas.

## Responsabilidades

- 🟢 CONFIRMADO — Criar e editar os dados de responsáveis, com validação de campos e navegação ao detalhe (S01–S03).
- 🟢 CONFIRMADO — Buscar por prefixo do sobrenome e oferecer paginação, tratamento de ausência e acesso ao detalhe (S03, S04).
- 🟢 CONFIRMADO — Associar animais ao responsável, converter tipos do catálogo e validar seus dados na criação/edição (S01, S05–S09).
- 🟢 CONFIRMADO — Criar visitas vinculadas a um animal pertencente ao responsável e apresentar animais/visitas no detalhe (S10–S12).

## Regras de Negócio

| ID local | Regra observada | Evidência / regra global |
|---|---|---|
| RESP-RN-01 | 🟢 Nome e sobrenome não vazios, máximo 30 caracteres cada; endereço não vazio até 255; cidade não vazia até 80; telefone com exatamente 10 dígitos. Não há normalização geral desses campos no código lido. | S01, S02; RN-01/02 |
| RESP-RN-02 | 🟢 Busca transforma sobrenome null em vazio e aplica strip quando preenchido. Usa prefixo; vazio permite busca ampla. Página externa inicia em 1, com tamanho 5 e sem ordenação explícita no PageRequest. | S03:95/141, S04; RN-03 |
| RESP-RN-03 | 🟢 Página inválida redireciona à primeira preservando sobrenome normalizado não vazio. Em página válida, zero resultados retorna busca com lastName/notFound; total de um resultado redireciona detalhe; múltiplos retornam lista. | S03:95; RN-04 |
| RESP-RN-04 | 🟢 Criação/edição de responsável com erro retorna formulário sem chamar save. Edição compara ID do objeto carregado com ownerId da URL; divergência rejeita id/mismatch e redireciona edição. | S03:78/154 |
| RESP-RN-05 | 🟢 Nome de animal deve conter texto e ter no máximo 30 caracteres; nascimento deve ser não nulo e não futuro. Tipo é exigido pelo PetValidator somente quando isNew é verdadeiro. Datas de formulário usam yyyy-MM-dd. | S05–S07; RN-05/06 |
| RESP-RN-06 | 🟢 Nome de animal é comparado com equalsIgnoreCase dentro do mesmo responsável. Na criação, a busca de duplicidade ignora animais novos. Na edição, é permitido manter nome do mesmo ID; nome de outro ID é rejeitado com name/duplicate. Não há strip de nome de animal. | S01:147, S06:108/145; RN-07 |
| RESP-RN-07 | 🟢 Criação/edição de animal persistem via saveAndFlush do responsável. Violação de integridade só vira erro name/duplicate quando a mensagem contém unique_owner_pet_name, ignorando caixa; outras violações são relançadas. | S06:108/145/202; RN-08 |
| RESP-RN-08 | 🟢 addPet ignora null, objeto já contido e outro objeto persistido com mesmo ID. Dois objetos novos distintos podem entrar na coleção. Essa regra não substitui a unicidade de nomes. | S01:100; RN-09 |
| RESP-RN-09 | 🟢 Tipos são consultados em ordem de nome; conversão de texto compara nome exato, sensível à caixa. Tipo não localizado causa ParseException. | S08, S09 |
| RESP-RN-10 | 🟢 Visit inicia a data em amanhã. Uma data não nula deve ser posterior a hoje; descrição deve conter texto e ter no máximo 255 caracteres. minVisitDate publica amanhã ao formulário. | S10:50, S11:84/98; RN-10 |
| RESP-RN-11 | 🟢 A criação de visita carrega o responsável e busca o animal dentro dele. Responsável inexistente ou animal fora dessa coleção causa IllegalArgumentException antes de salvar. | S11:64; RN-11 |
| RESP-RN-12 | 🟢 A mesma instância de Visit é anexada no carregamento do model e na submissão; o Set evita repetição da instância. Não há regra local de exclusividade por data, horário ou descrição. | S05, S11:64/98 |
| RESP-RN-13 | 🟢 Os binders bloqueiam id e *.id; as identidades usadas nas edições vêm do carregamento pela rota. Isso não implementa autenticação ou autorização por usuário. | S03:60, S06:90/95, S11:52; RN-15 |
| RESP-RN-14 | 🔴 A obrigatoriedade de data de visita vazia não está definida: falta @NotNull, e a validação manual ignora null. Não afirmar rejeição de null como regra confirmada. | S10, S11; D-01 |

## Requisitos Funcionais

🟡 INFERIDO — A coluna de prioridade segue a justificativa MoSCoW abaixo. 🟢 Os comportamentos das linhas são confirmados nas fontes citadas; a referência CA aponta para cenários especificados, não resultados de execução.

| ID | Requisito | Prioridade | Critério de aceite | Origem |
|---|---|---|---|---|
| RESP-RF-01 | 🟢 Abrir e submeter cadastro de responsável. | Must | CA-01 e CA-02 | S03:73/78 |
| RESP-RF-02 | 🟢 Buscar responsáveis por prefixo com normalização e paginação. | Must | CA-03 a CA-06 | S03:90/95, S04 |
| RESP-RF-03 | 🟢 Exibir detalhe com dados pessoais, animais e visitas. | Must | CA-07 e CA-08 | S03:179, S12 |
| RESP-RF-04 | 🟢 Abrir e submeter edição do responsável sem trocar sua identidade. | Must | CA-09 a CA-11 | S03:149/154 |
| RESP-RF-05 | 🟢 Consultar catálogo de tipos e converter o nome selecionado. | Must | CA-12 e CA-13 | S06:63, S08, S09 |
| RESP-RF-06 | 🟢 Criar animal para responsável existente com validação e controle de duplicidade. | Must | CA-14 a CA-17 | S06:101/108, S07 |
| RESP-RF-07 | 🟢 Editar nome, nascimento e tipo de animal carregado pelo responsável. | Must | CA-18 a CA-20 | S06:76/140/145/186 |
| RESP-RF-08 | 🟢 Abrir e submeter nova visita vinculada ao animal do responsável. | Must | CA-21 a CA-24 | S10, S11 |
| RESP-RF-09 | 🟢 Preservar integridade de identidade e associações ao receber formulário. | Must | CA-11, CA-24, CA-25 | S01, S03, S06, S11 |

## Requisitos Não Funcionais

| Tipo | Comportamento extraído | Evidência | Confiança |
|---|---|---|---|
| Limite de resultado | Consulta de responsáveis limita cada página a 5 registros; não há meta de latência comprovada. | S03:141 | 🟢 CONFIRMADO |
| Integridade no binding | Impedir atribuição de id e *.id pelos campos de formulário nos binders declarados. | S03:60, S06:90/95, S11:52 | 🟢 CONFIRMADO |
| Integridade persistente | Complementar a validação de nomes com restrição única por responsável; detalhes variam por perfil SQL. | S06:126/199; S13 | 🟢 CONFIRMADO quanto às declarações; 🔴 equivalência MySQL pendente |

🟢 CONFIRMADO — Não foram identificados SLA, timeout próprio, retry de negócio, limite de volume por responsável ou política de acesso por papel nas fontes desta unit. Nenhum desses requisitos é introduzido nesta extração.

## Critérios de Aceitação

🟢 CONFIRMADO — Os cenários CA abaixo derivam dos ramos do código citado em cada grupo. São critérios de fidelidade ao legado, ainda sem execução. Nos casos de exceção, nenhum status HTTP específico é presumido.

### Cadastro de responsável — S01–S03

```gherkin
Cenário CA-01: Cadastrar responsável válido
Dado um novo responsável com nome e sobrenome não vazios de até 30 caracteres, endereço até 255, cidade até 80 e telefone de 10 dígitos
Quando o cadastro é submetido sem erros de binding ou validação
Então o responsável é salvo e o retorno redireciona ao seu detalhe com a mensagem de criação

Cenário CA-02: Rejeitar cadastro inválido
Dado um novo responsável com cidade de 81 caracteres e os demais campos válidos
Quando o cadastro é submetido
Então o formulário de responsável é retornado com erros e save não é chamado
```

### Busca e paginação — S03, S04

```gherkin
Cenário CA-03: Listar por prefixo normalizado
Dado mais de um responsável cujo sobrenome começa com Silva
Quando a página 1 é solicitada com lastName igual a espaços seguidos de Silva e espaços
Então a consulta usa o prefixo Silva e a lista contém até 5 resultados com os dados de paginação

Cenário CA-04: Buscar sem correspondência
Dado que nenhum responsável corresponde ao prefixo informado
Quando a busca é feita na página 1
Então o formulário de busca retorna com erro notFound no campo lastName

Cenário CA-05: Redirecionar resultado único
Dado que o total da busca é exatamente um responsável
Quando a página 1 é solicitada
Então o retorno redireciona ao detalhe desse responsável

Cenário CA-06: Corrigir página fora de faixa
Dado um filtro Silva e um total de duas páginas de resultados
Quando a página 3 é solicitada e a consulta retorna
Então o retorno redireciona à busca com page igual a 1 e lastName igual a Silva
```

### Detalhe — S03, S12

```gherkin
Cenário CA-07: Consultar responsável existente
Dado um responsável persistido com seus animais e visitas
Quando seu detalhe é solicitado pelo ID
Então a view owners/ownerDetails recebe o responsável e apresenta dados pessoais, animais e visitas

Cenário CA-08: Consultar responsável inexistente
Dado um ownerId sem correspondência no repositório
Quando o carregamento do responsável é realizado para o detalhe
Então uma IllegalArgumentException é lançada
```

### Edição de responsável — S03

```gherkin
Cenário CA-09: Salvar edição válida
Dado um responsável carregado com ID igual ao ownerId da URL
Quando a edição válida é submetida
Então ele é salvo com esse ID e o retorno redireciona ao detalhe com mensagem de atualização

Cenário CA-10: Rejeitar edição inválida
Dado um responsável existente com telefone inválido no formulário
Quando a edição é submetida
Então o formulário é retornado com erro de validação sem salvar

Cenário CA-11: Detectar divergência interna de identidade
Dado um objeto Owner com ID diferente do ownerId recebido pelo método de edição e sem outros erros
Quando processUpdateOwnerForm é invocado
Então o erro id/mismatch é registrado e o retorno redireciona à edição sem salvar
```

🟢 CONFIRMADO — CA-11 exercita o ramo do método; não afirma que um cliente HTTP consegue substituir o ID bloqueado pelo binder.

### Catálogo de tipos — S08, S09

```gherkin
Cenário CA-12: Converter tipo conhecido
Dado um tipo persistido de nome cat
Quando o formatter recebe exatamente cat
Então retorna o PetType correspondente

Cenário CA-13: Falhar para tipo desconhecido
Dado que nenhum tipo do catálogo tem nome exatamente CAT
Quando o formatter recebe CAT
Então lança ParseException
```

### Criação de animal — S01, S05–S07

```gherkin
Cenário CA-14: Criar animal válido
Dado um responsável existente e um tipo conhecido
Quando é submetido um novo animal com nome textual até 30 caracteres, sem duplicidade e nascimento até hoje
Então o animal é associado, o responsável é salvo com flush e o retorno redireciona ao detalhe com mensagem de sucesso

Cenário CA-15: Rejeitar dados inválidos do novo animal
Dado um novo animal com nome em branco, tipo null e nascimento null
Quando PetValidator valida o objeto
Então os campos name, type e birthDate recebem erro required

Cenário CA-16: Rejeitar nome de animal já persistido
Dado um responsável com um animal persistido chamado Luna
Quando um novo animal válido chamado luna é submetido para esse responsável
Então name recebe erro duplicate e o formulário retorna sem saveAndFlush

Cenário CA-17: Tratar duplicidade detectada no flush
Dado um formulário de animal aceito pela validação inicial
Quando saveAndFlush lança DataIntegrityViolationException cuja mensagem contém unique_owner_pet_name
Então name recebe erro duplicate e o formulário é retornado
```

### Edição de animal — S06, S07

```gherkin
Cenário CA-18: Manter nome do mesmo animal
Dado um animal persistido carregado dentro do responsável e nenhum outro animal conflitante
Quando a edição mantém seu nome e fornece nascimento válido e tipo conhecido
Então os dados do animal existente são atualizados, saveAndFlush é chamado e o retorno redireciona ao detalhe

Cenário CA-19: Rejeitar nome de outro animal
Dado dois animais do mesmo responsável com IDs distintos
Quando a edição de um usa o nome do outro
Então name recebe erro duplicate e o formulário retorna sem salvar

Cenário CA-20: Rejeitar nascimento futuro
Dado um animal carregado para edição
Quando sua edição informa nascimento posterior a hoje
Então birthDate recebe erro typeMismatch.birthDate e o formulário é retornado sem salvar
```

### Visitas — S10, S11

```gherkin
Cenário CA-21: Abrir formulário de visita
Dado um responsável existente e um animal pertencente a ele
Quando o formulário de nova visita é carregado
Então a visita é criada em memória com data de amanhã e o model inclui owner, pet e minVisitDate

Cenário CA-22: Registrar visita válida
Dado um responsável e seu animal e uma visita com data de amanhã e descrição textual até 255 caracteres
Quando a visita é submetida sem erros
Então o responsável é salvo e o retorno redireciona ao detalhe com mensagem de agendamento

Cenário CA-23: Rejeitar data de hoje
Dado um animal válido e descrição válida
Quando uma visita com data de hoje é submetida
Então date recebe erro typeMismatch.visitDate e o formulário retorna sem salvar

Cenário CA-24: Rejeitar animal fora do responsável
Dado um responsável existente cuja coleção não contém o petId da URL
Quando o carregamento da nova visita é realizado
Então uma IllegalArgumentException é lançada antes de salvar
```

### Binding — S03, S06, S11

```gherkin
Cenário CA-25: Bloquear identidade enviada no formulário
Dado um WebDataBinder configurado pelos binders do módulo
Quando os campos id e pets[0].id são submetidos ao binding
Então esses campos são suprimidos conforme os padrões id e *.id e não substituem os identificadores carregados
```

## Prioridade (MoSCoW)

| Conjunto | Prioridade proposta | Justificativa |
|---|---|---|
| RESP-RF-01/04 | 🟡 Must | Cadastro e edição sustentam o agregado Owner; há cenários dedicados em OwnerControllerTests. |
| RESP-RF-02/03 | 🟡 Must | Busca e detalhe compõem navegação para animais e visitas, com testes dedicados. |
| RESP-RF-05/06/07 | 🟡 Must | Catálogo/conversão e validação estão no caminho de criação/edição de animal. |
| RESP-RF-08 | 🟡 Must | Registro de visita é o fluxo implementado de agendamento, com VisitControllerTests. |
| RESP-RF-09 | 🟡 Must | As validações de identidade e pertencimento são chamadas pelos fluxos principais. |

🟢 CONFIRMADO — Não foram localizados fluxos comentados/depreciados nesta unit que justifiquem Won’t. Não há base para inventar prioridades Should/Could apenas para preencher a matriz.

## Riscos e Lacunas

- 🔴 D-01 — Validar submissão de visita com data vazia e definir se a data deve ser obrigatória. Ausência do parâmetro e envio vazio podem ter efeitos distintos sobre o padrão amanhã; nenhum resultado HTTP foi comprovado aqui.
- 🔴 D-02 — Confirmar collation, nome e mensagem da constraint de duplicidade no MySQL; a regra de erro depende do texto unique_owner_pet_name. H2 e PostgreSQL têm mecanismos explícitos para comparação sem caixa.
- 🔴 D-03 — Confirmar políticas externas de autenticação e acesso. Acesso por ownerId não comprova autorização do usuário sobre esse responsável.
- 🔴 D-05 — Confirmar fuso de negócio para hoje/amanhã; o código usa LocalDate.now sem Clock/ZoneId explícitos.
- 🔴 D-06 — Definir ou comprovar tratamento HTTP para IDs inexistentes. PetController pode retornar null ao procurar pet; VisitController rejeita explicitamente ausência. Não presumir resposta 404.
- 🟢 CONFIRMADO — Controllers desta unit não expõem exclusão, alteração/cancelamento de visita, cobrança, atribuição de veterinário ou bloqueio de agenda. Essa ausência não equivale a proibição de negócio definitiva.

## Rastreabilidade de Código

🟢 CONFIRMADO — Caminhos abaixo são relativos à raiz do projeto. Referências Snn:linha indicam a linha inicial do símbolo relevante no checkout analisado.

| Ref. | Arquivo | Símbolo / cobertura |
|---|---|---|
| S01 | src/main/java/org/springframework/samples/petclinic/owner/Owner.java | campos e validações:52; addPet:100; getPet:129/147; addVisit:176 |
| S02 | src/main/java/org/springframework/samples/petclinic/model/Person.java | firstName:31; lastName:36 |
| S03 | src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java | binder:60; findOwner:65; cadastro:73/78; busca:90/95; paginação:141; edição:149/154; detalhe:179 |
| S04 | src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java | findByLastNameStartingWith e findById |
| S05 | src/main/java/org/springframework/samples/petclinic/owner/Pet.java | nascimento/tipo/visitas, ordenação e addVisit |
| S06 | src/main/java/org/springframework/samples/petclinic/owner/PetController.java | tipos:63; carregamento:68/76; binders:90/95; criação:101/108; edição:140/145/186; integridade:202 |
| S07 | src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java | validate:39; supports:65 |
| S08 | src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java | print e parse |
| S09 | src/main/java/org/springframework/samples/petclinic/owner/PetTypeRepository.java | findPetTypes ordenado por nome |
| S10 | src/main/java/org/springframework/samples/petclinic/owner/Visit.java | campos:39; construtor:50 |
| S11 | src/main/java/org/springframework/samples/petclinic/owner/VisitController.java | binder:52; carregamento:64; data mínima:84; formulário:91; submissão:98 |
| S12 | src/main/resources/templates/owners/ownerDetails.html | apresentação de responsável, animais e visitas |
| S13 | src/main/resources/db/h2/schema.sql; src/main/resources/db/mysql/schema.sql; src/main/resources/db/postgres/schema.sql | FKs e unicidade de nome por responsável |

🟢 CONFIRMADO — Evidências adicionais de intenção estão nos commits e ADRs de ../domain.md e ../adrs/. Os testes existentes ficam em src/test/java/org/springframework/samples/petclinic/owner/; sua presença não significa aprovação desta spec ou cobertura medida.
