# Análise técnica do legado

Escavação dos quatro módulos concluída. Nível detalhado; organização das specs por módulo. 🟢 CONFIRMADO indica leitura direta; 🟡 INFERIDO indica interpretação; 🔴 LACUNA exige validação. A aplicação e os testes não foram executados.

## owner — responsáveis, animais e visitas

🟢 CONFIRMADO — Agregado persistido via OwnerRepository (JpaRepository). Não existe serviço intermediário nestes fluxos. Owner possui pets em List/ArrayList, EAGER, cascade ALL, owner_id, ordenação por nome; Pet possui visits em Set/LinkedHashSet, EAGER, cascade ALL, pet_id, ordenação date ASC. Pet referencia PetType via ManyToOne. Consultas de tipos usam JPQL com ORDER BY name. Fontes: Owner.java, Pet.java, PetTypeRepository.java no pacote owner.

### Busca e edição de responsáveis

OwnerController.findOwner(Integer): Owner cria novo objeto se ownerId ausente; caso contrário busca Optional e lança IllegalArgumentException se não encontrado. Binder rejeita id e *.id. initCreationForm/initUpdateOwnerForm retornam owners/createOrUpdateOwnerForm; initFindForm retorna owners/findOwners. processCreationForm(Owner, BindingResult, RedirectAttributes): String retorna formulário em erro; salva e redireciona /owners/{id} em sucesso, com flash message. processUpdateOwnerForm valida primeiro, depois compara ID carregado com ID da URL; divergência rejeita id e redireciona edição. Em sucesso fixa ID, salva e redireciona detalhe. showOwner(int): ModelAndView consulta novamente o responsável e lança IllegalArgumentException em ausência.

processFindForm(int page, Owner, BindingResult, Model, RedirectAttributes): String normaliza sobrenome nulo para vazio e aplica strip. findPaginatedForOwnersLastName consulta prefixo com PageRequest.of(max(page,1)-1,5); não declara ordenação explícita. A consulta precede o teste de faixa. Página menor que 1 ou maior que max(totalPages,1) redireciona página 1 e mantém filtro não vazio. Resultado vazio rejeita lastName/notFound; totalElements igual a 1 redireciona detalhe; múltiplos populam currentPage, totalPages, totalItems, listOwners e retornam owners/ownersList. Fontes: OwnerController.java e OwnerRepository.java.

### Animais e integridade

PetController.populatePetTypes(): Collection fornece catálogo; findOwner(int): Owner lança em ausência; findPet(int,Integer): Pet cria novo se petId ausente, senão procura somente na coleção do responsável. Nesse último caminho pode retornar null; não existe rejeição explícita de animal inexistente como em VisitController. initOwnerBinder bloqueia IDs; initPetBinder instala PetValidator e bloqueia IDs. initCreationForm adiciona um novo Pet ao owner em memória e retorna formulário; initUpdateForm apenas retorna formulário.

processCreationForm: valida nome já persistido do mesmo responsável, ignorando caixa, apenas quando há texto e pet.isNew. Rejeita nascimento futuro quando data não nula. BindingResult com erros retorna formulário. Caso válido, addPet e saveAndFlush persistem em cascata. DataIntegrityViolationException vira name/duplicate apenas se ex.getMessage().toLowerCase contém unique_owner_pet_name; outras violações são relançadas. Retorno de sucesso: redirect:/owners/{ownerId} com flash.

processUpdateForm: busca nome igual no owner sem ignorar novos e rejeita se IDs diferem; permite o mesmo nome no mesmo ID. Aplica a mesma validação temporal e tratamento de integridade. updatePetDetails(Owner,Pet): void exige ID não nulo via Assert.state; procura Pet existente e copia nome, nascimento e tipo, ou adiciona se não localizado; termina com saveAndFlush.

PetValidator.validate(Object,Errors): void exige nome com texto, tamanho máximo 30, tipo apenas em Pet novo e nascimento não nulo. Não aplica trim ao nome; nascimento futuro é regra do controller. supports(Class): boolean usa Pet.class.isAssignableFrom. Código de erro: required, size, typeMismatch.birthDate; erro de conversão de data pode ser typeMismatch. Fonte: PetValidator.java e PetController.java.

Owner.addPet(Pet): void ignora null, mesmo objeto já contido e outro persistido com mesmo ID; dois objetos novos distintos são aceitos. Custo O(n). getPet(Integer): Pet percorre apenas pets persistidos e compara IDs com Objects.equals. getPet(String,boolean): Pet percorre nomes não nulos com equalsIgnoreCase, opcionalmente ignorando novos; getPet(String) delega com false. Busca O(n). Não existe override de equals/hashCode nas entidades base lidas. A unicidade de nome é controlada separadamente no controller e no banco.

PetTypeFormatter.print(PetType,Locale): String devolve nome ou literal <null>. parse(String,Locale): PetType percorre catálogo e compara nome com Objects.equals, portanto sensível a caixa, lançando ParseException com offset 0 se ausente. Custo O(t). Locale não modifica a transformação.

### Visitas

VisitController.loadPetWithVisit(int,int,Map): Visit busca owner, exige animal pertencente a ele, adiciona pet e owner ao model, constrói Visit e já adiciona à coleção em memória. Executa como ModelAttribute antes de GET/POST. Falhas de vínculo lançam IllegalArgumentException. Visit() inicia date em LocalDate.now().plusDays(1). minVisitDate(): LocalDate fornece amanhã à view e ao atributo HTML min no fragmento inputField.

processNewVisitForm(Owner,int,Visit,BindingResult,RedirectAttributes): String rejeita date não nula que não seja posterior a hoje. Erro retorna pets/createOrUpdateVisitForm; sucesso chama Owner.addVisit, salva Owner e redireciona detalhe com flash. Owner.addVisit exige petId, Visit e Pet existente usando Assert.notNull; delega Pet.addVisit. A adição prévia e posterior da mesma instância é deduplicada pelo Set; não há deduplicação por data ou descrição.

🔴 LACUNA — Visit.date não possui @NotNull, e a verificação de futuro ignora null. A obrigatoriedade no servidor de uma data enviada vazia precisa de validação dinâmica. Não converter o comportamento esperado em regra confirmada.

### Divergências entre bancos e riscos a validar

🟢 CONFIRMADO — H2 usa VARCHAR_IGNORECASE e constraint unique_owner_pet_name em (owner_id,name); PostgreSQL usa índice único nomeado em (owner_id,LOWER(name)); MySQL declara UNIQUE(owner_id,name) sem o mesmo nome explícito nem collation declarada no arquivo. 🔴 LACUNA — confirmar mensagem real de violação do MySQL e equivalência de comparação de caixa; o catch depende do nome textual. Não foi executada simulação de concorrência.

🟢 CONFIRMADO — SQL admite null em diversos campos que Bean Validation exige; tipo do pet é NOT NULL no DDL, embora PetValidator só o exija na criação. Semântica de banco e validação de formulário são camadas diferentes. Não há remoção de responsáveis/animais/visitas exposta pelos controllers examinados. IDs bloqueados no binding não constituem autenticação ou autorização.

### Evidência de testes existentes

PetControllerTests cobre criação/edição válidas, nome vazio/duplicado, ausência de tipo, nascimento inválido e violações com mensagem simulada unique_owner_pet_name. VisitControllerTests cobre amanhã válido, hoje inválido e descrição longa; não comprova o caso date vazio com descrição válida. OwnerControllerTests contém cenários de paginação, strip e limites de endereço/cidade. PetValidatorTests, OwnerTests e PetTypeFormatterTests exercitam validação, duplicidade de objetos e conversão. Testes localizados/lidos parcialmente para rastreio, não executados: não declarar aprovação ou cobertura percentual.

## vet — veterinários e especialidades

🟢 CONFIRMADO — Vet herda Person; Specialty herda NamedEntity. ManyToMany EAGER mapeia vet_specialties(vet_id,specialty_id). Não há cascade explícito. getSpecialtiesInternal(): Set inicializa HashSet sob demanda; addSpecialty(Specialty): void adiciona sem validar null. getSpecialties(): List cria lista ordenada por NamedEntity.getName (O(s log s)), sem alterar o conjunto interno; nomes null podem causar falha no comparador. getNrOfSpecialties(): int retorna tamanho. Vets é envelope de transporte com getVetList(): List, inicializando ArrayList sob demanda; possui anotações JAXB XmlRootElement/XmlElement.

VetRepository expõe findAll(): Collection e findAll(Pageable): Page, ambos @Transactional(readOnly=true), @Cacheable("vets") e throws DataAccessException. Não oferece escrita. VetController.showVetList(int,Model,RedirectAttributes): String consulta páginas de 5, converte página externa para índice baseado em zero e valida faixa após consulta. Página inválida redireciona /vets.html?page=1; caso válido popula currentPage, totalPages, totalItems, listVets e retorna vets/vetList. Sem ordenação explícita no PageRequest. showResourcesVetList(): Vets atende GET /vets com @ResponseBody, adicionando todos os resultados não paginados ao envelope. Não inferir suporte XML em execução apenas pelas anotações JAXB.

🔴 LACUNA — TTL, capacidade, provedor efetivo e estratégia de invalidação de cache não estão explicitados nos arquivos examinados; ver análise de system. Dados podem ser alterados externamente, sem endpoint de mutação neste módulo. Testes VetControllerTests/VetTests foram localizados; execução pendente.

## model — classes compartilhadas

🟢 CONFIRMADO — BaseEntity, NamedEntity e Person são @MappedSuperclass; não criam tabelas próprias. BaseEntity implementa Serializable, armazena Integer id com IDENTITY; getId(): Integer e setId(Integer): void são acesso direto. isNew(): boolean retorna id == null, independentemente de consulta ao banco. NamedEntity adiciona String name @NotBlank; getName/setName são acesso direto, e toString(): String devolve nome ou <null>. Person adiciona firstName e lastName, ambos String, @NotBlank e @Size(max=30), @Column(length=30), com getters/setters diretos. Não há normalização, equals/hashCode, controle de versão otimista ou configuração por ambiente nessas classes.

🟡 INFERIDO — Ausência de @Version exige avaliar concorrência conforme o agregado e a transação; não prova perda de atualização em todo fluxo. A herança compartilha campos com owners/vets e pets/types/specialties. Obrigatoriedade de id refere-se à persistência: objetos novos têm null.

## system — configuração web, cache e diagnóstico

🟢 CONFIRMADO — WelcomeController.welcome(): String atende GET / e retorna a view welcome. CrashController.triggerException(): String atende GET /oups e sempre lança RuntimeException com mensagem demonstrativa; não retorna normalmente. templates/error.html apresenta mensagem e explicação conforme status. CrashControllerTests verifica exceção direta; CrashControllerIntegrationTests verifica 500 HTML/JSON com spring.web.error.include-message=ALWAYS apenas no ambiente daquele teste. Não inferir essa exposição de mensagem como configuração padrão de produção.

WebConfiguration.localeResolver(): LocaleResolver cria SessionLocaleResolver com Locale.ENGLISH. localeChangeInterceptor(): LocaleChangeInterceptor define parâmetro lang; addInterceptors(InterceptorRegistry): void o registra. Assim a preferência de idioma é mantida na sessão, independente dos objetos de formulário, que são carregados por requisição. Não há enum de idiomas permitidos ou política explícita de locale inválido nesses métodos. spring.messages.basename=messages/messages aponta aos bundles; messages_en.properties usa fallback segundo comentário do teste I18nPropertiesSyncTest. Esse teste busca textos HTML literais e chaves ausentes; não prova tradução semanticamente correta.

CacheConfiguration habilita @EnableCaching e declara bean petclinicCacheConfigurationCustomizer(): JCacheManagerCustomizer. A função retornada cria cache vets usando cacheConfiguration(): javax.cache.configuration.Configuration<Object,Object>, implementada por MutableConfiguration.setStatisticsEnabled(true). Não há configuração explícita de tamanho/expiração/eviction nem chamada de invalidação nestes arquivos. A presença desse customizador não prova que JCache é o provedor ativo: Caffeine é dependência runtime e não há provedor JCache confirmado por execução. Fonte: CacheConfiguration.java, pom.xml e build.gradle. 🔴 LACUNA — verificar CacheManager efetivo e comportamento do customizador no perfil implantado.

### Configuração transversal e inicialização

🟢 CONFIRMADO — PetClinicApplication.main(String[]): void executa SpringApplication.run e importa PetClinicRuntimeHints. registerHints(RuntimeHints,ClassLoader): void registra recursos db/*, db/*/*, messages/*, mysql-default-conf e serialização Java para BaseEntity, Person e Vet. O parâmetro classLoader não é usado. Esses arquivos estão no pacote raiz e são incluídos como inicialização transversal, não como um quinto módulo.

application.properties define database=h2, SQL schema/data por interpolação de database, ddl-auto=none, open-in-view=false, PhysicalNamingStrategySnakeCaseImpl, default_batch_fetch_size=16, cache HTTP de estáticos 12h e logging org.springframework=INFO. management.endpoints.web.exposure.include=* declara exposição ampla; acesso efetivo depende dos endpoints/autoconfiguração. Perfis mysql/postgres habilitam SQL init always e URLs/usuário/senha com overrides MYSQL_URL/USER/PASS ou POSTGRES_URL/USER/PASS. Nenhum segredo externo foi consultado. Não há feature flags de negócio identificadas.

🔴 LACUNA — SessionLocaleResolver não representa sessão autenticada. A pesquisa por SecurityFilterChain/PreAuthorize/Secured/RolesAllowed e dependências Spring Security não encontrou política de acesso no código examinado. Restrições de rede, proxy e plataforma não podem ser inferidas do código local.

### Limites e complexidade

Métodos próprios de system são configurações O(1), além do trabalho interno do framework, não medido. Falhas de criação de cache ou parsing de locale são delegadas ao framework sem tratamento local. Não há modelos JPA/DTOs de negócio neste módulo. Spring Boot Actuator e o pipeline Kubernetes compõem observabilidade técnica; não foi localizado arquivo .log de execução para inferir eventos reais ou frequência de erros.
