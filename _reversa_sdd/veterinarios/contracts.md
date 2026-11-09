# Veterinários — Contratos HTTP

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

🟢 CONFIRMADO — Dois handlers de VetController atendem a consulta HTML paginada e a lista completa de dados. Este documento complementa [requirements.md](requirements.md) e [design.md](design.md), cobrindo VET-RF-01 a VET-RF-04. Não foram feitas chamadas HTTP nem executados testes nesta etapa.

🟢 CONFIRMADO — Fontes principais: src/main/java/org/springframework/samples/petclinic/vet/VetController.java, VetRepository.java, Vet.java e Vets.java; src/main/resources/templates/vets/vetList.html; src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java. Anotações e asserções de teste são evidência estática, não captura de resposta real.

## VET-HTTP-01 — GET /vets.html

### Entrada

| Item | Contrato extraído |
|---|---|
| 🟢 Método/caminho | GET /vets.html |
| 🟢 Query page | int, @RequestParam(defaultValue="1") |
| 🟢 Body | Nenhum parâmetro de body declarado |
| 🟢 Filtros/ordem | Nenhum parâmetro de filtro, pageSize ou sort lido pelo handler |
| 🟢 Consulta | findAll(PageRequest.of(max(page,1)-1,5)); sem Sort explícito |
| 🔴 Conversão inválida | Resultado de page não numérico, overflow ou valores repetidos não caracterizado |

🟢 CONFIRMADO — Fonte: VetController.showVetList/findPaginated. O valor padrão é o declarado na anotação; efeito exato de string vazia e parâmetros repetidos depende da conversão MVC ainda não executada.

### Retorno de página válida

🟢 CONFIRMADO — Após a consulta, quando page está entre 1 e max(totalPages,1), o handler retorna a view vets/vetList com os atributos:

| Atributo | Tipo no model | Origem |
|---|---|---|
| 🟢 currentPage | int | page externo solicitado |
| 🟢 totalPages | int | Page.getTotalPages() |
| 🟢 totalItems | long | Page.getTotalElements() |
| 🟢 listVets | List<Vet> | Page.getContent() |

🟢 CONFIRMADO — O template mostra firstName e lastName, percorre as especialidades ordenadas por Vet.getSpecialties e usa a mensagem none quando nrOfSpecialties é zero. Links numéricos e primeira/anterior/próxima/última usam /vets.html?page=...; bloco de navegação só aparece com totalPages > 1. Dados de paginação são model da view, não um envelope JSON nessa rota.

🟢 CONFIRMADO — Catálogo vazio na página 1 retorna a mesma view com lista vazia; não há rejeição notFound. O template não define mensagem dedicada de catálogo vazio no código examinado.

### Redirect de página inválida

🟢 CONFIRMADO — Se page < 1 ou page > max(totalPages,1), depois da consulta o handler adiciona atributo page=1 e retorna redirect:/vets.html. Índice seguro da consulta não altera o valor externo usado na checagem. Com totalPages=0, page=1 é válido e page=2 redireciona.

🟢 CONFIRMADO — VetControllerTests.showVetListRedirectsOutOfBoundsPageToFirstPage espera 3xx e URL /vets.html?page=1 para páginas 0 e 2 com Page mockada. O código exato do status 3xx não é afirmado por esse teste. showVetListHtml espera 200, atributo listVets e view vets/vetList para page=1. Nenhuma dessas expectativas foi executada aqui.

### Exemplo de requisição

🟡 INFERIDO — Exemplo construído do mapping, sem execução:

```http
GET /vets.html?page=1 HTTP/1.1
Accept: text/html
```

🟢 CONFIRMADO — Retorno normal do handler é vets/vetList após consulta e faixa válida. 🔴 Content-Type/charset/headers finais dependem da renderização MVC e não foram capturados; o mapping não declara produces.

## VET-HTTP-02 — GET /vets

### Entrada e consulta

| Item | Contrato extraído |
|---|---|
| 🟢 Método/caminho | GET /vets |
| 🟢 Parâmetros lidos | Nenhum parâmetro no método showResourcesVetList |
| 🟢 Consulta | VetRepository.findAll() sem Pageable |
| 🟢 Resultado Java | Novo Vets com todos os itens copiados para getVetList via addAll |
| 🟢 Saída do handler | @ResponseBody Vets; conversão delegada ao framework |
| 🔴 Limites/formato | Sem limite de itens no handler; representação completa e negociação não capturadas |

🟢 CONFIRMADO — Acrescentar page na query não ativa paginação nesse método. A rota /vets não usa o handler /vets.html. A ordem de veterinários não é explicitamente declarada em findAll; não deduzir ordem da fixture do teste.

### Envelope e serialização

🟢 CONFIRMADO — Vets armazena List<Vet> e getVetList inicializa ArrayList quando o campo está null. Coleção de repository vazia gera lista inicializada vazia no envelope. Vet expõe id/firstName/lastName herdados, getSpecialties como lista derivada e getNrOfSpecialties. Esses getters descrevem o objeto Java, não provam todos os campos finais de JSON/XML.

🟢 CONFIRMADO — VetControllerTests.showResourcesVetList, com repository mockado, declara as expectativas: Accept application/json → status 200, Content-Type application/json e JSONPath $.vetList[0].id igual a 1. Isso confirma o contrato esperado por esse teste, não uma execução aprovada nem um schema completo.

🟡 INFERIDO — A forma mínima ilustrativa compatível com o JSONPath do teste é a seguinte; outros campos/itens foram deliberadamente omitidos. Não usar o exemplo como payload completo ou schema fechado:

```json
{
  "vetList": [
    { "id": 1 }
  ]
}
```

🔴 VET-D-01 — Nomes, obrigatoriedade e nulabilidade de todos os campos serializados, shape de especialidades, lista vazia no wire e resposta a outros Accept precisam ser caracterizados. Vet/Vets possuem anotações JAXB, mas não há prova aqui de conversor XML HTTP ativo. Não prometer application/xml apenas pelo comentário da classe.

### Exemplo de requisição

🟡 INFERIDO — Requisição proposta para caracterização, não executada:

```http
GET /vets HTTP/1.1
Accept: application/json
```

🟢 CONFIRMADO — Se a consulta retorna normalmente, o método copia toda a coleção ao envelope e o devolve. Falhas de repositório, cache ou serialização podem impedir uma resposta normal; nenhum catch no handler define um fallback vazio.

## Representação de especialidades

| Caso | Comportamento confirmado no objeto/template | Limite |
|---|---|---|
| 🟢 Sem especialidades | HashSet inicializado vazio; lista vazia e contagem zero; HTML exibe chave none | 🔴 Wire completo não capturado |
| 🟢 Nomes não nulos | getSpecialties ordena por NamedEntity.getName e cria List nova | Não usa ordenação linguística/Locale explícita |
| 🟢 Mesmo objeto adicionado repetidamente | Set.add usa igualdade do objeto | Não existe igualdade por ID/nome implementada nas classes base lidas |
| 🟢 Specialty ou nome null | Sem guarda local em addSpecialty/comparador | 🔴 VET-INV-01: efeito depende de comparação/renderização efetivamente realizadas |

🟢 CONFIRMADO — Fonte: Vet.java e template vetList.html. Ordenação das especialidades não ordena os veterinários da resposta. 🔴 VET-D-03 — Ordem desejada de veterinários ainda precisa ser confirmada.

## Erros e negociação

| Condição | Tratamento local | Contrato HTTP comprovado nesta etapa |
|---|---|---|
| 🟢 Página inteira fora da faixa e consulta bem-sucedida | Redirect com page=1 | Expectativa de teste 3xx/URL; não executada |
| 🟢 Page não convertível para int | Delegado ao MVC | 🔴 Status, body e mensagem não caracterizados |
| 🟢 DataAccessException em qualquer findAll | Sem catch no controller | 🔴 Não definir status/shape por suposição |
| 🟢 Falha de cache | Nenhum fallback local explicitado | 🔴 Efeito depende da infraestrutura ativa |
| 🟢 Coleção vazia | View ou envelope vazio, sem ramo de erro | Resultado Java confirmado; wire não capturado |
| 🟢 Dados inválidos em especialidades | Sem tolerância específica declarada | 🔴 Status/renderização não caracterizados |
| 🟢 Accept não suportado / XML | Mapping não declara produces; conversão é de framework | 🔴 VET-D-01/02: caracterizar negociação e erro |

🟢 CONFIRMADO — Nenhum handler retorna ResponseEntity ou fixa status com ResponseStatus neste módulo. Testes de sucesso não definem o contrato de erros. Não apresentar 400/404/406/500 como respostas normativas sem evidência ou decisão nova identificada.

## Cache, transações e acesso

- 🟢 CONFIRMADO — Ambos os métodos findAll do repository declaram Transactional(readOnly=true) e Cacheable("vets"). Isso é configuração interna de consulta, não header Cache-Control ou contrato de cache HTTP.
- 🟢 CONFIRMADO — system/CacheConfiguration habilita caching e fornece customizador JCache que cria vets com estatísticas. 🔴 D-04 — Provedor ativo, chaves/entradas efetivas, TTL, capacidade, invalidação e frescor após mudança externa não foram comprovados. Não prometer catálogo sempre atualizado.
- 🟢 CONFIRMADO — Nenhum endpoint de escrita ou invalidação de cache é exposto por VetController. Não há autenticação/checagem de papel nesses handlers. 🔴 D-03 — Proteções externas de implantação permanecem desconhecidas.
- 🟢 CONFIRMADO — Não há ETag, Last-Modified ou política de versão do contrato definida nos handlers examinados. Não inferir garantia de snapshot consistente entre duas requisições ou entre lista completa e paginada.

## Rastreabilidade e validação

| Contrato | Requisitos | Critérios e tarefas propostas |
|---|---|---|
| 🟢 VET-HTTP-01 | VET-RF-01, VET-RF-02 e apresentação VET-RF-04 | VET-CA-01–05/09/10; T-04/06; TT-01/06 |
| 🟢 VET-HTTP-02 | VET-RF-03 e representação de especialidades VET-RF-04 | VET-CA-06–09; T-05; TT-02/03/05 |
| 🟢 Infraestrutura de consulta | Nome de cache/readOnly documentados nos RNFs | T-03/07; TT-04/06; D-04 |

🟡 INFERIDO — Associação a tarefas é plano de validação, não trabalho executado. Fontes compartilhadas: src/main/java/org/springframework/samples/petclinic/model/ e system/CacheConfiguration.java; schemas em src/main/resources/db/{h2,mysql,postgres}/schema.sql. Ver [tasks.md](tasks.md) para critérios de pronto e [requisitos](requirements.md) para VET-D-01/02/03 e VET-INV-01.
