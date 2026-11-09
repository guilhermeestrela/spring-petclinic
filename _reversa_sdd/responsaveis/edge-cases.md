# Responsáveis — Casos de borda

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


## Escopo e uso

🟢 CONFIRMADO — Este catálogo complementa [requirements.md](requirements.md), [design.md](design.md), [contracts.md](contracts.md) e [tasks.md](tasks.md). Foi derivado do código e das lacunas já registradas; nenhum cenário deste arquivo foi executado. Cenários CA representam critérios documentados, não aprovação de testes.

🟢 CONFIRMADO — Na tabela, o comportamento de código recebe 🟢; hipóteses de consequência recebem 🟡; resultados dependentes de execução ou decisão humana recebem 🔴. 🟡 As ações da coluna de verificação são propostas de caracterização futura, não alterações aprovadas do produto.

## Catálogo

🟢 CONFIRMADO — Referências Java abreviadas pertencem a `src/main/java/org/springframework/samples/petclinic/owner/`; referências completas apontam para a raiz do projeto. IDs TT correspondem às tarefas propostas em tasks.md.

| ID | Condição de borda | Comportamento / confiança | Verificação proposta | Origem no legado | Rastreabilidade |
|---|---|---|---|---|---|
| EC-01 | Sobrenome omitido, vazio ou só espaços | 🟢 null vira string vazia; demais valores recebem strip. Vazio consulta o prefixo mais amplo; não há @Valid no objeto Owner da busca. | Submeter cada forma na página 1 e observar filtro passado ao repositório; não aplicar as regras obrigatórias do cadastro à busca. | OwnerController.java:95; OwnerRepository.java | RESP-RF-02; TT-02 |
| EC-02 | Busca sem registros e page fora de faixa | 🟢 Faixa é validada antes de resultado vazio. Com totalPages=0 e page=1, retorna busca com lastName/notFound. Com page=2, redireciona página 1 antes disso. | Caracterizar as duas requisições e a sequência de retorno, sem converter toda ausência em 404. | OwnerController.java:95 | RESP-RF-02; TT-02 |
| EC-03 | Page zero, negativa, muito alta ou não numérica | 🟢 Inteiros até 0 usam página interna 0, mas redirecionam externamente à página 1 após consultar. 🔴 Resultado HTTP de overflow, não numérico ou falha do banco antes do redirect não foi executado. | Validar Pageable para inteiros; caracterizar conversão e status separadamente. Não alegar proteção de toda entrada apenas por Math.max. | OwnerController.java:95; OwnerController.java:141 | RESP-RF-02; D-06; TT-02/08 |
| EC-04 | Filtro perdido ao seguir links da lista | 🟢 Redirect de página inválida inclui lastName normalizado não vazio; links do template da lista incluem somente page. 🔴 A intenção da diferença é desconhecida. | Começar com prefixo que produz várias páginas, inspecionar link e navegar; registrar comportamento sem corrigir por inferência. | src/main/resources/templates/owners/ownersList.html; OwnerController.java:95 | RESP-RF-02; RESP-D-07; TT-08 |
| EC-05 | Campos no limite de tamanho | 🟢 Person limita nomes a 30; Owner limita cidade a 80 e endereço a 255; Visit limita descrição a 255. PetValidator usa name.length() até 30. | Usar texto não branco nos pares 30/31, 80/81 e 255/256. Os limites são de validação declarada/length Java, sem contrato de contagem de grafemas visuais estabelecido. | src/main/java/org/springframework/samples/petclinic/model/Person.java; Owner.java; PetValidator.java; Visit.java | RESP-RF-01/04/06/07/08; TT-01/03/05 |
| EC-06 | Telefone com tamanho ou formato inesperado | 🟢 Owner exige texto e padrão de 10 dígitos; não há normalização para remover pontuação ou prefixos. | Testar 9/10/11 dígitos, letras, espaços e pontuação; não transformar o campo em número que descarte zeros iniciais. | Owner.java:62 | RESP-RF-01/04; TT-01 |
| EC-07 | Identidade enviada em campo oculto ou divergente no método | 🟢 Binders bloqueiam id e *.id. Mismatch de Owner existe como ramo interno após validação; não prova que um formulário consegue substituir o ID carregado. | Separar teste de campos suprimidos do teste direto de processUpdateOwnerForm com objeto divergente. Lista de bloqueio não é allowlist de todas as demais propriedades. | OwnerController.java:60; OwnerController.java:154; PetController.java:90; VisitController.java:52 | RESP-RF-09; TT-06 |
| EC-08 | Responsável inexistente ou animal de outro responsável | 🟢 Ausência de Owner lança IllegalArgumentException. Em visita, pet não pertencente ao Owner também lança. PetController.findPet pode retornar null na edição. 🔴 Status e renderização subsequentes não comprovados. | Caracterizar GET e POST de edição/visita separadamente, confirmando ausência de save nos carregamentos que lançam; não uniformizar em 404 sem decisão. | OwnerController.java:65; PetController.java:76; VisitController.java:64 | RESP-RF-03/07/08/09; D-06; TT-08 |
| EC-09 | Nome de animal com caixa diferente ou espaços de borda | 🟢 Lookup usa equalsIgnoreCase, sem strip. Luna e luna coincidem na comparação Java; Luna e Luna seguida de espaço não são igualadas por esse método. 🔴 SQL pode comparar de outra forma conforme perfil/collation. | Testar as duas formas por responsável, distinguindo validação Java de restrição de banco. Não assumir normalização Unicode/acentos. | Owner.java:147; PetController.java:108; PetController.java:145 | RESP-RF-06/07; D-02; TT-04/07 |
| EC-10 | Edição mantendo o próprio nome ou nome de outro ID | 🟢 Primeiro pet encontrado por nome só causa erro se seu ID difere do pet editado. Caso de mesmo ID é aceito pela regra de duplicidade. | Testar coleção válida sem nomes repetidos; para dados históricos já duplicados, caracterizar a influência do primeiro resultado, sem deduplicar a base silenciosamente. | Owner.java:147; PetController.java:145 | RESP-RF-07; TT-04 |
| EC-11 | Objetos novos distintos ou persistidos com mesmo ID | 🟢 addPet ignora null e mesma instância; bloqueia dois persistidos com mesmo ID, mas admite dois novos distintos. getPet por ID ignora novos. | Verificar coleção antes/depois e não confundir deduplicação de identidade com regra de nome; igualdade de negócio não foi implementada na base. | Owner.java:100; Owner.java:129; src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java | RESP-RF-09; TT-04 |
| EC-12 | Tipo desconhecido, caixa diferente ou tipo null na edição | 🟢 Formatter compara nome exato e lança ParseException quando ausente. PetValidator exige tipo somente em novo; schemas exigem type_id NOT NULL. 🔴 Falha HTTP de conversão/tipo vazio em edição não caracterizada. | Testar nome conhecido, variante de caixa, desconhecido e vazio separadamente; distinguir erro de formatter, validator e integridade SQL. | PetTypeFormatter.java; PetValidator.java; src/main/resources/db/h2/schema.sql | RESP-RF-05/06/07; TT-03/08 |
| EC-13 | Nascimento hoje, amanhã, null ou texto inválido | 🟢 Hoje não é futuro e passa a regra temporal; amanhã é rejeitado; null é rejeitado no PetValidator. 🔴 Combinação exata de erros para texto não convertível depende do binding. | Testar fronteiras com referência temporal controlada no ambiente de caracterização; não usar uma data absoluta que deixará de ser futura. | PetValidator.java:39; PetController.java:108; PetController.java:145; Pet.java | RESP-RF-06/07; TT-03/08 |
| EC-14 | Duas submissões concorrentes de mesmo nome de animal | 🟢 Controller verifica em memória e chama saveAndFlush; banco declara unicidade. 🔴 Resultado exato do erro reconhecido por perfil/driver não foi comprovado. | Em base isolada, concorrer criações e edição com nomes conflitantes e observar linhas persistidas e mensagem. H2/PostgreSQL explicitam comparação sem caixa; MySQL depende de collation não declarada. | PetController.java:108; PetController.java:145; src/main/resources/db/h2/schema.sql; src/main/resources/db/mysql/schema.sql; src/main/resources/db/postgres/schema.sql | RESP-RF-06/07; D-02; TT-07 |
| EC-15 | Violação SQL sem marcador ou mensagem null | 🟢 Apenas mensagem não nula contendo unique_owner_pet_name, ignorando caixa, é traduzida em name/duplicate. Outras DataIntegrityViolationException são relançadas. | Separar mocks da mensagem reconhecida, não reconhecida e null; completar com erro real do banco. Não afirmar que todo conflito retorna formulário ou 409. | PetController.java:202 | RESP-RF-06/07; D-02; TT-04/07 |
| EC-16 | Visita com date omitida ou vazia | 🟢 Construtor define amanhã; campo não tem @NotNull e condição temporal ignora null. 🔴 Valor final ligado por cada requisição e obrigatoriedade desejada permanecem abertos. | Enviar separadamente sem date e com date vazia, usando descrição válida para evitar erro independente. Registrar objeto ligado, BindingResult e persistência em teste. | Visit.java:50; VisitController.java:98 | RESP-RF-08; D-01; TT-08 |
| EC-17 | Hoje/amanhã na virada do dia e em fusos distintos | 🟢 Construtor, minVisitDate e validação chamam LocalDate.now em momentos separados, sem Clock/ZoneId explícitos. 🔴 Fuso de negócio não definido. | Caracterizar em ambiente com relógio/fuso conhecidos; manter a distinção entre padrão inicial e instante da validação. Não prometer mesma referência de dia durante toda a requisição. | Visit.java:50; VisitController.java:84; VisitController.java:98; PetController.java:108 | RESP-RF-06/07/08; D-05; TT-03/05/08 |
| EC-18 | Repetir visita em memória ou repetir POST | 🟢 A mesma instância adicionada no carregamento e no handler não se repete no Set. Não existe deduplicação por descrição/data ou chave de idempotência nos componentes examinados. | Distinguir duas adições da mesma instância de duas requisições com instâncias novas; não afirmar que o Set impede agendamentos repetidos. | VisitController.java:64; VisitController.java:98; Pet.java | RESP-RF-08; TT-05 |
| EC-19 | GET ou POST inválido após mutação em memória | 🟢 GET de Pet/Visit anexa objeto em memória; GET não chama save. Handlers com erros retornam antes da chamada explícita de persistência. 🔴 Efeitos sob eventual transação externa não foram demonstrados. | Verificar chamadas de repository e base em execução isolada; não igualar alteração do model a escrita confirmada no banco. | PetController.java:101; VisitController.java:64; VisitController.java:98 | RESP-RF-06/08; TT-04/05 |
| EC-20 | Flash ausente ou mensagem de erro sem redirect | 🟢 OwnerController declara flash error em ramo que retorna view sem redirect. Detalhe acessa message/error no script sem checar null. 🟡 Ausência de elemento pode interromper script; efeito em navegador não foi observado. | Abrir detalhe com nenhum, um e ambos os atributos; inspecionar console e conteúdo. Não afirmar exibição imediata de flash no formulário apenas pela chamada addFlashAttribute. | OwnerController.java:78; OwnerController.java:154; src/main/resources/templates/owners/ownerDetails.html | RESP-RF-01/03/04; TT-08 |
| EC-21 | Responsável com muitos animais/visitas e ordem variável | 🟢 Collections são EAGER e a lista paginada limita responsáveis, não o tamanho de cada agregado; PageRequest não declara Sort. 🟡 Custo/instabilidade de ordem pode aumentar; sem medição. | Medir perfil de consultas e navegação com dados representativos; não inventar SLA, limite de pets ou ordenação persistente nova. | Owner.java; Pet.java; OwnerController.java:141 | RESP-RF-02/03; RESP-D-08; caracterização futura |

## Cenários de caracterização prioritários

### EC-02 — Base vazia e faixa de página

🟢 CONFIRMADO — A ordem dos ramos de OwnerController.processFindForm define os resultados abaixo quando a consulta retorna normalmente.

```gherkin
Dado uma consulta sem resultados e totalPages igual a zero
Quando a página 1 é solicitada
Então o formulário owners/findOwners retorna com lastName/notFound

Dado a mesma consulta sem resultados
Quando a página 2 é solicitada
Então o handler redireciona à página 1 antes de avaliar o resultado vazio
```

### EC-15 — Falha SQL não reconhecida

🟢 CONFIRMADO — O catch em PetController só traduz a mensagem com o marcador esperado.

```gherkin
Dado um formulário de animal sem erro de validação
Quando saveAndFlush lança DataIntegrityViolationException com mensagem sem unique_owner_pet_name
Então a exceção original é relançada e não é convertida em name/duplicate pelo catch
```

### EC-16 — Data de visita indefinida

🔴 LACUNA D-01 — O cenário define a evidência a coletar; não antecipa aceitação ou rejeição do HTTP.

```gherkin
Dado um responsável e seu animal e descrição válida para nova visita
Quando duas submissões isoladas enviam respectivamente date ausente e date vazia
Então registrar em cada execução a data após binding, os erros de campo, a chamada de save e a resposta efetiva
```

### EC-18 — Repetição e identidade

🟢 CONFIRMADO — No nível de objeto, Pet.addVisit delega ao Set e a entidade base não redefine igualdade por data/descrição.

```gherkin
Dado um Pet com coleção de visitas inicialmente vazia
Quando a mesma instância de Visit é adicionada duas vezes
Então a coleção contém uma única referência a essa instância
```

🟡 INFERIDO — Uma caracterização separada de duas requisições válidas iguais deve verificar quantos registros foram gravados; o cenário em memória não garante idempotência HTTP.

## Decisões pendentes e vínculos

| Lacuna existente | Casos relacionados | Evidência que falta |
|---|---|---|
| 🔴 D-01 | EC-16 | Binding ausente/vazio e política de obrigatoriedade da data |
| 🔴 D-02 | EC-09/12/14/15 | Collation, restrição, erro real e concorrência por perfil |
| 🔴 D-03 | EC-07/08 | Política externa de acesso; IDs bloqueados e vínculo de dados não provam autorização |
| 🔴 D-05 | EC-13/17 | Fuso/relógio de negócio e regra na virada do dia |
| 🔴 D-06 | EC-03/08/12/13 | Status e apresentação das falhas de binding/IDs ausentes |
| 🔴 RESP-D-07 | EC-04 | Intenção da omissão de filtro nos links da lista |
| 🟡 RESP-D-08 | EC-21 | Medições de carga/ordem antes de propor limite ou otimização |

🟢 CONFIRMADO — Contratos atuais não definem papéis autenticados, chave idempotente, regra de disponibilidade de agenda ou atribuição de veterinário à visita. 🟡 Eventual melhoria desses pontos deve ter escopo próprio; este catálogo registra evidências e perguntas, sem alterar o legado nem completar lacunas com decisões inventadas.
