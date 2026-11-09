# Domínio e regras

## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


## Glossário

🟢 CONFIRMADO — **Owner** é o responsável por animais; **Pet**, o animal com nome, nascimento e tipo; **PetType**, catálogo de espécies/tipos; **Visit**, registro/agendamento vinculado ao animal, com data e descrição; **Vet**, veterinário; **Specialty**, sua especialidade. Evidência: entidades em owner/ e vet/.

🟡 INFERIDO — A persona operacional é uma pessoa da clínica que cadastra responsáveis, animais e visitas e consulta veterinários. Não há separação de recepcionista/veterinário/administrador implementada. O sistema é apresentado como aplicação de exemplo no README; não inferir requisitos regulatórios ou processos clínicos completos.

## Catálogo de regras extraídas

| ID | Comportamento | Fonte relativa ao pacote Java, salvo indicação |
|---|---|---|
| RN-01 | 🟢 CONFIRMADO — Pessoa exige nome e sobrenome não vazios, até 30 caracteres cada. | `model/Person.java` |
| RN-02 | 🟢 CONFIRMADO — Responsável exige endereço até 255, cidade até 80 e telefone com exatamente 10 dígitos; todos não vazios. | `owner/Owner.java` |
| RN-03 | 🟢 CONFIRMADO — Busca de responsáveis usa prefixo de sobrenome, strip nas bordas, vazio para todos, páginas de 5. | `owner/OwnerController.java; owner/OwnerRepository.java` |
| RN-04 | 🟢 CONFIRMADO — Página fora de faixa redireciona à primeira; busca com total de um responsável redireciona ao detalhe. | `owner/OwnerController.java; vet/VetController.java` |
| RN-05 | 🟢 CONFIRMADO — Formulário de animal exige nome textual de até 30 caracteres e data de nascimento não nula; tipo obrigatório na criação. | `owner/PetValidator.java` |
| RN-06 | 🟢 CONFIRMADO — Nascimento posterior a hoje é rejeitado na criação e edição de animal. | `owner/PetController.java` |
| RN-07 | 🟢 CONFIRMADO — Nome de animal já pertencente ao mesmo responsável é rejeitado ignorando caixa; edição do mesmo ID mantém nome. | `owner/PetController.java; owner/Owner.java` |
| RN-08 | 🟢 CONFIRMADO — Banco complementa unicidade de nome por responsável; tradução do erro depende da mensagem conter unique_owner_pet_name. | `owner/PetController.java; resources/db/*/schema.sql` |
| RN-09 | 🟢 CONFIRMADO — Adição de animal ignora null, mesma instância e ID persistido já presente, mas admite objetos novos diferentes. | `owner/Owner.java` |
| RN-10 | 🟢 CONFIRMADO — Visita nasce com data de amanhã; data não nula até hoje é rejeitada e descrição exige texto até 255 caracteres. | `owner/Visit.java; owner/VisitController.java` |
| RN-11 | 🟢 CONFIRMADO — Visita exige que o petId pertença ao ownerId; ausência gera IllegalArgumentException. | `owner/VisitController.java; owner/Owner.java` |
| RN-12 | 🟢 CONFIRMADO — GET /vets retorna envelope com todos os veterinários; /vets.html é paginado em 5. | `vet/VetController.java; vet/Vets.java` |
| RN-13 | 🟢 CONFIRMADO — Especialidades são mantidas em conjunto e expostas como lista ordenada por nome. | `vet/Vet.java` |
| RN-14 | 🟢 CONFIRMADO — Identidade nova significa somente id null; não há enum de situação de negócio nas entidades examinadas. | `model/BaseEntity.java; owner/*.java; vet/*.java` |
| RN-15 | 🟢 CONFIRMADO — IDs enviados por formulário são bloqueados nos binders de responsáveis, animais e visitas. | `owner/OwnerController.java; owner/PetController.java; owner/VisitController.java` |
| RN-16 | 🟢 CONFIRMADO — Idioma inicia em inglês e pode ser alterado por lang, persistindo em sessão. | `system/WebConfiguration.java` |
| RN-17 | 🟢 CONFIRMADO — GET /oups lança exceção de demonstração; GET / retorna welcome. | `system/CrashController.java; system/WelcomeController.java` |

## História e evidências

🟢 CONFIRMADO — Histórico local inspecionado em HEAD 500158f732419217507c7656904b8e6aa1bcc0d6, com 25 commits recentes e busca temática no histórico disponível. Mensagens e diffs relevantes foram usados em ADRs; a data de autoria pode divergir da data de integração. Não foi realizada leitura exaustiva de todos os commits ou de issues remotas.

| Commit | Evidência no histórico | Interpretação |
|---|---|---|
| e0db9b1 | Enforce unique pet names per owner; adiciona índices/restrições e tratamento saveAndFlush | 🟢 CONFIRMADO — regra de unicidade também no banco |
| 88e37c1 | IF NOT EXISTS e nomes explícitos de índices PostgreSQL | 🟢 CONFIRMADO — evita recriação de relação já existente |
| 753d35c | Validate future visit dates; construtor amanhã e minVisitDate | 🟢 CONFIRMADO — visitas como agendamento futuro no formulário |
| 676db04 | Fix persisted pet association and enforce pet name length | 🟢 CONFIRMADO — preserva associação de pet persistido e limita nome |
| 500158f | redirection ... page ... out of bounds | 🟢 CONFIRMADO — páginas fora de faixa voltam à primeira |
| bb37aad | normalize whitespace in owner search | 🟡 INFERIDO — intenção indicada pela mensagem; comportamento strip confirmado no código atual |
| 0c88f91 | Adding strings for all other languages | 🟡 INFERIDO — contexto de internacionalização; idioma de sessão confirmado em WebConfiguration |

## Lacunas para validação posterior

- 🔴 LACUNA D-01 — A data da visita deve ser obrigatória? Código atual admite null na validação manual e no SQL; confirmar contrato pretendido para entrada vazia.
- 🔴 LACUNA D-02 — Confirmar collation e mensagem de duplicidade MySQL em ambiente real; o schema não nomeia a constraint como o catch espera.
- 🔴 LACUNA D-03 — Quais políticas de acesso/rede são aplicadas fora do repositório? Não há papéis implementados no código examinado.
- 🔴 LACUNA D-04 — Qual CacheManager é usado em implantação, com quais limites e invalidação? Customizador JCache não prova ativação desse provedor.
- 🔴 LACUNA D-05 — Qual fuso e relógio devem governar hoje/amanhã? LocalDate.now usa o padrão da JVM; nenhuma definição explícita localizada.
- 🔴 LACUNA D-06 — Qual comportamento HTTP desejado para IDs inexistentes de responsável/animal? Exceções locais são diferentes e não há handler de negócio explícito identificado.

## Logs e limites da investigação

🟢 CONFIRMADO — Não foram localizados arquivos .log no escopo do legado examinado, nem logging de eventos de domínio nos módulos Java principais. application.properties configura org.springframework=INFO. Sem amostra de execução não se pode atribuir recorrência a erros. Não foram encontrados TODO/FIXME de domínio na busca realizada. Não há regras de pagamento, disponibilidade de agenda, duração de consulta ou atribuição de veterinário a Visit no modelo lido.
