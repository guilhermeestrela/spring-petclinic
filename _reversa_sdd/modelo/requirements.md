# Modelo compartilhado — Requisitos

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Visão Geral

🟢 CONFIRMADO — A unit model → modelo define as superclasses mapeadas BaseEntity, NamedEntity e Person, reutilizadas por responsáveis, animais, visitas, tipos, veterinários e especialidades. São contratos de classes Java, sem endpoints HTTP próprios. Fontes M01–M03 na rastreabilidade.

🟢 CONFIRMADO — Esta extração descreve declarações e métodos, sem executar validação/JPA ou testes. 🟡 Prioridades e critérios abaixo são organização proposta para reimplementar fielmente o comportamento, não tarefas já realizadas.

## Responsabilidades

- 🟢 Centralizar ID Integer, estratégia JPA IDENTITY e verificação de novo por id null (M01).
- 🟢 Compartilhar campo name, restrição NotBlank e representação textual com fallback (M02).
- 🟢 Compartilhar firstName/lastName com restrições de texto e tamanho 30 (M03).
- 🟢 Fornecer herança de mapeamento/Serializable aos consumidores, sem tabelas independentes das superclasses (M01–M03 e entidades consumidoras).

## Regras de Negócio

| ID | Comportamento extraído | Origem |
|---|---|---|
| MOD-RN-01 | 🟢 BaseEntity implementa Serializable; id é Integer, @Id e @GeneratedValue(IDENTITY). Objetos novos começam com id null. | M01 |
| MOD-RN-02 | 🟢 isNew retorna somente id == null; não consulta banco nem valida existência ou positividade de ID. | M01 |
| MOD-RN-03 | 🟢 setId aceita o valor recebido sem guarda; atribuir null torna isNew verdadeiro novamente. Valores 0 ou negativos não são tratados como novo pelo método. | M01 |
| MOD-RN-04 | 🟢 NamedEntity.name é String com @Column e @NotBlank; setter não aplica trim/normalização nem rejeita diretamente null. | M02 |
| MOD-RN-05 | 🟢 NamedEntity.toString retorna getName quando não null, incluindo string vazia; só nome null produz literal <null>. | M02 |
| MOD-RN-06 | 🟢 Person.firstName e lastName são String com @NotBlank, @Size(max=30) e @Column(length=30); setters/getters são acesso direto. | M03 |
| MOD-RN-07 | 🟢 Validações são declarativas: setters podem armazenar dados inválidos antes da aplicação de Bean Validation. Não confundir atribuição Java com submissão válida. | M02/M03 |
| MOD-RN-08 | 🟢 As três classes são @MappedSuperclass, não @Entity com tabela própria. NamedEntity e Person herdam BaseEntity; Person não herda NamedEntity. | M01–M03 |
| MOD-RN-09 | 🟢 Não há equals/hashCode por ID/nome, @Version, status de negócio ou normalização nessas classes. Regras específicas como nome de Pet até 30 estão em outros módulos. | M01–M03 |

## Requisitos Funcionais

🟢 CONFIRMADO — Contratos de classe extraídos. 🟡 Todas as prioridades Must são propostas com base nos consumidores, sem frequência medida.

| ID | Requisito | Prioridade | Critério de aceite |
|---|---|---|---|
| MOD-RF-01 | 🟢 Armazenar e expor identidade e estado técnico isNew. | Must | MOD-CA-01/02/03 |
| MOD-RF-02 | 🟢 Armazenar nome e fornecer representação textual de NamedEntity. | Must | MOD-CA-04/05/06 |
| MOD-RF-03 | 🟢 Declarar obrigatoriedade e limite de nomes de Person. | Must | MOD-CA-07/08/09 |
| MOD-RF-04 | 🟢 Compartilhar mapeamento e identidade com entidades consumidoras. | Must | MOD-CA-10; MOD-INV-01 |

## Requisitos Não Funcionais

| Tipo | Contrato declarado | Evidência | Confiança |
|---|---|---|---|
| Persistência | Identidade gerada por estratégia IDENTITY; campos herdados via MappedSuperclass | M01–M03 | 🟢 CONFIRMADO quanto às anotações; execução por banco não medida |
| Serialização Java | BaseEntity implementa Serializable, herdado pelas subclasses | M01; teste M05 | 🟢 CONFIRMADO; compatibilidade binária entre versões não definida |
| Validação | NotBlank/Size nas propriedades indicadas | M02/M03; teste M04 | 🟢 CONFIRMADO quanto à declaração e expectativas; teste não executado |

🟢 CONFIRMADO — Não há SLA, timeout, cache, autenticação ou retry implementados nesta unit. Não se introduzem requisitos quantitativos nem política de acesso a partir da existência do campo id.

## Critérios de Aceitação

🟢 CONFIRMADO — Cenários de identidade/texto são derivados dos métodos. Cenários de validação pressupõem invocação do Bean Validator, não apenas dos setters. Nenhum cenário foi executado aqui.

### Identidade — MOD-RF-01

```gherkin
Cenário MOD-CA-01: Objeto novo sem ID
Dado uma BaseEntity recém-instanciada
Quando getId e isNew são consultados
Então o ID é null e isNew é verdadeiro

Cenário MOD-CA-02: ID atribuído
Dado uma BaseEntity com ID null
Quando setId recebe 7
Então getId retorna 7 e isNew é falso sem consulta ao banco

Cenário MOD-CA-03: ID zerado ou removido
Dado uma BaseEntity cujo ID foi definido como 0
Quando isNew é consultado e depois o ID é alterado para null
Então isNew é falso antes da alteração e verdadeiro depois dela
```

### Nome compartilhado — MOD-RF-02

```gherkin
Cenário MOD-CA-04: Nome disponível
Dado uma NamedEntity com name igual a Luna
Quando getName e toString são chamados
Então ambos retornam Luna

Cenário MOD-CA-05: Nome null e nome vazio
Dado uma NamedEntity com nome null
Quando toString é chamado e depois o nome é definido como string vazia
Então o primeiro resultado é o literal <null> e a chamada posterior de toString retorna string vazia

Cenário MOD-CA-06: Nome inválido na validação
Dado uma NamedEntity com name vazio e um Bean Validator configurado
Quando o objeto é validado
Então name viola a restrição NotBlank sem exigir que setName lance exceção
```

### Nomes de pessoa — MOD-RF-03

```gherkin
Cenário MOD-CA-07: Pessoa com nomes válidos
Dado uma Person com firstName e lastName não brancos de até 30 caracteres cada
Quando o Bean Validator verifica as restrições dessas propriedades
Então não há violação NotBlank ou Size nessas duas propriedades

Cenário MOD-CA-08: Nome obrigatório ausente
Dado uma Person com firstName vazio e lastName igual a smith
Quando o Bean Validator é invocado
Então firstName viola NotBlank

Cenário MOD-CA-09: Sobrenome acima do limite
Dado uma Person com firstName válido e lastName de 31 letras
Quando o Bean Validator é invocado
Então lastName viola Size
```

### Herança — MOD-RF-04

```gherkin
Cenário MOD-CA-10: Entidades herdam campos compartilhados
Dado Owner e Vet herdando Person e Pet, PetType e Specialty herdando NamedEntity e Visit herdando BaseEntity
Quando seus contratos de propriedades herdadas são consultados
Então todos expõem id e os campos correspondentes à sua cadeia de herança sem exigir tabelas próprias de Person ou NamedEntity
```

🔴 MOD-INV-01 — Falhas de persistência de ID manual, geração efetiva e incompatibilidade de serialização dependem de ORM, banco e versões de classes. Não existe ramo local de rejeição a especificar como cenário de falha de herança. Caracterizar no ambiente alvo antes de estabelecer garantia de importação ou compatibilidade binária.

## Prioridade (MoSCoW)

| Requisito | Prioridade proposta | Justificativa |
|---|---|---|
| MOD-RF-01 | 🟡 Must | Base de identidade de todas as seis entidades JPA |
| MOD-RF-02 | 🟡 Must | Contrato compartilhado por Pet, PetType e Specialty e por conversão/apresentação |
| MOD-RF-03 | 🟡 Must | Campos compartilhados por Owner/Vet; há testes de NotBlank em Person |
| MOD-RF-04 | 🟡 Must | Sustenta mapeamento e reaproveitamento dos campos em todos os domínios |

🟢 CONFIRMADO — Não há método deprecado/comentado nesta unit que fundamente Won’t, nem prioridade Should/Could comprovada para inventar linhas adicionais.

## Limites e pontos de validação

- 🟢 Ausência de equals/hashCode próprio não autoriza inferir igualdade por ID; coleções dos módulos consumidores devem preservar sua semântica observada.
- 🟢 NamedEntity não declara Size. O limite de nome de Pet está no PetValidator; comprimentos de colunas específicas e TEXT/VARCHAR variam nos schemas. Não propagar limite 30 a todo NamedEntity por suposição.
- 🟢 Column(length=30) e Bean Validation não provam DDL efetivamente gerado, pois aplicação usa SQL próprio e ddl-auto=none. Ver schemas/dicionário.
- 🔴 MOD-INV-01: compatibilidade de serialização de objetos antigos e importação de IDs não definida. Não há serialVersionUID explícito nem migração escolhida nesta extração.
- 🟡 A ausência de Version exige avaliação de concorrência nas units consumidoras; não prova perda de atualização em toda operação.

## Rastreabilidade de Código

| Ref. | Arquivo | Cobertura |
|---|---|---|
| 🟢 M01 | src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java | id, getId/setId, isNew, Serializable e MappedSuperclass |
| 🟢 M02 | src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java | name, NotBlank, getters/setters e toString |
| 🟢 M03 | src/main/java/org/springframework/samples/petclinic/model/Person.java | firstName/lastName, NotBlank, Size e Column |
| 🟢 M04 | src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java | Expectativas para firstName/lastName vazios, path e mensagem inglesa |
| 🟢 M05 | src/test/java/org/springframework/samples/petclinic/vet/VetTests.java | Serialização Java de Vet com id/nome/sobrenome, sem especialidades preenchidas |
| 🟢 M06 | src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java | Registro de serialização BaseEntity/Person/Vet |

🟢 CONFIRMADO — Consumidores: src/main/java/org/springframework/samples/petclinic/owner/{Owner,Pet,PetType,Visit}.java e vet/{Vet,Specialty}.java. package-info.java apenas declara pacote e documentação. Mais detalhes em [dicionário](../data-dictionary.md), [ERD](../erd-complete.md) e [análise técnica](../code-analysis.md).
