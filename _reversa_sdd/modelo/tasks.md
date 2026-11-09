# Modelo compartilhado — Tarefas de Implementação

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


🟡 INFERIDO — Plano proposto para reimplementar os contratos de [requirements.md](requirements.md) e [design.md](design.md). Confiança indica origem do comportamento; decomposição/ordem são propostas. Nenhuma classe de produção, teste ou migração foi alterada/executada nesta extração.

## Pré-requisitos

- [ ] 🟢 Dependências Jakarta Persistence e Validation disponíveis conforme pom.xml/build.gradle. Pronto: anotações e infraestrutura de validação necessárias são resolvidas.
- [ ] 🟢 Contratos dos seis consumidores identificados na matriz de impacto. Pronto: saber onde mudanças em identidade/nomes afetam responsáveis e veterinários.
- [ ] 🟢 Configuração de persistência por perfil identificada em src/main/resources/application*.properties. Pronto: não confundir Column com DDL real de aplicação que usa ddl-auto=none.
- [ ] 🔴 Se houver importação de IDs ou objetos serializados antigos, levantar destino/versões e MOD-INV-01 antes de afirmar compatibilidade. Pronto: necessidade e evidências registradas; sem isso não presumir migração.

## Tarefas

- [ ] **T-01 — Implementar identidade compartilhada**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`.

  Trabalho proposto: Reproduzir Integer id com Id/GeneratedValue IDENTITY, Serializable e MappedSuperclass; getters/setters diretos e isNew por null, sem consulta ao banco.

  Critério de pronto: MOD-CA-01/02/03 reproduzidos; ID 0/negativo não é classificado como novo, setId(null) volta a novo e não representa exclusão SQL.

  Rastreabilidade: MOD-RF-01. Confiança: 🟢 comportamento confirmado.

- [ ] **T-02 — Implementar nome e representação textual**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java`.

  Trabalho proposto: Estender BaseEntity, mapear String name com Column/NotBlank e acessores diretos; toString retorna nome não null ou literal <null>. Não adicionar Size/trim nesta classe.

  Critério de pronto: MOD-CA-04/05/06 reproduzidos; string vazia não vira fallback e setter não ganha validação imperativa.

  Rastreabilidade: MOD-RF-02. Confiança: 🟢 comportamento confirmado.

- [ ] **T-03 — Implementar nomes de pessoa**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/Person.java`.

  Trabalho proposto: Estender BaseEntity com firstName/lastName, Column length 30, Size max 30 e NotBlank; preservar atribuição sem normalização.

  Critério de pronto: MOD-CA-07/08/09 reproduzidos por Bean Validator; setter pode armazenar inválido antes da validação.

  Rastreabilidade: MOD-RF-03. Confiança: 🟢 comportamento confirmado.

- [ ] **T-04 — Integrar herança nos consumidores**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/owner/Owner.java`, `src/main/java/org/springframework/samples/petclinic/owner/Pet.java`, `src/main/java/org/springframework/samples/petclinic/owner/PetType.java`, `src/main/java/org/springframework/samples/petclinic/owner/Visit.java`, `src/main/java/org/springframework/samples/petclinic/vet/Vet.java`, `src/main/java/org/springframework/samples/petclinic/vet/Specialty.java`.

  Trabalho proposto: Manter Owner/Vet → Person, Pet/PetType/Specialty → NamedEntity e Visit → BaseEntity; não criar tabelas base ou igualdades por ID/nome.

  Critério de pronto: MOD-CA-10 validado; campos herdados e coleções consumidoras mantêm semântica documentada, sem status/Version novo.

  Rastreabilidade: MOD-RF-04. Confiança: 🟢 estrutura confirmada; 🟡 sequência de integração proposta.

- [ ] **T-05 — Preservar integração de serialização quando necessária**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`, `src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java`.

  Trabalho proposto: Manter capacidade Serializable e registros withJavaSerialization para BaseEntity/Person/Vet na integração de sistema. Distinguir round trip atual de compatibilidade com bytes antigos.

  Critério de pronto: Teste legado VetTests.serialization preservado; requisitos de compatibilidade entre releases somente assumidos se MOD-INV-01 for resolvida.

  Rastreabilidade: MOD-RF-04. Confiança: 🟢 declarações confirmadas; 🔴 compatibilidade histórica pendente.

## Tarefas de Teste

🟡 INFERIDO — Escolher verificações proporcionais ao risco de reimplementação; não criar testes redundantes para repetir setters. Contagem de métodos existentes não mede cobertura.

- [ ] **TT-01 — Identidade e semântica de coleções**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`.

  Trabalho proposto: Verificar null, ID positivo/zero/negativo e retorno a null; se houver mudança de representação, verificar repercussão em deduplicação de Pet/Visit/Specialty nos consumidores.

  Critério de pronto: MOD-CA-01/02/03 e invariantes de consumidores preservados; evitar testes de cada getter sem risco associado.

  Confiança: 🟢 regras confirmadas; 🟡 verificação de compatibilidade proposta.

- [ ] **TT-02 — Validação declarativa de nomes**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java`.

  Trabalho proposto: Preservar os dois testes de NotBlank com path/mensagem inglesa. Complementar fronteiras 30/31, null e whitespace de Person/NamedEntity quando necessários à equivalência, invocando Bean Validator.

  Critério de pronto: MOD-CA-06/07/08/09 validados; ausência de exceção no setter não confundida com validade do objeto.

  Confiança: 🟢 testes/restrições confirmados; 🟡 extensão proposta.

- [ ] **TT-03 — Fallback textual e consumidores**

  Origem no legado: `src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java`.

  Trabalho proposto: Validar toString com null, vazio, whitespace e nome normal, incluindo dependências de apresentação/formatter quando afetadas.

  Critério de pronto: MOD-CA-04/05 e uso nos consumidores mantidos; não transformar fallback em normalização persistente.

  Confiança: 🟢 comportamento confirmado; 🟡 verificações propostas.

- [ ] **TT-04 — Herança e mapeamento**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java`.

  Trabalho proposto: Conferir metamodelo/schema das entidades consumidoras e testes JPA relevantes em ambiente isolado. Não criar entidades/tabelas para as três superclasses.

  Critério de pronto: MOD-CA-10 validado; geração de IDs observada no perfil registrado. Não declarar equivalência multi-banco sem execução.

  Confiança: 🟢 fontes confirmadas; 🔴 geração por destino pendente.

- [ ] **TT-05 — Serialização e empacotamento**

  Origem no legado: `src/test/java/org/springframework/samples/petclinic/vet/VetTests.java`.

  Trabalho proposto: Preservar round trip de Vet id/nome/sobrenome; se suporte nativo ou dados serializados históricos forem necessários, executar caracterização específica com versões/artefato identificados.

  Critério de pronto: Expectativa do teste atual documentada e executada no contexto apropriado; resultado histórico/nativo só declarado se comprovado. Não equiparar Serializable a contrato JSON/XML.

  Confiança: 🟢 teste legado confirmado; 🔴 MOD-INV-01 pendente.

## Tarefas de Migração de Dados (se aplicável)

🟢 CONFIRMADO — Esta unit não possui tabelas próprias e não há migração escolhida. As tarefas condicionais abaixo não autorizam execução contra bases existentes.

- [ ] **TM-01 — Avaliar preservação/remapeamento de IDs, se houver importação.**

  Origem: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java`, schemas em `src/main/resources/db/h2/schema.sql`, `src/main/resources/db/mysql/schema.sql` e `src/main/resources/db/postgres/schema.sql`.

  Trabalho proposto: 🟡 planejar preservação ou remapeamento de IDs e FKs nas entidades consumidoras; considerar identidade gerada e sequência do destino, sem alterar setId/isNew silenciosamente.

  Critério de pronto: 🔴 destino/política definidos; 🟡 mapa de IDs, vínculos e validação de contagens aprovado. Não executar carga/DDL nem ajustar sequências nesta extração.

  Confiança: 🟢 origem confirmada; 🟡 preparação proposta; 🔴 MOD-INV-01 sem definição de destino.

- [ ] **TM-02 — Avaliar bytes Java antigos, somente se existirem consumidores.**

  Origem: `src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java` e `src/test/java/org/springframework/samples/petclinic/vet/VetTests.java`.

  Trabalho proposto: 🟡 levantar classes/versões e amostras autorizadas de dados serializados, se tal compatibilidade for requisito. Não introduzir serialVersionUID arbitrário como garantia de leitura histórica.

  Critério de pronto: 🔴 necessidade de compatibilidade definida e testes com versões conhecidas, ou registro explícito de que essa migração não se aplica.

  Confiança: 🟢 Serializable e teste de round trip confirmados; 🔴 compatibilidade entre releases desconhecida.

## Ordem Sugerida

1. 🟡 T-01 antes de T-02/T-03: BaseEntity sustenta identidade/herança.
2. 🟡 T-02/T-03 antes de T-04: contratos de campos e validação antes da integração com entidades.
3. 🟡 TT-01/02/03 acompanham mudanças que afetem comportamento; TT-04 valida integração JPA.
4. 🟡 T-05/TT-05 quando integração serializável/nativa for requerida pelo contexto.
5. 🟡 TM-01/TM-02 apenas se cenário de migração correspondente existir; não exigir importação só por haver Serializable ou setId.

## Lacunas Pendentes (🔴)

| Item | Evidência/decisão necessária | Afeta |
|---|---|---|
| 🔴 MOD-INV-01: identidade | Destino, política de IDs e geração real em persistência | T-01/04; TT-04; TM-01 |
| 🔴 MOD-INV-01: serialização | Necessidade de ler objetos antigos, versões e bytes de teste autorizados | T-05; TT-05; TM-02 |
| 🔴 Integração por perfil/artefato | Evidência de schema, validação e suporte nativo quando necessários | TT-04/05 |

🟢 CONFIRMADO — Ausência de Version, normalização e igualdade por negócio são características observadas, não lacunas a preencher automaticamente. 🟡 Alterar essas políticas precisa de escopo de evolução próprio por afetar consumidores.

## Rastreabilidade resumida

| Requisito | Tarefas propostas | Verificação proposta |
|---|---|---|
| 🟢 MOD-RF-01 | T-01 | TT-01/04 |
| 🟢 MOD-RF-02 | T-02 | TT-02/03 |
| 🟢 MOD-RF-03 | T-03 | TT-02 |
| 🟢 MOD-RF-04 | T-04/05 | TT-04/05 |

🟡 INFERIDO — Associação a tarefas é plano de realização, não implementação concluída. Todas as caixas permanecem abertas até execução futura no contexto autorizado.
