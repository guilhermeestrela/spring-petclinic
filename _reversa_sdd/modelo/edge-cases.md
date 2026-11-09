# Modelo compartilhado — Casos de borda

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e evidência

🟢 CONFIRMADO — Catálogo complementar a [requirements.md](requirements.md), [design.md](design.md) e [tasks.md](tasks.md). São casos de classes/mapeamento, sem endpoint próprio. Nenhum teste de aplicação, Validator, ORM ou serialização foi executado nesta etapa.

🟢 CONFIRMADO — 🟢 identifica código/declaração; 🟡 hipótese ou proposta; 🔴 resultado ou decisão ainda não comprovados. 🟡 Verificações abaixo são tarefas futuras proporcionais ao risco, não mudanças autorizadas nas classes.

## Catálogo

🟢 CONFIRMADO — Caminhos abreviados de origem são relativos a src/main/java/org/springframework/samples/petclinic/.

| ID | Caso | Comportamento e limite | Verificação proposta | Origem | Rastreabilidade |
|---|---|---|---|---|---|
| MOD-EC-01 | ID null, zero ou negativo | 🟢 isNew é verdadeiro apenas para null; zero e negativos retornam falso. Não há verificação de existência no banco. | Validar em memória separadamente de persistência; não usar isNew como autorização ou prova de registro existente. | model/BaseEntity.java | MOD-RF-01; MOD-CA-01/02/03; TT-01 |
| MOD-EC-02 | Troca de ID ou retorno a null | 🟢 setId atribui diretamente; trocar ID altera objeto em memória e null volta a isNew=true. 🔴 Efeito de salvar objeto com ID reatribuído depende do contexto JPA e destino. | Caracterizar em base isolada se importação/reatribuição for necessária; não interpretar null como comando delete. | model/BaseEntity.java | MOD-INV-01; TT-04; TM-01 |
| MOD-EC-03 | Nome null versus vazio | 🟢 toString retorna <null> somente para null; vazio permanece vazio. NotBlank é declaração separada. | Testar getter/toString e Bean Validator separadamente; fallback textual não torna o objeto válido. | model/NamedEntity.java | MOD-RF-02; MOD-CA-05/06; TT-02/03 |
| MOD-EC-04 | Espaços de borda ou nome só de whitespace | 🟢 Setters não aplicam trim. toString retorna conteúdo não null. NotBlank restringe texto quando validação é invocada. | Usar valores com texto e espaços, vazio e whitespace; não introduzir normalização como detalhe neutro. | model/NamedEntity.java | MOD-RF-02/03; TT-02/03 |
| MOD-EC-05 | Pessoa com nomes de 30 ou 31 letras | 🟢 Person declara Size(max=30) e Column(length=30) em ambos os campos. Setters não rejeitam 31 imediatamente. | Invocar Bean Validator para limites; distinguir atribuição, validação e restrição efetiva da coluna SQL. | model/Person.java | MOD-RF-03; MOD-CA-07/09; TT-02/04 |
| MOD-EC-06 | Size válido mas texto ausente | 🟢 NotBlank e Size coexistem em Person; conformidade com tamanho não substitui obrigatoriedade. | Validar null/vazio/whitespace com o outro campo válido, sem assumir uma única violação em todo valor/cenário. | model/Person.java | MOD-RF-03; MOD-CA-08; TT-02 |
| MOD-EC-07 | Nome longo de NamedEntity | 🟢 Não há Size em NamedEntity; PetValidator impõe limite específico de Pet e schemas têm limites distintos. | Não copiar regra 30 de Person/Pet para todos os nomes; caracterizar consumidor e perfil separadamente. | model/NamedEntity.java | MOD-RF-02; TT-02/04 |
| MOD-EC-08 | Dado inválido atribuído sem validação | 🟢 Setters armazenam os valores sem validação imperativa; restrições precisam de contexto que invoque Bean Validation. | Validar setters separadamente de Validator/MVC/JPA; não prometer rejeição universal de null pelo simples uso da classe. | model/Person.java | MOD-RN-07; TT-02 |
| MOD-EC-09 | Mesmo ID em instâncias distintas | 🟢 Não há equals/hashCode por ID/nome nas classes base; atribuir mesmo ID não cria regra própria de igualdade. | Avaliar impacto em collections de consumidores sem introduzir equals/hashCode silenciosamente; regras próprias de Owner.addPet são distintas. | model/BaseEntity.java | MOD-RF-04; TT-01; consumidores owner/vet |
| MOD-EC-10 | Nomes com Unicode ou composição visual equivalente | 🟢 Não há normalização Unicode nem contagem explícita de grafemas no código. 🔴 Efeitos de validação/coluna por representação e perfil não caracterizados. | Usar exemplos relevantes ao domínio apenas se necessário; registrar comprimento/representação sem redefinir o contrato de tamanho por aparência visual. | model/Person.java | MOD-RF-02/03; TT-02/04 |
| MOD-EC-11 | Herança interpretada como tabela própria | 🟢 BaseEntity/Person/NamedEntity são MappedSuperclass, não Entity; schemas não definem tabelas próprias para elas. | Conferir mapeamento de consumidores e SQL real; não gerar tabelas base nem estratégia de herança de entidades por suposição. | model/BaseEntity.java | MOD-RF-04; MOD-CA-10; TT-04 |
| MOD-EC-12 | Leitura de bytes serializados de outra versão | 🟢 Serializable é implementado e não há serialVersionUID explícito. 🔴 Compatibilidade entre releases não foi definida nem executada. | Identificar necessidade, classes/versões e amostras autorizadas antes de prometer compatibilidade; teste atual de round trip de Vet não resolve histórico. | model/BaseEntity.java | MOD-INV-01; TT-05; TM-02 |
| MOD-EC-13 | Serialização nativa versus Java e HTTP | 🟢 RuntimeHints registra JavaSerialization para BaseEntity/Person/Vet; isso não é shape JSON/XML nem prova de build nativo aprovado. | Validar artefato/runtime relevante se necessário; não interpretar ausência de NamedEntity no registro explícito como falha já confirmada. | PetClinicRuntimeHints.java | MOD-RF-04; MOD-INV-01; TT-05 |
| MOD-EC-14 | Nome obrigatório em Java, nullable no banco | 🟢 NotBlank não implica nullable=false declarado na Column; aplicação usa schemas próprios e ddl-auto=none. 🔴 Dados existentes podem exigir avaliação no destino. | Comparar dados e DDL por perfil; não sanear nulls nem alterar schema nesta extração. | model/Person.java | TT-04; TM-01; dados compartilhados |
| MOD-EC-15 | Ausência de Version interpretada como política de concorrência | 🟢 Classes não têm Version ou controle local de revisão. 🟡 Consequência depende de transações e operações consumidoras; perda de atualização não foi demonstrada. | Avaliar concorrência nos fluxos afetados quando houver mudança; não adicionar Version como ajuste automático de fidelidade. | model/BaseEntity.java | MOD-RF-04; integração com consumidores |

## Cenários de referência

### Identidade — MOD-EC-01/02

🟢 CONFIRMADO — Cenário do contrato em memória, não uma operação de banco.

```gherkin
Dado uma BaseEntity com ID 0
Quando isNew é consultado e depois setId recebe null
Então isNew é falso antes e verdadeiro depois da atribuição
```

### Texto e validação — MOD-EC-03

🟢 CONFIRMADO — toString e NotBlank têm propósitos separados.

```gherkin
Dado uma NamedEntity com name null
Quando toString é chamado e o objeto é submetido ao Bean Validator
Então a representação textual é <null> e a restrição NotBlank de name é avaliada como inválida
```

### Limite sem rejeição no setter — MOD-EC-05

🟢 CONFIRMADO — O setter não lança erro próprio pelo limite.

```gherkin
Dado uma Person com sobrenome válido
Quando firstName recebe 31 letras e o Bean Validator é invocado
Então o valor atribuído permanece disponível no getter e há violação Size em firstName
```

### Compatibilidade histórica — MOD-EC-12

🔴 MOD-INV-01 — Cenário coleta evidência, sem antecipar êxito de deserialização.

```gherkin
Dado que compatibilidade com uma versão anterior é um requisito confirmado e existem amostras autorizadas dessa versão
Quando a leitura dessas amostras é caracterizada com as classes do destino
Então registrar versões, resultado e incompatibilidades antes de definir contrato de migração
```

## Evidências compartilhadas

- 🟢 src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java: dois testes de nomes vazios, path e mensagem inglesa; não equivalem a toda a matriz de limites.
- 🟢 src/test/java/org/springframework/samples/petclinic/vet/VetTests.java: round trip de Vet com ID/nome/sobrenome; sem especialidades preenchidas, versões antigas ou HTTP.
- 🟢 src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java: tamanho máximo 30 específico do formulário Pet.
- 🟢 src/main/resources/db/h2/schema.sql, db/mysql/schema.sql e db/postgres/schema.sql: diferenças de tipo/limite/nulabilidade dos consumidores.
- 🟢 src/main/resources/application.properties: ddl-auto=none; anotações Java não são fonte única de DDL.

## Lacunas e ações posteriores

| Item | Casos | Evidência necessária |
|---|---|---|
| 🔴 MOD-INV-01 — identidade | MOD-EC-02/14 | Destino, política de importação/remapeamento e comportamento JPA por perfil |
| 🔴 MOD-INV-01 — serialização | MOD-EC-12/13 | Necessidade real, versões, bytes de amostra autorizados e artefato de execução |
| 🟡 Compatibilidade de coleções | MOD-EC-09 | Caracterização de consumidores caso identidade/igualdade seja alterada |
| 🟡 Limites por contexto | MOD-EC-05/07/10/14 | Validator/DDL e dados reais relevantes, sem normalização inventada |
| 🟡 Concorrência | MOD-EC-15 | Evidência por fluxo antes de atribuir consequências à ausência de Version |

🟢 CONFIRMADO — O plano desta unit não inclui questions.md próprio; investigações técnicas ficam registradas aqui e em tasks.md e poderão ser consolidadas na revisão global. 🟡 Se surgir decisão de produto/destino necessária, explicitá-la sem converter comportamento desconhecido em confirmado.
