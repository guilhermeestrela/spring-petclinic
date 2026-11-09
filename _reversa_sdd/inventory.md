# Inventário — spring-petclinic

Gerado em 2026-11-09T21:11:01.954528+00:00. Reconhecimento estático, sem executar aplicação ou testes.

## Escopo e tecnologias

🟢 CONFIRMADO — 133 arquivos no escopo; infraestrutura Reversa, Git, caches e saídas de build excluídos.

| Linguagem | Arquivos |
|---|---:|
| Java | 50 |
| HTML | 12 |
| SCSS | 4 |
| CSS | 1 |
| SQL | 7 |
| Groovy (build) | 2 |

🟢 CONFIRMADO — Java 17; Spring Boot 4.1.0; MVC, Thymeleaf, JPA, validação, Actuator e cache Caffeine. Evidências: `pom.xml`, `build.gradle`, `src/main/resources/application.properties`.

## Módulos

- 🟢 CONFIRMADO — `owner`: responsáveis, animais, tipos e visitas (`src/main/java/org/springframework/samples/petclinic/owner/`).
- 🟢 CONFIRMADO — `vet`: veterinários e especialidades (`src/main/java/org/springframework/samples/petclinic/vet/`).
- 🟢 CONFIRMADO — `model`: entidades base compartilhadas (`src/main/java/org/springframework/samples/petclinic/model/`).
- 🟢 CONFIRMADO — `system`: página inicial, erros, cache e configuração web (`src/main/java/org/springframework/samples/petclinic/system/`).

## Pontos de entrada

- `src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java` — app_entry
- `src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java` — mvc_controller
- `src/main/java/org/springframework/samples/petclinic/owner/PetController.java` — mvc_controller
- `src/main/java/org/springframework/samples/petclinic/owner/VisitController.java` — mvc_controller
- `src/main/java/org/springframework/samples/petclinic/system/CrashController.java` — mvc_controller
- `src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java` — mvc_controller
- `src/main/java/org/springframework/samples/petclinic/vet/VetController.java` — mvc_controller

Controllers atendem páginas e formulários de responsáveis, animais e visitas, listagem de veterinários (`/vets.html`, `/vets`), início (`/`) e erro (`/oups`).

## Configuração, build e implantação

- `build.gradle`
- `pom.xml`
- `settings.gradle`
- `.mvn/wrapper/maven-wrapper.properties`
- `gradle/wrapper/gradle-wrapper.properties`
- `src/main/resources/application-mysql.properties`
- `src/main/resources/application-postgres.properties`
- `src/main/resources/application.properties`
- `.github/workflows/deploy-and-test-cluster.yml`
- `.github/workflows/gradle-build.yml`
- `.github/workflows/maven-build.yml`
- `docker-compose.yml`
- `k8s/petclinic.yml`
- `k8s/db.yml`

🟢 CONFIRMADO — Wrappers Maven 3.9.16 e Gradle 9.5.1. CI executa `./mvnw -B verify`, `./gradlew build` e implantação Kind/Kubernetes. Não há Dockerfile de empacotamento da aplicação na raiz; existe `.devcontainer/Dockerfile`, referenciado por `.gitpod.yml` para ambiente de desenvolvimento. Kubernetes referencia imagem `dsyer/petclinic`. [Revisão estática: escopo de Dockerfile corrigido.]

## Dados e integrações

🟢 CONFIRMADO — H2 é a configuração padrão; MySQL e PostgreSQL são alternativas por perfil. Duas integrações externas de banco configuradas, não simultaneamente obrigatórias. Nenhuma API externa de negócio identificada no reconhecimento. Docker Compose declara MySQL 9.7 e PostgreSQL 18.4.

Arquivos SQL (inventário superficial):

- `src/main/resources/db/h2/data.sql`
- `src/main/resources/db/h2/schema.sql`
- `src/main/resources/db/mysql/data.sql`
- `src/main/resources/db/mysql/schema.sql`
- `src/main/resources/db/mysql/user.sql`
- `src/main/resources/db/postgres/data.sql`
- `src/main/resources/db/postgres/schema.sql`

Entidades JPA localizadas: Owner, Pet, PetType, Visit, Vet e Specialty. Schema e dados são inicializados por SQL; `ddl-auto=none`.

## Testes

🟢 CONFIRMADO — 18 arquivos Java com nomes Test/Tests; 20 arquivos Java totais em testes (inclui utilitários). JUnit Jupiter, Spring Boot Test/MockMvc, Mockito, AssertJ e Testcontainers identificados. Plano JMeter em `src/test/jmeter/petclinic_test_plan.jmx`. JaCoCo configurado no Maven.

🔴 LACUNA — Percentual de cobertura e aprovação dos testes não medidos; contagem de arquivos não equivale a cobertura.

## Organização sugerida

🟡 INFERIDO — Organizar specs por módulo, acompanhando os pacotes de domínio. Roteamento distribuído em controllers; não há roteador central dominante. A escolha final depende do usuário.

## Árvore completa de diretórios no escopo

```text
.
.devcontainer
.github
.mvn
gradle
k8s
src
.github/workflows
.mvn/wrapper
gradle/wrapper
src/checkstyle
src/main
src/test
src/main/java
src/main/resources
src/main/scss
src/main/java/org
src/main/java/org/springframework
src/main/java/org/springframework/samples
src/main/java/org/springframework/samples/petclinic
src/main/java/org/springframework/samples/petclinic/model
src/main/java/org/springframework/samples/petclinic/owner
src/main/java/org/springframework/samples/petclinic/system
src/main/java/org/springframework/samples/petclinic/vet
src/main/resources/db
src/main/resources/messages
src/main/resources/static
src/main/resources/templates
src/main/resources/db/h2
src/main/resources/db/mysql
src/main/resources/db/postgres
src/main/resources/static/resources
src/main/resources/static/resources/css
src/main/resources/static/resources/fonts
src/main/resources/static/resources/images
src/main/resources/templates/fragments
src/main/resources/templates/owners
src/main/resources/templates/pets
src/main/resources/templates/vets
src/test/java
src/test/jmeter
src/test/java/org
src/test/java/org/springframework
src/test/java/org/springframework/samples
src/test/java/org/springframework/samples/petclinic
src/test/java/org/springframework/samples/petclinic/model
src/test/java/org/springframework/samples/petclinic/owner
src/test/java/org/springframework/samples/petclinic/service
src/test/java/org/springframework/samples/petclinic/system
src/test/java/org/springframework/samples/petclinic/vet
```

## Arquivos no escopo

```text
.editorconfig
.gitattributes
.gitignore
.gitpod.yml
AGENTS.md
LICENSE.txt
README.md
build.gradle
docker-compose.yml
gradlew
gradlew.bat
mvnw
mvnw.cmd
pom.xml
settings.gradle
.devcontainer/Dockerfile
.devcontainer/devcontainer.json
.github/dco.yml
.github/workflows/deploy-and-test-cluster.yml
.github/workflows/gradle-build.yml
.github/workflows/maven-build.yml
.mvn/wrapper/maven-wrapper.properties
gradle/wrapper/gradle-wrapper.jar
gradle/wrapper/gradle-wrapper.properties
k8s/db.yml
k8s/petclinic.yml
src/checkstyle/nohttp-checkstyle-suppressions.xml
src/checkstyle/nohttp-checkstyle.xml
src/main/java/org/springframework/samples/petclinic/PetClinicApplication.java
src/main/java/org/springframework/samples/petclinic/PetClinicRuntimeHints.java
src/main/java/org/springframework/samples/petclinic/package-info.java
src/main/java/org/springframework/samples/petclinic/model/BaseEntity.java
src/main/java/org/springframework/samples/petclinic/model/NamedEntity.java
src/main/java/org/springframework/samples/petclinic/model/Person.java
src/main/java/org/springframework/samples/petclinic/model/package-info.java
src/main/java/org/springframework/samples/petclinic/owner/Owner.java
src/main/java/org/springframework/samples/petclinic/owner/OwnerController.java
src/main/java/org/springframework/samples/petclinic/owner/OwnerRepository.java
src/main/java/org/springframework/samples/petclinic/owner/Pet.java
src/main/java/org/springframework/samples/petclinic/owner/PetController.java
src/main/java/org/springframework/samples/petclinic/owner/PetType.java
src/main/java/org/springframework/samples/petclinic/owner/PetTypeFormatter.java
src/main/java/org/springframework/samples/petclinic/owner/PetTypeRepository.java
src/main/java/org/springframework/samples/petclinic/owner/PetValidator.java
src/main/java/org/springframework/samples/petclinic/owner/Visit.java
src/main/java/org/springframework/samples/petclinic/owner/VisitController.java
src/main/java/org/springframework/samples/petclinic/owner/package-info.java
src/main/java/org/springframework/samples/petclinic/system/CacheConfiguration.java
src/main/java/org/springframework/samples/petclinic/system/CrashController.java
src/main/java/org/springframework/samples/petclinic/system/WebConfiguration.java
src/main/java/org/springframework/samples/petclinic/system/WelcomeController.java
src/main/java/org/springframework/samples/petclinic/system/package-info.java
src/main/java/org/springframework/samples/petclinic/vet/Specialty.java
src/main/java/org/springframework/samples/petclinic/vet/Vet.java
src/main/java/org/springframework/samples/petclinic/vet/VetController.java
src/main/java/org/springframework/samples/petclinic/vet/VetRepository.java
src/main/java/org/springframework/samples/petclinic/vet/Vets.java
src/main/java/org/springframework/samples/petclinic/vet/package-info.java
src/main/resources/application-mysql.properties
src/main/resources/application-postgres.properties
src/main/resources/application.properties
src/main/resources/banner.txt
src/main/resources/db/h2/data.sql
src/main/resources/db/h2/schema.sql
src/main/resources/db/mysql/data.sql
src/main/resources/db/mysql/petclinic_db_setup_mysql.txt
src/main/resources/db/mysql/schema.sql
src/main/resources/db/mysql/user.sql
src/main/resources/db/postgres/data.sql
src/main/resources/db/postgres/petclinic_db_setup_postgres.txt
src/main/resources/db/postgres/schema.sql
src/main/resources/messages/messages.properties
src/main/resources/messages/messages_de.properties
src/main/resources/messages/messages_en.properties
src/main/resources/messages/messages_es.properties
src/main/resources/messages/messages_fa.properties
src/main/resources/messages/messages_hi.properties
src/main/resources/messages/messages_ja.properties
src/main/resources/messages/messages_ko.properties
src/main/resources/messages/messages_pt.properties
src/main/resources/messages/messages_ru.properties
src/main/resources/messages/messages_tr.properties
src/main/resources/static/resources/css/petclinic.css
src/main/resources/static/resources/fonts/montserrat-webfont.eot
src/main/resources/static/resources/fonts/montserrat-webfont.svg
src/main/resources/static/resources/fonts/montserrat-webfont.ttf
src/main/resources/static/resources/fonts/montserrat-webfont.woff
src/main/resources/static/resources/fonts/varela_round-webfont.eot
src/main/resources/static/resources/fonts/varela_round-webfont.svg
src/main/resources/static/resources/fonts/varela_round-webfont.ttf
src/main/resources/static/resources/fonts/varela_round-webfont.woff
src/main/resources/static/resources/images/favicon.png
src/main/resources/static/resources/images/pets.png
src/main/resources/static/resources/images/spring-logo-dataflow-mobile.png
src/main/resources/static/resources/images/spring-logo-dataflow.png
src/main/resources/static/resources/images/spring-logo.svg
src/main/resources/templates/error.html
src/main/resources/templates/welcome.html
src/main/resources/templates/fragments/inputField.html
src/main/resources/templates/fragments/layout.html
src/main/resources/templates/fragments/selectField.html
src/main/resources/templates/owners/createOrUpdateOwnerForm.html
src/main/resources/templates/owners/findOwners.html
src/main/resources/templates/owners/ownerDetails.html
src/main/resources/templates/owners/ownersList.html
src/main/resources/templates/pets/createOrUpdatePetForm.html
src/main/resources/templates/pets/createOrUpdateVisitForm.html
src/main/resources/templates/vets/vetList.html
src/main/scss/header.scss
src/main/scss/petclinic.scss
src/main/scss/responsive.scss
src/main/scss/typography.scss
src/test/java/org/springframework/samples/petclinic/MySqlIntegrationTests.java
src/test/java/org/springframework/samples/petclinic/MysqlTestApplication.java
src/test/java/org/springframework/samples/petclinic/PetClinicConcurrencyTests.java
src/test/java/org/springframework/samples/petclinic/PetClinicIntegrationTests.java
src/test/java/org/springframework/samples/petclinic/PostgresIntegrationTests.java
src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java
src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java
src/test/java/org/springframework/samples/petclinic/owner/OwnerTests.java
src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java
src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java
src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java
src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java
src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java
src/test/java/org/springframework/samples/petclinic/service/EntityUtils.java
src/test/java/org/springframework/samples/petclinic/system/CrashControllerIntegrationTests.java
src/test/java/org/springframework/samples/petclinic/system/CrashControllerTests.java
src/test/java/org/springframework/samples/petclinic/system/I18nPropertiesSyncTest.java
src/test/java/org/springframework/samples/petclinic/system/WelcomeControllerTests.java
src/test/java/org/springframework/samples/petclinic/vet/VetControllerTests.java
src/test/java/org/springframework/samples/petclinic/vet/VetTests.java
src/test/jmeter/petclinic_test_plan.jmx
```
