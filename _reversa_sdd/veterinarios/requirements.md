# Veterinários — Requisitos

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


## Visão Geral

🟢 CONFIRMADO — A unit corresponde ao pacote Java vet e disponibiliza consulta de veterinários e suas especialidades em página HTML paginada e em uma resposta de dados com envelope Vets. Vet e Specialty são entidades JPA; Vets é objeto de transporte. Fontes V01–V06 na rastreabilidade.

🟡 INFERIDO — A persona é a pessoa que consulta o catálogo da clínica; não existe papel autenticado específico implementado aqui. A prioridade MoSCoW proposta deriva da posição dos fluxos e dos testes presentes, não de métricas de frequência.

🟢 CONFIRMADO — Extração estática. Não foram executados testes, endpoints ou cache; asserções de testes são evidência de contrato esperado, não resultado aprovado nesta sessão.

## Responsabilidades

- 🟢 Exibir veterinários em página de cinco registros, com navegação e correção de faixa de página (V01, V07).
- 🟢 Retornar todos os veterinários no envelope de dados de GET /vets (V01, V05).
- 🟢 Expor especialidades por nome e quantidade; apresentar indicação localizada de ausência na página HTML (V03, V07).
- 🟢 Consultar dados por repositório com transações readOnly e anotações de cache (V02, V08).

## Regras de Negócio

| ID | Regra extraída | Fonte |
|---|---|---|
| VET-RN-01 | 🟢 GET /vets.html recebe page inteiro com padrão 1; tamanho interno é 5, índice interno max(page,1)-1. Não declara Sort explícito. | V01 showVetList/findPaginated |
| VET-RN-02 | 🟢 Consulta ocorre antes da checagem de faixa. Page menor que 1 ou maior que max(totalPages,1) redireciona /vets.html com page=1. | V01 showVetList |
| VET-RN-03 | 🟢 Página válida recebe currentPage, totalPages, totalItems e listVets e retorna vets/vetList. Base vazia na página 1 segue esse caminho, sem erro notFound local. | V01 addPaginationModel |
| VET-RN-04 | 🟢 GET /vets usa findAll sem Pageable, adiciona os resultados ao envelope Vets e retorna @ResponseBody; não implementa paginação nesse handler. | V01 showResourcesVetList, V05 |
| VET-RN-05 | 🟢 Vet herda id, firstName e lastName de modelo; Specialty herda id e name. Relação ManyToMany EAGER usa vet_specialties com vet_id/specialty_id. | V03, V04 |
| VET-RN-06 | 🟢 Coleção interna de especialidades inicializa HashSet sob demanda; getSpecialties devolve nova List ordenada por nome, getNrOfSpecialties retorna tamanho e addSpecialty adiciona ao conjunto. Não há normalização de nome ou validação de null local nesse método. | V03 |
| VET-RN-07 | 🟢 O template exibe nome completo, especialidades e mensagem none quando nrOfSpecialties é zero. Links de páginas só aparecem no bloco totalPages > 1. | V07 |
| VET-RN-08 | 🟢 As duas consultas findAll são @Transactional(readOnly=true), @Cacheable("vets") e declaram DataAccessException. Controller não captura essa falha. | V02 |
| VET-RN-09 | 🟢 Vets.getVetList inicializa ArrayList quando necessário e expõe a lista mutável. JAXB XmlRootElement/XmlElement estão declarados, mas não comprovam XML HTTP em execução. | V05 |

## Requisitos Funcionais

🟢 CONFIRMADO — Comportamentos na tabela são extraídos das fontes. 🟡 As prioridades são propostas; cenários referem-se aos critérios abaixo, ainda não executados.

| ID | Requisito | Prioridade proposta | Critério de aceite | Fonte |
|---|---|---|---|---|
| VET-RF-01 | 🟢 Consultar a página HTML de veterinários com paginação de 5. | Must | VET-CA-01/02/03 | V01, V07 |
| VET-RF-02 | 🟢 Redirecionar página fora da faixa à primeira. | Must | VET-CA-04/05 | V01 |
| VET-RF-03 | 🟢 Disponibilizar lista completa em envelope por GET /vets. | Must | VET-CA-06/07/08 | V01, V05, V09 |
| VET-RF-04 | 🟢 Exibir especialidades ordenadas e ausência localizada no HTML. | Must | VET-CA-09/10; VET-INV-01 | V03, V07 |

## Requisitos Não Funcionais

| Tipo | Comportamento declarado | Evidência | Confiança |
|---|---|---|---|
| Limite de consulta HTML | Até 5 veterinários por página; sem limite correspondente no handler de lista completa | V01 findPaginated/showResourcesVetList | 🟢 CONFIRMADO |
| Transação de leitura | Ambas as consultas do repositório são readOnly | V02 | 🟢 CONFIRMADO quanto à anotação; execução não medida |
| Cache | Consultas usam nome vets; system habilita caching e declara customizador JCache com estatísticas | V02, V08 | 🟢 CONFIRMADO quanto à declaração; 🔴 provedor/TTL/capacidade/invalidação não comprovados |
| Carregamento associado | Especialidades mapeadas como EAGER | V03 | 🟢 CONFIRMADO quanto ao mapeamento; custo não medido |

🟢 CONFIRMADO — Não há SLA de latência, retry próprio, limite global de catálogo ou política de autenticação/papel declarados nos handlers desta unit. Nenhum requisito quantitativo foi inventado.

## Critérios de Aceitação

🟢 CONFIRMADO — Critérios derivam dos ramos dos métodos e do template. Exceções são especificadas no nível do código quando o status HTTP não está definido. VET-CA-08 é critério de falha do caminho de dados; VET-INV-01 é investigação de dados inválidos, não comportamento de produto estabelecido.

### Consulta HTML — VET-RF-01/02

```gherkin
Cenário VET-CA-01: Consultar página válida
Dado um repositório com seis veterinários e a primeira página válida
Quando GET /vets.html solicita page igual a 1
Então o handler consulta PageRequest com índice 0 e tamanho 5 e retorna vets/vetList com currentPage, totalPages, totalItems e listVets

Cenário VET-CA-02: Consultar catálogo vazio
Dado que a consulta retorna página vazia com totalPages igual a zero
Quando a página 1 é solicitada
Então o handler retorna vets/vetList com lista vazia sem rejeição notFound

Cenário VET-CA-03: Falha ao consultar página
Dado que findAll(Pageable) lança DataAccessException
Quando o handler de página HTML executa a consulta
Então a falha não é convertida localmente em lista vazia nem redirect de página

Cenário VET-CA-04: Corrigir página não positiva
Dado que a consulta da primeira página retorna normalmente
Quando page igual a 0 é solicitado
Então a consulta usa índice interno 0 e o retorno redireciona /vets.html com page igual a 1

Cenário VET-CA-05: Corrigir página acima do total
Dado uma consulta cujo total é de duas páginas
Quando page igual a 3 é solicitado e a consulta retorna
Então o handler redireciona /vets.html com page igual a 1
```

### Lista completa — VET-RF-03

```gherkin
Cenário VET-CA-06: Retornar todos os veterinários
Dado que findAll sem Pageable fornece uma coleção de veterinários
Quando showResourcesVetList atende GET /vets
Então todos os itens são adicionados a Vets.getVetList e o envelope é retornado como body

Cenário VET-CA-07: Retornar envelope vazio
Dado que findAll sem Pageable retorna coleção vazia
Quando showResourcesVetList é chamado
Então o envelope contém lista vazia inicializada e não null

Cenário VET-CA-08: Propagar falha da lista completa
Dado que findAll sem Pageable lança DataAccessException
Quando showResourcesVetList é chamado
Então o controller não captura a falha para substituir o resultado por envelope vazio
```

🟢 CONFIRMADO — VetControllerTests.showResourcesVetList declara expectativa de 200, application/json e $.vetList[0].id igual a 1 para Accept application/json com repositório mockado. 🔴 Shape completo, outros media types e erros HTTP não foram capturados nesta extração; não prometer XML só pelas anotações JAXB.

### Especialidades — VET-RF-04

```gherkin
Cenário VET-CA-09: Ordenar especialidades
Dado um Vet com especialidades de nomes não nulos surgery e dentistry
Quando getSpecialties é consultado
Então a lista devolvida ordena dentistry antes de surgery sem ordenar em posição o Set interno

Cenário VET-CA-10: Exibir ausência de especialidade
Dado um Vet sem especialidades
Quando a linha desse veterinário é renderizada pelo template vets/vetList
Então a indicação da chave de mensagem none é exibida porque nrOfSpecialties é zero
```

🔴 VET-INV-01 — addSpecialty aceita null e o comparador usa NamedEntity.getName sem tratamento null. Não há contrato explícito para especialidade null ou nome null vindo de dados inconsistentes. Caracterizar cenários com múltiplos itens que forcem comparação antes de definir mensagem/resultado; não afirmar que qualquer lista com null falha em toda circunstância.

## Prioridade (MoSCoW)

| Requisito | Prioridade | Justificativa |
|---|---|---|
| VET-RF-01 | 🟡 Must | Caminho HTML principal, com template e teste MVC dedicados |
| VET-RF-02 | 🟡 Must | Ramo explícito de navegação com teste para páginas 0/2 |
| VET-RF-03 | 🟡 Must | Segundo contrato público definido no controller, com teste JSON dedicado |
| VET-RF-04 | 🟡 Must | Informação central da listagem e relacionamento persistido; ordenação também é esperada em teste JPA transversal |

🟢 CONFIRMADO — Não há funcionalidade comentada/depreciada neste pacote que fundamente Won’t. Não foram atribuídas prioridades Should/Could sem sinal do código ou decisão do usuário.

## Riscos e Lacunas

- 🔴 **D-04 (transversal):** confirmar CacheManager efetivo, TTL, capacidade, invalidação e frescor esperado após atualização externa. A anotação de cache não comprova política operacional.
- 🔴 **VET-D-01:** confirmar formatos/shape necessários de GET /vets além da expectativa JSON do teste e respostas para Accept não suportado. Anotações JAXB não bastam para garantir XML.
- 🔴 **VET-D-02:** caracterizar erros HTTP de parâmetros não numéricos/overflow e falhas de repositório/cache; não assumir 400/500 de contrato apenas pelo tipo de exceção.
- 🔴 **VET-D-03:** confirmar se existe ordem de veterinários desejada. PageRequest não declara Sort e findAll completo não declara ordem; a ordem das especialidades não determina a dos veterinários.
- 🔴 **VET-INV-01:** validar comportamento com dados inconsistentes de especialidades antes de definir política de tolerância/correção. @NotBlank herdado não equivale a NOT NULL em todos os schemas.
- 🟢 CONFIRMADO — A unit não expõe cadastro/edição/exclusão de veterinário/especialidade, busca por nome, filtro por especialidade ou vínculo com Visit. Isso registra ausência de handlers, não proibição definitiva de produto.
- 🔴 **D-03 (transversal):** autenticação e proteção de acesso externas não foram identificadas no código local desta unit.

## Rastreabilidade de Código

🟢 CONFIRMADO — Caminhos relativos à raiz do projeto. Campos herdados de modelo e infraestrutura de sistema permanecem compartilhados.

| Ref. | Arquivo | Símbolos / cobertura |
|---|---|---|
| V01 | src/main/java/org/springframework/samples/petclinic/vet/VetController.java | showVetList, findPaginated, addPaginationModel, showResourcesVetList |
| V02 | src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java | findAll() e findAll(Pageable), readOnly e cache |
| V03 | src/main/java/org/springframework/samples/petclinic/vet/Vet.java | relação ManyToMany, getSpecialties, getNrOfSpecialties e addSpecialty |
| V04 | src/main/java/org/springframework/samples/petclinic/vet/Specialty.java | entidade e herança de NamedEntity |
| V05 | src/main/java/org/springframework/samples/petclinic/vet/Vets.java | envelope JAXB e inicialização da lista |
| V06 | src/main/java/org/springframework/samples/petclinic/model/Person.java; src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java; src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java | nomes, campos e identidade herdados |
| V07 | src/main/resources/templates/vets/vetList.html | nomes, especialidades, ausência e links de paginação |
| V08 | src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java | caching e customizador JCache |
| V09 | src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java | expectativas MVC de HTML, redirect e JSON |
| V10 | src/test/java/org/springframework/samples/petclinic/vet/VetTests.java | serialização Java de firstName, lastName e id, sem especialidades configuradas |
| V11 | src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java | shouldFindVets espera Douglas e especialidades dentistry/surgery |

🟢 CONFIRMADO — Os três schemas em src/main/resources/db/{h2,mysql,postgres}/schema.sql definem vets, specialties e vet_specialties. Unicidade do par está explícita em MySQL/PostgreSQL e não em H2; Set Java não deve ser confundido com constraint SQL universal. Ver [dicionário de dados](../data-dictionary.md) e [ERD](../erd-complete.md).
