# Responsáveis — Contratos HTTP e de formulário

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


## Escopo e nível de evidência

🟢 CONFIRMADO — Contratos extraídos dos três controllers do pacote owner e dos templates de owners/pets. Este documento complementa [requirements.md](requirements.md) e [design.md](design.md), cobrindo RESP-RF-01 a RESP-RF-09. Nomes de views, caminhos de redirect, tipos e rejeições são declarações de código; não são respostas capturadas de uma execução.

🟢 CONFIRMADO — Os controllers usam @Controller, sem @RequestBody/@ResponseBody nos handlers deste módulo. Recebem parâmetros de formulário/query por binding e devolvem views HTML ou redirect; não há DTO JSON, envelope JSON de erro ou API de catálogo de tipos exposta separadamente aqui.

🟡 INFERIDO — O navegador envia os formulários POST convencionais como application/x-www-form-urlencoded e recebe HTML nas views. Os mappings não declaram consumes/produces; portanto isso descreve o fluxo dos templates, não uma restrição explícita de media type no servidor. Charset, headers e Content-Type efetivamente negociados não foram capturados.

🟡 INFERIDO — Caminhos normais de view correspondem a 200 e redirects a 3xx pelo MVC. 🔴 LACUNA — Código exato de redirect, Location final, falhas de conversão e status de exceções exigem caracterização. Nenhum handler deste módulo declara 201/400/404/409 de negócio; não transformar IllegalArgumentException em contrato 404 presumido.

## Operações

🟢 CONFIRMADO — Model e efeitos abaixo são derivados do código. Referências Java são relativas a `src/main/java/org/springframework/samples/petclinic/owner/`. Falha de persistência/renderização pode impedir qualquer retorno normal descrito.

| ID | Método | Caminho | Entradas | Model relevante | Retorno do handler | Efeito / erro local | Origem |
|---|---|---|---|---|---|---|---|
| 🟢 HTTP-01 | GET | /owners/new | nenhum | owner novo | owners/createOrUpdateOwnerForm | Nenhuma escrita; falhas de infraestrutura não tratadas localmente. | OwnerController.java:73 |
| 🟢 HTTP-02 | POST | /owners/new | firstName, lastName, address, city, telephone | owner e BindingResult | válido: redirect:/owners/{id gerado}; inválido: owners/createOrUpdateOwnerForm | save(owner) apenas sem erros; flash message em sucesso e error no ramo inválido. | OwnerController.java:78 |
| 🟢 HTTP-03 | GET | /owners/find | nenhum | owner novo | owners/findOwners | Nenhuma escrita. | OwnerController.java:90 |
| 🟢 HTTP-04 | GET | /owners | lastName opcional; page:int padrão 1 | owner; na lista: currentPage, totalPages, totalItems, listOwners | owners/findOwners, owners/ownersList ou redirect conforme regras abaixo | Consulta por prefixo; não grava. | OwnerController.java:95 |
| 🟢 HTTP-05 | GET | /owners/{ownerId} | ownerId:int na rota | owner carregado | ModelAndView owners/ownerDetails | Owner ausente lança IllegalArgumentException; nenhuma escrita. | OwnerController.java:179 |
| 🟢 HTTP-06 | GET | /owners/{ownerId}/edit | ownerId:int na rota | owner carregado | owners/createOrUpdateOwnerForm | Owner ausente lança IllegalArgumentException; nenhuma escrita. | OwnerController.java:149 |
| 🟢 HTTP-07 | POST | /owners/{ownerId}/edit | ownerId da rota e campos pessoais | owner e BindingResult | válido: redirect:/owners/{ownerId}; erro: formulário; mismatch: redirect:/owners/{ownerId}/edit | save somente após validação e concordância do ID carregado. | OwnerController.java:154 |
| 🟢 HTTP-08 | GET | /owners/{ownerId}/pets/new | ownerId:int na rota | owner, pet novo, types | pets/createOrUpdatePetForm | Adição em memória de outro Pet no owner pelo handler; não chama save. | PetController.java:101 |
| 🟢 HTTP-09 | POST | /owners/{ownerId}/pets/new | ownerId da rota; name, birthDate, type | owner, pet, types e BindingResult | válido: redirect:/owners/{ownerId}; erro: pets/createOrUpdatePetForm | saveAndFlush após validação; duplicidade reconhecida retorna formulário. | PetController.java:108 |
| 🟢 HTTP-10 | GET | /owners/{ownerId}/pets/{petId}/edit | ownerId:int, petId:Integer na rota | owner, pet buscado no owner, types | pets/createOrUpdatePetForm | findPet pode retornar null; resultado HTTP nessa condição não comprovado. | PetController.java:140 |
| 🟢 HTTP-11 | POST | /owners/{ownerId}/pets/{petId}/edit | IDs da rota; name, birthDate, type | owner, pet, types e BindingResult | válido: redirect:/owners/{ownerId}; erro: pets/createOrUpdatePetForm | Atualiza pet existente por ID e saveAndFlush; exige ID não nulo no método interno. | PetController.java:145 |
| 🟢 HTTP-12 | GET | /owners/{ownerId}/pets/{petId}/visits/new | ownerId:int, petId:int na rota | owner, pet, visit com amanhã, minVisitDate | pets/createOrUpdateVisitForm | Anexa nova visita em memória; não chama save. Vínculo inválido lança exceção. | VisitController.java:91 |
| 🟢 HTTP-13 | POST | /owners/{ownerId}/pets/{petId}/visits/new | IDs da rota; date, description | owner, pet, visit, minVisitDate e BindingResult | válido: redirect:/owners/{ownerId}; erro: pets/createOrUpdateVisitForm | owner.addVisit e save(owner) apenas sem erros; vínculo é validado no carregamento. | VisitController.java:98 |

## Dados de entrada

### Identificadores e busca

| Campo | Local / tipo | Contrato observado | Fonte |
|---|---|---|---|
| 🟢 ownerId | rota / int ou Integer no model loader | Identifica responsável a carregar; não há restrição positiva declarada por anotação. Ausência no cadastro produz objeto novo. | OwnerController.findOwner; PetController.findOwner/findPet; VisitController.loadPetWithVisit |
| 🟢 petId | rota / int ou Integer | Busca apenas na coleção do responsável; Integer ausente permite criar Pet nas rotas de criação. | PetController.findPet; VisitController.loadPetWithVisit |
| 🟢 page | query / int | @RequestParam(defaultValue="1"); consulta interna usa max(page,1)-1 e tamanho 5, sem Sort explícito. | OwnerController.processFindForm/findPaginatedForOwnersLastName |
| 🟢 lastName na busca | query / String do objeto Owner | null torna-se string vazia; outros valores usam strip; busca por prefixo; string vazia amplia consulta. Não há @Valid no parâmetro Owner desse handler. | OwnerController.processFindForm |

🔴 LACUNA — Não foi caracterizado o resultado HTTP de IDs/page não numéricos, fora de Integer ou com parâmetros repetidos. O tipo Java não prova política de validação ou mensagem de erro do framework.

### Cadastro e edição de responsável

| Campo de formulário | Tipo | Validação observada |
|---|---|---|
| 🟢 firstName | String | @NotBlank, @Size(max=30) herdados de Person |
| 🟢 lastName | String | @NotBlank, @Size(max=30) herdados de Person |
| 🟢 address | String | @NotBlank, @Size(max=255) |
| 🟢 city | String | @NotBlank, @Size(max=80) |
| 🟢 telephone | String | @NotBlank e padrão de exatamente 10 dígitos; não é número com formatação/DDD livre |

🟢 CONFIRMADO — Fontes: model/Person.java, owner/Owner.java e templates/owners/createOrUpdateOwnerForm.html. Não há trim geral dos campos. POST de criação/edição usa @Valid Owner e BindingResult. IDs carregados não devem ser substituídos por campo id do cliente.

### Cadastro e edição de animal

| Campo | Tipo de entrada → objeto | Validação/conversão |
|---|---|---|
| 🟢 name | texto → String | hasText; máximo 30 caracteres; sem strip. Duplicidade no mesmo responsável usa equalsIgnoreCase. |
| 🟢 birthDate | yyyy-MM-dd → LocalDate | requerido por PetValidator; futuro rejeitado no controller; hoje é aceito pela regra temporal. |
| 🟢 type | nome textual → PetType | Formatter procura Objects.equals(name,text); comparação sensível a caixa; novo exige tipo não nulo. |
| 🟢 id oculto no template | texto de ID | Está presente no HTML, mas é bloqueado pelo binder pet; edição usa objeto carregado da rota. |

🟢 CONFIRMADO — Fontes: PetValidator.java, PetController.java, PetTypeFormatter.java, Pet.java, templates/pets/createOrUpdatePetForm.html e templates/fragments/selectField.html. Catálogo é consultado via PetTypeRepository.findPetTypes, ordenado por nome; print retorna nome ou literal <null>. Não há contrato de envio de type_id por esse select.

🟢 CONFIRMADO — Criação rejeita nome que já exista em animal persistido do responsável; edição permite nome do próprio ID e rejeita nome do primeiro animal encontrado com outro ID. PetValidator exige tipo apenas quando pet.isNew; o DDL exige type_id não nulo também para persistidos. 🔴 LACUNA — A falha real de tipo vazio em edição pode ocorrer em camada diferente e não foi caracterizada.

### Nova visita

| Campo | Tipo | Validação/estado observado |
|---|---|---|
| 🟢 date | yyyy-MM-dd → LocalDate | Nova Visit inicia amanhã; se valor não nulo for hoje/passado, rejeita com typeMismatch.visitDate. |
| 🟢 description | String | @NotBlank e @Size(max=255). |
| 🟢 petId oculto no template | texto | O parâmetro petId do handler é @PathVariable: a rota determina a associação usada; o hidden não é fonte dessa anotação. |

🟢 CONFIRMADO — Fontes: Visit.java, VisitController.java e templates/pets/createOrUpdateVisitForm.html. minVisitDate é um atributo do model com amanhã, consumido pelo min do input date; não é um campo de negócio persistido.

🔴 D-01 — Date não tem @NotNull; condicional manual ignora null. Parâmetro omitido e string vazia podem interagir de modo diferente com o valor inicial amanhã. Não especificar obrigatório/nullable no contrato externo como decisão resolvida sem caracterização e validação humana.

## Respostas, model e redirects

### Busca de responsáveis — HTTP-04

🟢 CONFIRMADO — A ordem de decisão é parte do contrato observado:

1. Consultar prefixo normalizado com página interna segura e tamanho 5.
2. Se page < 1 ou page > max(totalPages,1), declarar redirect:/owners com atributo page=1 e lastName normalizado apenas se não vazio.
3. Em página válida, se Page.isEmpty, rejeitar lastName/notFound e retornar owners/findOwners.
4. Se getTotalElements == 1, retornar redirect:/owners/ concatenado ao ID único.
5. Caso contrário, retornar owners/ownersList com currentPage:int, totalPages:int, totalItems:long e listOwners:List<Owner>.

🟢 CONFIRMADO — Fonte: OwnerController.java:95/132/141. O redirect do item 2 preserva filtro, mas os links da lista montam apenas /owners?page=... no template. 🔴 RESP-D-07 — Confirmar intenção dessa diferença antes de mudar o contrato de navegação.

### Views e seus dados

| View | Dados usados pelo HTML | Fonte |
|---|---|---|
| 🟢 owners/createOrUpdateOwnerForm | owner, campos pessoais e erros de binding | templates/owners/createOrUpdateOwnerForm.html |
| 🟢 owners/findOwners | owner.lastName e erros de busca | templates/owners/findOwners.html |
| 🟢 owners/ownersList | listOwners e atributos de paginação | templates/owners/ownersList.html |
| 🟢 owners/ownerDetails | owner, pets, visits e message/error quando presentes | templates/owners/ownerDetails.html |
| 🟢 pets/createOrUpdatePetForm | pet, owner, types e erros de campos | templates/pets/createOrUpdatePetForm.html |
| 🟢 pets/createOrUpdateVisitForm | visit, pet, owner, minVisitDate e histórico de visitas | templates/pets/createOrUpdateVisitForm.html; fragments/inputField.html |

🟢 CONFIRMADO — Esses objetos pertencem ao model de renderização, não a um payload JSON serializado. Pet não tem campo owner e Visit não tem campo pet no modelo Java; isso não altera os IDs de rota exigidos para carregar os vínculos.

### Feedback declarado pelos handlers

| Resultado | Atributo flash e valor literal | Destino/retorno |
|---|---|---|
| 🟢 Responsável criado | message = New Owner Created | redirect para detalhe com ID gerado |
| 🟢 Cadastro de responsável inválido | error = There was an error in creating the owner. | formulário, sem redirect |
| 🟢 Responsável atualizado | message = Owner Values Updated | redirect para detalhe |
| 🟢 Edição de responsável inválida | error = There was an error in updating the owner. | formulário, sem redirect |
| 🟢 Mismatch de ID na edição | error = Owner ID mismatch. Please try again. | redirect de edição; também rejeita id/mismatch |
| 🟢 Animal criado | message = New Pet has been Added | redirect para detalhe do responsável |
| 🟢 Animal atualizado | message = Pet details has been edited | redirect para detalhe do responsável |
| 🟢 Visita registrada | message = Your visit has been booked | redirect para detalhe do responsável |

🟢 CONFIRMADO — Fontes: processCreationForm/processUpdateOwnerForm em OwnerController, processCreationForm/processUpdateForm em PetController e processNewVisitForm em VisitController. Declarar flash no ramo sem redirect não prova exibição imediata na view do formulário. Strings são as atuais do código, mesmo com suporte global de idioma.

## Erros e limites do contrato

| Condição | Resultado local confirmado | Resultado HTTP |
|---|---|---|
| 🟢 Responsável inexistente | IllegalArgumentException no carregamento ou detalhe | 🔴 Não caracterizado; não presumir 404 |
| 🟢 Pet inexistente/fora do responsável na visita | IllegalArgumentException antes de salvar | 🔴 Não caracterizado |
| 🟢 Pet não encontrado na edição | findPet retorna null | 🔴 Binding/renderização subsequentes não caracterizados |
| 🟢 Nome Pet sem texto | rejectValue(name, required, required) | 🟡 View do formulário em fluxo de BindingResult |
| 🟢 Nome Pet maior que 30 | rejectValue(name, size, mensagem literal do validator) | 🟡 View do formulário |
| 🟢 Tipo Pet novo null ou nascimento null | rejectValue(type ou birthDate, required, required) | 🟡 View do formulário |
| 🟢 Nome Pet duplicado | rejectValue(name, duplicate, already exists) | 🟡 View do formulário, sem 409 declarado |
| 🟢 Nascimento futuro | rejectValue(birthDate, typeMismatch.birthDate) | 🟡 View do formulário |
| 🟢 Visita com data informada até hoje | rejectValue(date, typeMismatch.visitDate) | 🟡 View do formulário |
| 🟢 Busca sem resultado em página válida | rejectValue(lastName, notFound, not found) | 🟡 View de busca |
| 🟢 ID interno de Owner diverge da URL | rejectValue(id, mismatch, mensagem literal do controller) | 🟡 Redirect de edição; não prova manipulação por HTTP |
| 🟢 saveAndFlush falha com mensagem contendo unique_owner_pet_name, ignorando caixa | Captura DataIntegrityViolationException, rejeita name/duplicate, retorna formulário | 🟡 View de formulário |
| 🟢 Outra DataIntegrityViolationException ou mensagem null | Relança exceção original | 🔴 Status/conteúdo final não caracterizados |

🟢 CONFIRMADO — Fontes: controllers de owner e PetValidator.java. Bean Validation também declara NotBlank/Size/Pattern nas entidades. Mensagens renderizadas e lista completa dos códigos resolvidos pelo framework não foram capturadas; não há envelope de erro estável definido no código destes handlers.

🔴 D-02 — Reconhecimento de duplicidade depende do texto unique_owner_pet_name. H2/PostgreSQL usam esse nome explicitamente; o schema MySQL declara UNIQUE sem esse nome. Resultado real de driver/constraint e comparação de caixa precisam ser comprovados por perfil.

## Binding, acesso e repetição de requisições

- 🟢 CONFIRMADO — Binders declaram setDisallowedFields("id", "*.id"). É uma lista de bloqueio, não lista exclusiva de campos permitidos: os campos documentados são os usados pelos templates, não garantia de rejeição de qualquer outra propriedade vinculável (OwnerController.setAllowedFields; PetController.initOwnerBinder/initPetBinder; VisitController.setAllowedFields).
- 🟢 CONFIRMADO — Carregamento de visita valida petId na coleção do owner. Isso verifica vínculo de dados, sem verificação de identidade/papel do usuário nos handlers examinados. 🔴 D-03 — Políticas de acesso externas não conhecidas.
- 🟢 CONFIRMADO — Não há chave de idempotência, deduplicação por conteúdo da visita, token de versão explícito ou ETag definido nesses controllers/entidades. Duas submissões de visita não são tornadas idempotentes pela deduplicação da mesma instância em Set dentro de uma requisição.
- 🟢 CONFIRMADO — Nenhum handler de autenticação, exclusão, cancelamento, cobrança ou escolha de veterinário consta nas 13 operações deste módulo. Idioma e cache são contratos transversais de sistema.
- 🔴 D-05 — Datas usam o relógio padrão da JVM via LocalDate.now; fuso de negócio, sincronização entre chamadas e virada do dia não foram definidos explicitamente.

## Exemplos para caracterização

🟡 INFERIDO — Exemplos de requisição montados a partir dos campos do código, sem execução. IDs e tipos dependem da base; POSTs devem ser usados apenas no ambiente de teste escolhido. Não constituem autorização para executá-los contra implantação existente.

```http
GET /owners?lastName=%20Silva%20&page=1 HTTP/1.1
Accept: text/html
```

🟢 CONFIRMADO — Consulta usa Silva após strip. A view ou redirect depende da quantidade de resultados e da faixa; não há resposta fixa para esse exemplo.

```http
POST /owners/1/pets/new HTTP/1.1
Content-Type: application/x-www-form-urlencoded

name=Luna&birthDate=2020-02-12&type=cat
```

🟢 CONFIRMADO — Se owner 1 e tipo cat existem, não há nome conflitante, a data não é futura e binding/persistência não falham, o handler retorna redirect:/owners/{ownerId} com message de criação. Caso faltem essas condições, aplicar regras anteriores; código final HTTP ainda não foi observado.

```http
POST /owners/1/pets/1/visits/new HTTP/1.1
Content-Type: application/x-www-form-urlencoded

date=2030-01-15&description=Consulta
```

🟢 CONFIRMADO — Válido temporalmente somente se 2030-01-15 for posterior ao hoje da JVM no instante da validação. O animal 1 deve pertencer ao responsável 1. Não existe garantia de aceitação indefinida de uma data absoluta no futuro.

## Rastreabilidade e validação

🟢 CONFIRMADO — Fontes primárias: `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java`, `PetController.java`, `VisitController.java`, `PetValidator.java`, `PetTypeFormatter.java`, entidades Owner/Pet/Visit e superclasse Person. Templates ficam em `src/main/resources/templates/owners/`, `pets/` e `fragments/`. A matriz S01–S13 em requirements.md fornece caminhos completos.

🟢 CONFIRMADO — HTTP-01/02 → RESP-RF-01; HTTP-03/04 → RESP-RF-02; HTTP-05 → RESP-RF-03; HTTP-06/07 → RESP-RF-04; catálogo/conversão dos formulários → RESP-RF-05; HTTP-08/09 → RESP-RF-06; HTTP-10/11 → RESP-RF-07; HTTP-12/13 → RESP-RF-08; carregamento/binders → RESP-RF-09.

🟢 CONFIRMADO — Cobertura documental: 13 combinações método/caminho mapeadas nos três controllers; nenhum endpoint foi chamado para validação. A execução dos cenários e a caracterização de lacunas estão planejadas em [tasks.md](tasks.md), especialmente TT-06/07/08.
