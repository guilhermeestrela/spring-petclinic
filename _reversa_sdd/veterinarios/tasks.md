# Veterinários — Tarefas de Implementação

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


🟡 INFERIDO — Roteiro proposto de reimplementação fiel à unit vet, baseado em [requirements.md](requirements.md) e [design.md](design.md). A divisão e a ordem de tarefas são propostas; confiança de comportamento indica sua origem no código. Nenhuma tarefa de código, teste de aplicação ou migração foi executada nesta extração.

## Pré-requisitos

- [ ] 🟢 Contratos de modelo disponíveis: BaseEntity, Person e NamedEntity, em src/main/java/org/springframework/samples/petclinic/model/. Pronto: IDs, nomes e herança atendem T-01.
- [ ] 🟢 Dependências MVC/JPA/Thymeleaf/cache declaradas em pom.xml e build.gradle disponíveis. Pronto: componentes resolvidos no ambiente de implementação; equivalência de builds não presumida.
- [ ] 🟢 Perfil/datasource de validação identificado conforme src/main/resources/application*.properties. Pronto: base e configuração registradas antes de executar testes com dados.
- [ ] 🔴 Decisões de formato, ordem, dados inconsistentes, erros e cache identificadas pelas lacunas abaixo. Pronto: separar comportamento legado observado de qualquer mudança aprovada.

## Tarefas

- [ ] **T-01 — Mapear entidades e relacionamento**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`, `src/main/java/org/springframework/samples/petclinic/vet/Specialty.java`, `src/main/java/org/springframework/samples/petclinic/model/Person.java`, `src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java`, `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`.

  Trabalho proposto: Reproduzir identidade Integer/IDENTITY e herança; Vet em vets, Specialty em specialties e ManyToMany EAGER por vet_specialties(vet_id,specialty_id), sem cascade novo.

  Critério de pronto: Campos e FKs conferidos com dicionário/ERD. Não criar vínculo com Visit ou tabela para superclasses.

  Rastreabilidade: VET-RF-04. Confiança: 🟢 comportamento extraído.

- [ ] **T-02 — Implementar coleção e apresentação de especialidades**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`.

  Trabalho proposto: Inicializar HashSet sob demanda, expor nova lista ordenada por nome, contar tamanho e adicionar Specialty sem política de normalização inventada.

  Critério de pronto: VET-CA-09 reproduzido; lista derivada não ordena o Set em posição. Duas instâncias com mesmo ID não ganham igualdade de negócio nova. Dados null são caracterizados em TT-05 antes de definir tratamento.

  Rastreabilidade: VET-RF-04. Confiança: 🟢 código confirmado; 🔴 política de dados inconsistentes pendente.

- [ ] **T-03 — Disponibilizar consultas do repositório**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`.

  Trabalho proposto: Expor findAll():Collection<Vet> e findAll(Pageable):Page<Vet>, ambos readOnly e Cacheable("vets"), com falhas de acesso não convertidas localmente.

  Critério de pronto: Contratos completo/paginado preservados; não adicionar save/delete ao contrato de leitura como requisito legado. Anotações não são apresentadas como cache de runtime comprovado.

  Rastreabilidade: VET-RF-01; VET-RF-03. Confiança: 🟢 declarações confirmadas.

- [ ] **T-04 — Implementar HTML e faixa de página**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`.

  Trabalho proposto: Receber page padrão 1; consultar PageRequest(max(page,1)-1,5) sem Sort; depois validar page contra max(totalPages,1). Fora da faixa redirecionar page=1; demais casos preencher currentPage/totalPages/totalItems/listVets e retornar vets/vetList.

  Critério de pronto: VET-CA-01 a VET-CA-05 validados; página 1 vazia não ganha erro notFound; falha de consulta não vira redirect presumido.

  Rastreabilidade: VET-RF-01; VET-RF-02. Confiança: 🟢 comportamento extraído.

- [ ] **T-05 — Implementar envelope de dados**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/Vets.java`, `src/main/java/org/springframework/samples/petclinic/vet/VetController.java`.

  Trabalho proposto: Criar Vets com lista ArrayList sob demanda; GET /vets copia todos os resultados de findAll() ao envelope e retorna ResponseBody, sem paginação própria.

  Critério de pronto: VET-CA-06/07/08 validados no nível do handler; lista vazia inicializada. Teste JSON esperado é reproduzido sem declarar XML ou shape completo ainda não comprovados.

  Rastreabilidade: VET-RF-03. Confiança: 🟢 código confirmado; 🔴 representação completa pendente.

- [ ] **T-06 — Integrar template e idioma**

  Origem no legado: `src/main/resources/templates/vets/vetList.html`, `src/main/resources/templates/fragments/layout.html`, `src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java`.

  Trabalho proposto: Exibir nome completo, especialidades e chave none quando quantidade zero; usar layout e mensagens compartilhados. Navegação aparece quando totalPages>1 com links de primeira/anterior/próxima/última e números.

  Critério de pronto: VET-CA-10 e renderização com/sem especialidades validados; nome/ordem de veterinários não recebem regra adicional sem decisão VET-D-03.

  Rastreabilidade: VET-RF-01; VET-RF-04. Confiança: 🟢 template confirmado.

- [ ] **T-07 — Integrar configuração declarada de cache**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java`, `src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java`, `pom.xml`, `build.gradle`.

  Trabalho proposto: Preservar nome vets das consultas e integração com infraestrutura existente; documentar customizador JCache com estatísticas e dependência Caffeine, distinguindo configuração declarada de provedor ativo.

  Critério de pronto: Identificar CacheManager no ambiente de validação, testar consultas completa/paginada e registrar limites/invalidação se conhecidos. Não inventar TTL/capacidade nem substituir provedor por suposição.

  Rastreabilidade: VET-RF-01; VET-RF-03. Confiança: 🟢 declarações confirmadas; 🔴 D-04 depende de evidência operacional.

## Tarefas de Teste

🟡 INFERIDO — Executar verificações relevantes ao contexto de implementação, preservando asserções legadas úteis e evitando testes que apenas espelhem detalhes internos sem risco comportamental.

- [ ] **TT-01 — HTML e paginação**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java`.

  Trabalho proposto: Preservar showVetListHtml e o teste de páginas 0/2; complementar com página padrão, negativa, acima do total, base vazia e captura de Pageable/model.

  Critério de pronto: VET-CA-01/02/04/05 validados; tamanho 5 e ordem consulta→faixa explicitamente verificados. Não limitar-se a assert de view.

  Confiança: 🟢 testes localizados; 🟡 expansão proposta.

- [ ] **TT-02 — Representação do envelope**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java`.

  Trabalho proposto: Preservar Accept JSON com expectativa 200/application/json/$.vetList[0].id; caracterizar catálogo vazio e conteúdo completo necessário, além de outros Accept apenas se fizerem parte do contrato a preservar.

  Critério de pronto: VET-CA-06/07 e formato registrado por evidência. XML não é aprovado apenas por JAXB; VET-D-01 recebe decisão/evidência.

  Confiança: 🟢 expectativa de JSON confirmada; 🔴 formatos completos pendentes.

- [ ] **TT-03 — Especialidades e identidade serializada**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/vet/VetTests.java`.

  Trabalho proposto: Preservar serialization de id/firstName/lastName; complementar com lista ordenada, conjunto vazio, contagem, mesma instância repetida e isolamento da lista retornada.

  Critério de pronto: VET-CA-09 e semântica de coleção verificadas. Serialização Java sem especialidades não é tratada como prova de payload JSON/XML.

  Confiança: 🟢 teste legado confirmado; 🟡 casos adicionais propostos.

- [ ] **TT-04 — Persistência de associações**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java`.

  Trabalho proposto: Usar shouldFindVets como evidência inicial de Douglas e dentistry/surgery; validar join e leitura nos perfis requeridos em base isolada. Diferenciar contexto JPA de teste com mock.

  Critério de pronto: Vínculos e nomes esperados no perfil registrado; equivalência entre bancos só declarada se executada. Não alterar seed para simular regra de domínio fechada.

  Confiança: 🟢 cenário JPA existente; 🟡 validação multi-perfil proposta.

- [ ] **TT-05 — Falhas e dados inconsistentes**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java`.

  Trabalho proposto: Caracterizar DataAccessException em consultas, page não numérico/overflow e coleção com Specialty/name null, inclusive múltiplos itens que forcem comparação. Inspecionar runtime, sem escolher status novo por inferência.

  Critério de pronto: VET-CA-03/08 e VET-INV-01 registrados; política de erro/tolerância separada do efeito atual.

  Confiança: 🔴 contratos de falha pendentes; 🟡 caracterização proposta.

- [ ] **TT-06 — Cache e apresentação real**

  Origem no legado: `src/main/resources/templates/vets/vetList.html`.

  Trabalho proposto: Verificar template com zero/uma/várias especialidades, navegação e idioma; em infraestrutura integrada identificar cache ativo e observar consultas completa/paginada e atualização externa.

  Critério de pronto: VET-CA-10 verificado em renderização; registrar provedor, hit/miss/invalidação quando observáveis. Mock de repository não encerra D-04.

  Confiança: 🟢 fontes confirmadas; 🔴 runtime pendente.

## Tarefas de Migração de Dados (se aplicável)

🟢 CONFIRMADO — Não há destino de migração escolhido nesta extração. Esta seção não autoriza carga de dados ou mudança de schema.

- [ ] **TM-01 — Mapear catálogo e vínculos, somente se houver importação.**

  Origem no legado: `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql`, `src/main/resources/db/postgres/schema.sql` e seus data.sql; Vet.java e Specialty.java.

  Trabalho proposto: 🟡 inventariar vets, specialties e vet_specialties, IDs e vínculos; detectar órfãos, pares repetidos e nomes null antes de definir conversão. Carregar entidades antes das associações. Não deduplicar por nome nem criar equivalência de IDs sem decisão.

  Critério de pronto: 🔴 destino e política de inconsistências definidos; 🟡 mapa origem/destino e conferências de contagem/vínculo registrados. 🟢 MySQL/PostgreSQL declaram UNIQUE no par de junção e H2 não: essa diferença deve constar na avaliação.

  Confiança: 🟢 schemas confirmados; 🟡 preparação proposta; 🔴 migração concreta não definida.

## Ordem Sugerida

1. 🟡 T-01 → T-02/T-03: modelo e coleções antes dos handlers; iniciar TT-03/04.
2. 🟡 T-04/T-05: consultas HTML e de dados sobre repositório definido; validar TT-01/02/05.
3. 🟡 T-06: integrar renderização e navegação com sistema; validar TT-06.
4. 🟡 T-07: confirmar integração operacional com cache e repetir verificações apenas quando nova configuração ou falha justificar.
5. 🟡 TM-01: executar planejamento específico apenas se importação for necessária após definição do destino.

## Lacunas Pendentes (🔴)

| ID | Informação necessária | Itens afetados | Critério de fechamento |
|---|---|---|---|
| 🔴 D-04 | CacheManager, TTL, capacidade, frescor e invalidação | T-03/07; TT-06 | Evidência operacional e contrato de atualização, sem inventar configuração |
| 🔴 VET-D-01 | Shape e formatos suportados de GET /vets | T-05; TT-02 | Caracterização e confirmação do contrato necessário |
| 🔴 VET-D-02 | Erros de page/conversão/repository/cache | T-04/05; TT-05 | Status/conteúdo observados ou decisão nova identificada como tal |
| 🔴 VET-D-03 | Ordem desejada de veterinários | T-04/05/06; TT-01/02 | Decidir preservar ausência de Sort ou especificar mudança separada |
| 🔴 VET-INV-01 | Política para especialidade/nome null | T-01/02; TT-05; TM-01 | Caracterização seguida de decisão de produto/dados |
| 🔴 D-03 | Proteção externa de acesso | Integração da unit | Confirmar controles reais; nenhum papel é inferido de IDs de entidade |

## Rastreabilidade resumida

| Requisito | Implementação proposta | Validação proposta |
|---|---|---|
| 🟢 VET-RF-01 | T-03/04/06/07 | TT-01/04/06 |
| 🟢 VET-RF-02 | T-04 | TT-01/05 |
| 🟢 VET-RF-03 | T-03/05/07 | TT-02/05/06 |
| 🟢 VET-RF-04 | T-01/02/06 | TT-03/04/05/06 |

🟢 CONFIRMADO — IDs de requisito/cenários referem-se a requirements.md. 🟡 Ligações às tarefas são propostas de realização. Todas as caixas permanecem abertas: documento concluído não significa unit implementada ou suite aprovada.
