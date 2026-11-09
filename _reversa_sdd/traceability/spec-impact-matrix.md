# Matriz de impacto entre componentes

## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](../validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


🟢 CONFIRMADO — Setas significam dependência/import/configuração observada; impacto recomendado é 🟡 INFERIDO. Units futuras em português: owner → responsaveis, vet → veterinarios, model → modelo, system → sistema. Dependências de framework não criam novas units.

| Origem alterada | Consumidores e possível impacto | Evidência | Units a revisar |
|---|---|---|---|
| model/BaseEntity | identidade, isNew e serialização de todas as entidades | herança em Person/NamedEntity/Visit; RuntimeHints | modelo, responsaveis, veterinarios, sistema |
| model/Person | nomes de responsáveis e veterinários, validação/colunas | Owner extends Person; Vet extends Person | modelo, responsaveis, veterinarios |
| model/NamedEntity | nomes de animais, tipos e especialidades | Pet/PetType/Specialty; Vet comparator | modelo, responsaveis, veterinarios |
| owner/Owner e OwnerRepository | busca, edição, persistência em cascata, agendamento | injeção em Owner/Pet/VisitController | responsaveis |
| owner/Pet e PetValidator | dados/validação, binding e views de animal/visita | PetController, VisitController | responsaveis |
| PetTypeRepository/Formatter | catálogo e conversão de nomes do formulário | PetController e Formatter | responsaveis |
| owner/Visit | data/descrição, model pré-carregado e persistência | VisitController e Pet.visits | responsaveis |
| vet/VetRepository | listagem paginada e completa, cache vets | VetController; anotações cache | veterinarios, sistema |
| vet/Vet e Specialty | ordenação e representação da lista | VetController, Vets e RuntimeHints | veterinarios, sistema |
| system/CacheConfiguration | customização cache vets e estatísticas | cache nomeado vets | sistema, veterinarios |
| system/WebConfiguration e messages | idioma transversal às views | interceptor global, basename | sistema, responsaveis, veterinarios |
| templates/fragments | layout, erros e datas compartilhados | th:replace nas páginas de formulário | sistema, responsaveis, veterinarios |
| db/*/schema.sql e application*.properties | persistência/constraints para cada perfil | SQL init e JPA mappings | responsaveis, veterinarios, sistema |
| PetClinicApplication/RuntimeHints | inicialização e empacotamento nativo | SpringApplication e registerHints | sistema, modelo, veterinarios |
| pom.xml/build.gradle e wrappers | dependências, compilação, testes, runtime | manifests e workflows | todas |
| k8s/*.yml e docker-compose.yml | bancos, portas e configuração operacional | deployment.md | sistema, responsaveis, veterinarios |

## Direção de dependências

- 🟢 CONFIRMADO — owner → model; vet → model. Não há chamada owner → vet no código examinado; Visit não referencia veterinário.
- 🟢 CONFIRMADO — system configura cache consumido por vet e inicialização transversal referencia model/vet via runtime hints. owner depende de infraestrutura MVC/i18n em execução, sem import Java direto para system.
- 🟢 CONFIRMADO — Fluxos usam os mesmos templates/fragments e datasource por aplicação; tabelas compartilhadas não implicam serviços separados.

🔴 LACUNA — Matriz mede relações estáticas, não frequência de chamadas nem cobertura de regressão executada. Versões dos contratos e dependências operacionais efetivas devem ser validadas na implantação.
