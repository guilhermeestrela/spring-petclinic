# Dependências — spring-petclinic

🟢 CONFIRMADO — Versões declaradas em `pom.xml`, `build.gradle` e wrappers. Não foi resolvida a árvore transitiva; versões gerenciadas não foram presumidas.

## Plataforma e ferramentas

| Componente | Versão declarada |
|---|---|
| Java | 17 |
| Spring Boot (Maven e Gradle) | 4.1.0 |
| Aplicação | 4.0.0-SNAPSHOT |
| Maven Wrapper / Maven | 3.3.4 / 3.9.16 |
| Gradle | 9.5.1 |
| Dependency Management Gradle | 1.1.7 |
| GraalVM Build Tools Gradle | 1.1.2 |
| CycloneDX Gradle | 3.2.4 |

## Dependências diretas Maven

| Artefato | Versão | Escopo |
|---|---|---|
| org.springframework.boot:spring-boot-starter-actuator | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-cache | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-data-jpa | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-thymeleaf | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-validation | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-webmvc | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| javax.cache:cache-api | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| jakarta.xml.bind:jakarta.xml.bind-api | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| com.h2database:h2 | gerenciada pelo parent/BOM Spring Boot 4.1.0 | runtime |
| com.github.ben-manes.caffeine:caffeine | gerenciada pelo parent/BOM Spring Boot 4.1.0 | runtime |
| com.mysql:mysql-connector-j | gerenciada pelo parent/BOM Spring Boot 4.1.0 | runtime |
| org.postgresql:postgresql | gerenciada pelo parent/BOM Spring Boot 4.1.0 | runtime |
| org.webjars:webjars-locator-lite | 1.1.3 | runtime |
| org.webjars.npm:bootstrap | 5.3.8 | runtime |
| org.webjars.npm:font-awesome | 4.7.0 | runtime |
| org.springframework.boot:spring-boot-devtools | gerenciada pelo parent/BOM Spring Boot 4.1.0 | compile |
| org.springframework.boot:spring-boot-starter-data-jpa-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-restclient | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-restclient-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-thymeleaf-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-validation-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-webmvc-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-actuator-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-testcontainers | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-docker-compose | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.springframework.boot:spring-boot-starter-cache-test | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.testcontainers:testcontainers-junit-jupiter | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |
| org.testcontainers:testcontainers-mysql | gerenciada pelo parent/BOM Spring Boot 4.1.0 | test |

## Propriedades de ferramentas Maven

| Propriedade | Valor |
|---|---|
| java.version | 17 |
| webjars-locator.version | 1.1.3 |
| webjars-bootstrap.version | 5.3.8 |
| webjars-font-awesome.version | 4.7.0 |
| checkstyle.version | 12.3.1 |
| jacoco.version | 0.8.15 |
| libsass.version | 0.3.4 |
| lifecycle-mapping | 1.0.0 |
| maven-checkstyle.version | 3.6.0 |
| nohttp-checkstyle.version | 0.0.11 |
| spring-format.version | 0.0.47 |

## Gradle e serviços

Gradle declara os mesmos principais componentes web, JPA, cache e drivers; os escopos e starters de teste diferem do Maven. A configuração efetiva de ambos não foi executada para avaliar equivalência.

Docker Compose: `mysql:9.7`, `postgres:18.4`. Kubernetes: `postgres:18.4` e `dsyer/petclinic` sem tag explícita.

🔴 LACUNA — Dependências transitivas, compatibilidade em execução e vulnerabilidades não avaliadas nesta fase.
