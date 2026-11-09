# Veterinários — Casos de borda

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-04:** Não há política de cache. Confirmada ausência de política operacional a preservar. Não significa remover cache nem definir TTL=0; manter declaração atual e escolher política em evolução se necessária.
- 🟢 **VET-D-01:** Somente a aplicação consome; campos desnecessários podem ser removidos numa refatoração. Não há consumidor externo informado. Simplificação de campos é permitida no planejamento de refatoração após verificar uso; nenhum campo concreto foi declarado desnecessário nem removido.
- 🟢 **VET-D-02:** Não há contrato de erro definido. Não há contrato conhecido a preservar para falhas da consulta; não atribuir status novo como comportamento do legado.
- 🟢 **VET-D-03:** Ordem alfabética. Ordenação alfabética passa a ser requisito desejado confirmado para veterinários; é diferença em relação a findAll/PageRequest sem Sort explícito no legado.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e evidência

🟢 CONFIRMADO — Catálogo baseado em [requirements.md](requirements.md), [design.md](design.md) e [contracts.md](contracts.md), com vínculos às verificações propostas em [tasks.md](tasks.md). Nenhum caso foi executado nesta etapa.

🟢 CONFIRMADO — 🟢 indica declaração/ramo de código; 🟡 indica consequência inferida; 🔴 indica resultado não comprovado ou decisão pendente. 🟡 A coluna de verificação descreve investigação futura, não mudança autorizada de código.

## Catálogo

🟢 CONFIRMADO — Caminhos abreviados na coluna Origem são relativos a src/main/java/org/springframework/samples/petclinic/. Templates e schemas complementares são referidos abaixo.

| ID | Condição | Comportamento e limites | Verificação proposta | Origem | Rastreabilidade |
|---|---|---|---|---|---|
| VET-EC-01 | Catálogo vazio na primeira página | 🟢 totalPages=0 ainda permite page=1; model recebe lista vazia e retorna vets/vetList. Não há notFound. | Verificar view e quatro atributos; observar ausência de linhas e do bloco de paginação no template. | vet/VetController.java | VET-RF-01; VET-CA-02; TT-01/06 |
| VET-EC-02 | Catálogo vazio e página 2 | 🟢 Após consultar, page=2 excede max(0,1) e redireciona page=1, em vez de retornar lista vazia diretamente. | Caracterizar retorno antes e depois de seguir o redirect; não unificar esses passos numa resposta presumida. | vet/VetController.java | VET-RF-02; VET-CA-05; TT-01 |
| VET-EC-03 | Página zero ou negativa | 🟢 Consulta usa índice 0 via max(page,1)-1; checagem posterior usa o valor externo original e redireciona à primeira. | Capturar Pageable e redirect; incluir página negativa além do zero já esperado no teste legado. | vet/VetController.java | VET-RF-02; VET-CA-04; TT-01 |
| VET-EC-04 | Página alta ou não convertível | 🟢 Inteiro alto é consultado antes da validação de faixa. 🔴 Overflow, texto não numérico e parâmetros repetidos são conversões de framework não caracterizadas. | Separar inteiro representável de erro de binding; registrar status/body e falhas anteriores ao redirect, sem inventar 400/404. | vet/VetController.java | VET-D-02; TT-05 |
| VET-EC-05 | Falha de repository antes de corrigir faixa | 🟢 Nenhum catch de DataAccessException no controller; a falha pode interromper antes de testar faixa. | Simular falha nas duas assinaturas findAll e caracterizar HTTP em teste integrado. Não apresentar indisponibilidade como catálogo vazio. | vet/VetRepository.java | VET-CA-03/08; VET-D-02; TT-05 |
| VET-EC-06 | Mudança do catálogo entre páginas | 🟢 PageRequest não declara Sort e cada requisição consulta separadamente, com cache anotado. 🟡 Alterações ou ordem variável podem afetar navegação; resultado concreto não medido. | Caracterizar navegação com atualização externa; separar ordenação desejada e consistência de snapshot do comportamento observado. | vet/VetController.java | VET-D-03; D-04; TT-01/06 |
| VET-EC-07 | Vet sem especialidades | 🟢 Inicializa HashSet vazio; getSpecialties retorna lista vazia, contagem zero e HTML exibe chave none. | Verificar getters e template em idioma selecionado; não confundir coleção vazia com elemento null. | vet/Vet.java | VET-RF-04; VET-CA-10; TT-03/06 |
| VET-EC-08 | Especialidade null ou nome null | 🟢 addSpecialty não rejeita null e comparador não declara nullsFirst/nullsLast. 🔴 Falha depende de comparação/renderização executada; caso unitário pode não forçar comparação. | Usar zero/um/vários itens, incluindo dois nomes que forcem comparar com null; observar getter, HTML e serialização separadamente. Não inserir filtragem silenciosa. | vet/Vet.java | VET-INV-01; TT-05 |
| VET-EC-09 | Repetição de objeto ou instâncias de mesmo ID | 🟢 HashSet usa igualdade dos objetos; classes base não implementam equals/hashCode de negócio. Mesma instância não se repete; duas instâncias de mesmo ID não ganham deduplicação própria. | Testar memória separadamente de persistência. Não inferir que Set garante unicidade SQL por ID/nome. | vet/Vet.java | VET-RF-04; TT-03/04 |
| VET-EC-10 | Alterar a lista retornada de especialidades | 🟢 getSpecialties coleta nova List a partir do Set; alterar a estrutura dessa lista não altera estruturalmente o Set. Objetos Specialty referenciados continuam compartilhados. | Remover item da lista e consultar getter novamente; distinguir de mudar name no objeto retornado. Não prometer cópia profunda. | vet/Vet.java | VET-RF-04; TT-03 |
| VET-EC-11 | Lista completa vazia ou consulta com page em /vets | 🟢 Vets inicializa ArrayList; addAll de coleção vazia mantém lista vazia. showResourcesVetList não recebe page e chama findAll sem Pageable. | Testar handler vazio e query page adicional; caracterizar wire vazio. Não converter /vets em lista paginada por semelhança de nome. | vet/Vets.java | VET-RF-03; VET-CA-06/07; TT-02 |
| VET-EC-12 | Accept XML, ausente ou não suportado | 🟢 Mappings não fixam produces; JAXB está declarado em Vet/Vets; teste JSON só afirma um JSONPath. 🔴 Conversores e negociação efetivos não comprovados. | Registrar formato/headers/body/status para Accept relevantes no ambiente escolhido; não declarar XML garantido ou erro 406 como contrato já observado. | vet/Vets.java | VET-D-01/02; TT-02/05 |
| VET-EC-13 | Catálogo grande em /vets | 🟢 Handler carrega todos e copia para envelope; especialidades EAGER. 🟡 Memória/payload/custo podem crescer; sem medição ou limite confirmado. | Medir com volumes representativos se a reimplementação exigir; não inventar paginação, truncamento ou SLA. | vet/VetController.java | VET-RF-03; TT-02/04 |
| VET-EC-14 | Cache desatualizado após alteração externa | 🟢 Consultas anotadas com vets; nenhum endpoint de alteração/invalidação neste controller. 🔴 Provedor, expiração e frescor não confirmados. | Identificar CacheManager e caracterizar leitura antes/depois de mudança em base isolada; registrar TTL/invalidação somente com evidência. | system/CacheConfiguration.java | D-04; TT-06 |
| VET-EC-15 | Consultas completa e paginada no mesmo cache | 🟢 Duas assinaturas usam nome vets, retornando Collection e Page. 🔴 Chaves efetivas e comportamento do cache dependem da infraestrutura ativa, não foram medidos. | Alternar findAll() e findAll(Pageable) no ambiente integrado e observar isolamento de resultados; não afirmar colisão apenas pelo mesmo nome do cache. | vet/VetRepository.java | D-04; TT-06 |
| VET-EC-16 | Pares repetidos na tabela de junção | 🟢 MySQL/PostgreSQL declaram UNIQUE(vet_id,specialty_id); H2 não. 🔴 Efeito ORM sobre dados históricos inconsistentes não executado. | Inventariar pares e vínculos em avaliação de dados; validar por perfil sem deduplicar automaticamente ou presumir mesma constraint em todos. | vet/Vet.java | VET-INV-01; TT-04/05; TM-01 |

## Cenários prioritários de caracterização

### Base vazia — VET-EC-01/02

🟢 CONFIRMADO — Cenários derivados de showVetList, supondo consulta concluída normalmente.

```gherkin
Dado uma página vazia com totalPages igual a zero
Quando page igual a 1 é solicitado
Então o handler retorna vets/vetList com listVets vazia

Dado uma página vazia com totalPages igual a zero
Quando page igual a 2 é solicitado
Então o handler retorna redirect:/vets.html com atributo page igual a 1
```

### Cópia de especialidades — VET-EC-10

🟢 CONFIRMADO — O getter coleta lista nova; não clona Specialty.

```gherkin
Dado um Vet com uma especialidade válida em seu Set
Quando a lista devolvida por getSpecialties tem um item removido
Então uma nova chamada de getSpecialties ainda encontra o item no Set
```

### Negociação — VET-EC-12

🔴 VET-D-01 — Cenário coleta evidência; não predetermina formato ou status.

```gherkin
Dado um catálogo conhecido em ambiente de teste com conversores identificados
Quando GET /vets é chamado com Accept application/json e application/xml em requisições separadas
Então registrar para cada resposta o status, Content-Type e body efetivos e compará-los com o contrato que deve ser preservado
```

### Frescor — VET-EC-14/15

🔴 D-04 — A anotação não determina a política desejada de atualização.

```gherkin
Dado um ambiente isolado com CacheManager efetivo identificado
Quando consultas completas e paginadas são feitas antes e depois de uma alteração externa do catálogo
Então registrar chaves e resultados observáveis, eventual invalidação e tempo até a alteração aparecer
```

## Evidências complementares

- 🟢 src/main/resources/templates/vets/vetList.html: renderiza especialidades e none; links/bloco de paginação dependem do model.
- 🟢 src/main/resources/db/h2/schema.sql, db/mysql/schema.sql e db/postgres/schema.sql: FKs e diferenças de unicidade da tabela vet_specialties.
- 🟢 src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java e NamedEntity.java: ausência de igualdade por ID/nome nas classes lidas; validação @NotBlank não é garantia de integridade de todo dado externo.
- 🟢 src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java: expectativas HTML/redirect/um campo JSON com repository mockado. Não resolve cache, negociação XML ou erros reais.
- 🟢 src/test/java/org/springframework/samples/petclinic/vet/VetTests.java: serialização Java sem especialidades preenchidas; não comprova todos os casos de coleção/representação.

## Decisões e verificações pendentes

| Lacuna | Casos | Evidência ou decisão necessária |
|---|---|---|
| 🔴 D-04 | VET-EC-06/14/15 | Provedor, chaves, limites, invalidação e frescor esperado |
| 🔴 VET-D-01 | VET-EC-11/12 | Formatos e shape completos necessários e observados |
| 🔴 VET-D-02 | VET-EC-04/05/12 | Status/body de conversão, repository e cache |
| 🔴 VET-D-03 | VET-EC-06 | Ordem desejada dos veterinários sem Sort explícito atual |
| 🔴 VET-INV-01 | VET-EC-08/09/16 | Efeito dos dados inconsistentes e política de tratamento |

🔴 D-03 — Proteção externa de acesso continua desconhecida. 🟢 O módulo não verifica papéis de usuário e não expõe alterações por esses handlers; isso não comprova acesso público real nem política de produção.

🟡 ORIENTAÇÃO — Separar correção pretendida de comportamento observado. Resolver casos de borda não autoriza adicionar filtro, paginação ao body, ordenação ou saneamento de dados que o contrato legado não declara.
