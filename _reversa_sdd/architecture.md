# Arquitetura do spring-petclinic

## Stack confirmada para a reescrita

🟢 **Next.js em monorepo Nx** é a stack de destino informada pelo usuário (TARGET-STACK-01 em [decisões validadas](validated-decisions.md)). PostgreSQL permanece o banco escolhido. A topologia apps/libs, a divisão entre interface e servidor, a persistência e os contratos precisam de desenho próprio. Não há decisão de backend separado ou manutenção de Spring. As seções abaixo e os diagramas C4 existentes descrevem o legado; tarefas dependentes de Java/JPA/Thymeleaf precisam ser traduzidas antes da implementação.


## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


## Visão e limites

🟢 CONFIRMADO — Aplicação Java 17 / Spring Boot 4.1.0, empacotada como uma aplicação executável, com Spring MVC, templates Thymeleaf e persistência Spring Data JPA. Maven e Gradle coexistem; não há serviços separados por domínio. Fontes: pom.xml, build.gradle, PetClinicApplication.java e controllers. 🟡 INFERIDO — A classificação arquitetural é monólito com pacotes por domínio e infraestrutura compartilhada.

🟢 CONFIRMADO — Módulos lógicos: owner (responsáveis, animais, visitas), vet (consulta de veterinários/especialidades), model (superclasses JPA) e system (idioma/cache/boas-vindas/erro), além da inicialização no pacote raiz. Não há camada service própria em src/main para separar os controllers dos repositórios. O nome ClinicServiceTests em testes não prova existência de ClinicService no código de produção.

## Fluxo de requisição e persistência

🟢 CONFIRMADO — MVC resolve modelos e binding, aplica Bean Validation/PetValidator e regras do controller. Controllers de owner persistem Owner em cascata para pets/visits; consulta vet usa repository readOnly e cache vets. Thymeleaf renderiza páginas e formulários, com redirect e flash em sucesso. GET /vets retorna envelope @ResponseBody. Fontes: OwnerController, PetController, VisitController, VetController, entidades e templates.

🟢 CONFIRMADO — Não há transação de aplicação declarada envolvendo toda a sequência carregar-validar-salvar no controller. saveAndFlush de Pet é escolhido para observar erro de unicidade no ponto de persistência. Owner.pets e Pet.visits usam fetch EAGER, cascade ALL; vet.specialties é EAGER. open-in-view=false e batch_fetch_size=16 estão configurados. 🔴 LACUNA — Sem execução não há perfil de consultas ou garantia sobre custo/latência de agregados grandes.

## Dados e integrações

🟢 CONFIRMADO — Uma base relacional por perfil: H2 por padrão, MySQL ou PostgreSQL como alternativas. Os schemas e seeds SQL são carregados por configuração; ddl-auto=none. Existem seis entidades JPA e a associação vet_specialties. Banco default H2 recria tabelas no schema.sql; perfis externos têm CREATE IF NOT EXISTS. Não há ferramenta de migração incremental identificada nos manifests lidos.

🟢 CONFIRMADO — Serviços de dados usam JDBC e FKs. Não foram identificados clientes de API de negócio externa, broker de mensagens, webhook ou worker dedicado no código principal. Bootstrap, Font Awesome, fontes e CSS são recursos da interface, não integrações de negócio. GET /vets é contrato de dados produzido; formulários § são outros contratos HTTP produzidos. XML em execução não foi comprovado.

## Configuração e operação

🟢 CONFIRMADO — Idioma usa sessão com padrão inglês e parâmetro lang. Cache vets é anotado no repository e existe customizador JCache com estatísticas; Caffeine é dependência runtime. 🔴 LACUNA — Provedor ativo, limites/TTL e invalidação não definidos explicitamente. Actuator seleciona todos os endpoints para exposição, sem política de acesso local identificada. Não há autenticação de domínio implementada no código examinado.

🟢 CONFIRMADO — Docker Compose inicia bancos, não a aplicação. Kubernetes declara serviço NodePort, uma réplica da aplicação, PostgreSQL e probes. Buildpacks são o caminho de imagem documentado no README, sem Dockerfile de aplicação na raiz. Há `.devcontainer/Dockerfile` para ambiente de desenvolvimento/Gitpod, distinto do empacotamento da aplicação. [Revisão estática: escopo corrigido.] Ver deployment.md para discrepâncias e limites.

## Pontos de atenção fundamentados

| ID | Achado | Evidência e consequência |
|---|---|---|
| ARQ-01 | 🟢 Duplicidade de lógica de paginação e validação temporal | OwnerController/VetController e PetController; 🟡 risco de divergência entre fluxos |
| ARQ-02 | 🟢 Tradução de duplicidade por texto de exceção | PetController.isDuplicatePetNameViolation; 🔴 mensagem/constraint MySQL não validada |
| ARQ-03 | 🟢 Datas de visita nullable na entidade/DDL | Visit e VisitController; 🔴 política de data vazia pendente |
| ARQ-04 | 🟢 Exposição ampla Actuator e ausência de autenticação local | application.properties e manifests; 🔴 proteção efetiva externa não conhecida |
| ARQ-05 | 🟢 Recursos de banco/hints e dois builds independentes | build.gradle/pom.xml; 🔴 equivalência de testes/build e imagem não executada |
| ARQ-06 | 🟢 Página sem ordenação explícita | PageRequest nos controllers; 🟡 ordem pode variar por banco/plano |
| ARQ-07 | 🟢 Manifesto PostgreSQL sem volume persistente | k8s/db.yml; 🟡 retenção de dados não garantida entre substituições de pod |
| ARQ-08 | 🟢 Cache sem política explícita na configuração lida | CacheConfiguration; 🔴 limites e frescor a validar |

🟢 CONFIRMADO — Testes existem em todos os domínios, mas não se executou suite nem mediu cobertura. Não foi realizada auditoria de versões/vulnerabilidades; não classificar dependências como desatualizadas sem esse levantamento.

## Artefatos relacionados

[c4-context.md](c4-context.md), [c4-containers.md](c4-containers.md), [c4-components.md](c4-components.md), [erd-complete.md](erd-complete.md), [deployment.md](deployment.md), [matriz de impacto](traceability/spec-impact-matrix.md). Regras em domain.md; campos em data-dictionary.md; detalhes de métodos em code-analysis.md.
