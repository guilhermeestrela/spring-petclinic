# Responsáveis — Testes existentes e limites de verificação

## Validação do usuário — escopo de destino

🟢 Decisões confirmadas pelo usuário, sem alteração do legado. Este bloco define a intenção de destino; seções de código/testes abaixo são o retrato extraído. Pendências de produto já respondidas não devem ser reabertas por textos históricos; apenas os residuais continuam em aberto.

- 🟢 **D-01:** Visita sempre deve conter data futura. Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário.
- 🟢 **D-02:** Manter somente PostgreSQL. Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL.
- 🟢 **D-03:** Sistema somente em rede local. Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units.
- 🟢 **D-05:** São Paulo, Brasil. Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso.
- 🟢 **D-06:** Não há contrato de erro conhecido. Confirmada ausência de contrato conhecido pelo usuário. Não impor 404/409 ou body específico como regra existente; definir contrato na evolução se necessário.
- 🟢 **RESP-D-07:** Não preservar filtro ao trocar páginas. Resposta Não interpretada para a primeira pergunta direta: filtro não precisa permanecer ativo. Comportamento atual dos links pode ser preservado, sem correção automática.
- 🟢 **VET-INV-01:** Não é necessário preservar dados. Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente.
- 🟢 **SIS-D-02:** Português e inglês. Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default.
- 🟢 **SIS-D-03:** Página genérica de erro sem expor informações. Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação.

🔴 Refinamentos e evidência técnica não executada: ver [registro de decisões](../validated-decisions.md) e [lacunas finais](../gaps.md). Nenhum requisito novo foi implementado.


## Escopo e situação

🟢 CONFIRMADO — Foram lidas integralmente seis classes de teste do pacote owner, ClinicServiceTests, PetClinicConcurrencyTests e model/ValidatorTests. Nenhum teste foi executado nesta etapa. Asserções abaixo são expectativas presentes no código, não resultados observados nem cobertura medida.

🟡 INFERIDO — Correspondência com critérios CA e tarefas TT é análise documental. [requirements.md](requirements.md), [tasks.md](tasks.md) e [edge-cases.md](edge-cases.md) definem cenários adicionais; a existência de um método com nome semelhante não comprova todas as condições de um cenário.

## Infraestrutura

| Classe | Configuração confirmada | Limite |
|---|---|---|
| 🟢 OwnerControllerTests | WebMvcTest, MockMvc, MockitoBean OwnerRepository; DisabledInNativeImage e DisabledInAotMode | Não comprova persistência real |
| 🟢 PetControllerTests | WebMvcTest com PetTypeFormatter incluído, dois repositories mockados; desabilitada em native/AOT | Erros de integridade são simulados |
| 🟢 VisitControllerTests | WebMvcTest, MockMvc, repository mockado; desabilitada em native/AOT | Não comprova dados gravados |
| 🟢 OwnerTests | JUnit com objetos em memória | Não envolve binding/ORM |
| 🟢 PetTypeFormatterTests | MockitoExtension, catálogo mockado; DisabledInNativeImage | Não valida negociação HTTP |
| 🟢 PetValidatorTests | MockitoExtension, MapBindingResult; DisabledInNativeImage | Não executa regras temporais do controller |

## Inventário dos métodos e asserções

🟢 CONFIRMADO — Links apontam para a anotação @Test. Trechos de asserção são extraídos literalmente das fontes para evitar atribuir ao teste verificações que ele não faz. Blocos podem omitir preparação e chamadas intermediárias; o arquivo vinculado contém o método completo. Laços e classes Nested não multiplicam a contagem de declarações.

### OwnerControllerTests.java — 18 declarações

🟢 [initCreationForm](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L106)

```java
.andExpect(status().isOk())
.andExpect(model().attributeExists("owner"))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [processCreationFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L114)

```java
.andExpect(status().is3xxRedirection());
```

🟢 [processCreationFormHasErrors](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L125)

```java
.andExpect(status().isOk())
.andExpect(model().attributeHasErrors("owner"))
.andExpect(model().attributeHasFieldErrors("owner", "address"))
.andExpect(model().attributeHasFieldErrors("owner", "telephone"))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [processCreationFormRejectsCityLongerThanColumn](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L136)

```java
.andExpect(status().isOk())
.andExpect(model().attributeHasErrors("owner"))
.andExpect(model().attributeHasFieldErrors("owner", "city"))
.andExpect(model().attributeHasFieldErrorCode("owner", "city", "Size"))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [processCreationFormRejectsAddressLongerThanColumn](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L151)

```java
.andExpect(status().isOk())
.andExpect(model().attributeHasErrors("owner"))
.andExpect(model().attributeHasFieldErrors("owner", "address"))
.andExpect(model().attributeHasFieldErrorCode("owner", "address", "Size"))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [initFindForm](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L166)

```java
.andExpect(status().isOk())
.andExpect(model().attributeExists("owner"))
.andExpect(view().name("owners/findOwners"));
```

🟢 [processFindFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L174)

```java
mockMvc.perform(get("/owners?page=1")).andExpect(status().isOk()).andExpect(view().name("owners/ownersList"));
```

🟢 [processFindFormRedirectsOutOfBoundsPageToFirstPage](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L181)

```java
.andExpect(status().is3xxRedirection())
.andExpect(redirectedUrl("/owners?page=1&lastName=Franklin"));
```

🟢 [processFindFormByLastName](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L190)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/" + TEST_OWNER_ID));
```

🟢 [processFindFormIgnoresSurroundingWhitespace](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L199)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/" + TEST_OWNER_ID));
verify(this.owners, times(3)).findByLastNameStartingWith(eq("Franklin"), any(Pageable.class));
```

🟢 [processFindFormWithWhitespaceOnlyLastNameReturnsAllOwners](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L213)

```java
.andExpect(status().isOk())
.andExpect(view().name("owners/ownersList"));
verify(this.owners).findByLastNameStartingWith(eq(""), any(Pageable.class));
```

🟢 [processFindFormNoOwnersFound](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L225)

```java
.andExpect(status().isOk())
.andExpect(model().attributeHasFieldErrors("owner", "lastName"))
.andExpect(model().attributeHasFieldErrorCode("owner", "lastName", "notFound"))
.andExpect(view().name("owners/findOwners"));
```

🟢 [initUpdateOwnerForm](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L237)

```java
.andExpect(status().isOk())
.andExpect(model().attributeExists("owner"))
.andExpect(model().attribute("owner", hasProperty("lastName", is("Franklin"))))
.andExpect(model().attribute("owner", hasProperty("firstName", is("George"))))
.andExpect(model().attribute("owner", hasProperty("address", is("110 W. Liberty St."))))
.andExpect(model().attribute("owner", hasProperty("city", is("Madison"))))
.andExpect(model().attribute("owner", hasProperty("telephone", is("6085551023"))))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [processUpdateOwnerFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L250)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processUpdateOwnerFormUnchangedSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L262)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processUpdateOwnerFormHasErrors](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L269)

```java
.andExpect(status().isOk())
.andExpect(model().attributeHasErrors("owner"))
.andExpect(model().attributeHasFieldErrors("owner", "address"))
.andExpect(model().attributeHasFieldErrors("owner", "telephone"))
.andExpect(view().name("owners/createOrUpdateOwnerForm"));
```

🟢 [showOwner](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L283)

```java
.andExpect(status().isOk())
.andExpect(model().attribute("owner", hasProperty("lastName", is("Franklin"))))
.andExpect(model().attribute("owner", hasProperty("firstName", is("George"))))
.andExpect(model().attribute("owner", hasProperty("address", is("110 W. Liberty St."))))
.andExpect(model().attribute("owner", hasProperty("city", is("Madison"))))
.andExpect(model().attribute("owner", hasProperty("telephone", is("6085551023"))))
.andExpect(model().attribute("owner", hasProperty("pets", not(empty()))))
.andExpect(model().attribute("owner",
hasProperty("pets", hasItem(hasProperty("visits", hasSize(greaterThan(0)))))))
.andExpect(view().name("owners/ownerDetails"));
```

🟢 [processUpdateOwnerFormWithIdMismatch](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerControllerTests.java#L298)

```java
.andExpect(status().is3xxRedirection())
.andExpect(redirectedUrl("/owners/" + pathOwnerId + "/edit"))
.andExpect(flash().attributeExists("error"));
```

### OwnerTests.java — 2 declarações

🟢 [addPetAddsPersistedPet](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerTests.java#L25)

```java
assertTrue(owner.getPets().contains(pet));
assertEquals(1, owner.getPets().size());
```

🟢 [addPetDoesNotAddDuplicatePet](../../src/test/java/org/springframework/samples/petclinic/owner/OwnerTests.java#L38)

```java
assertEquals(1, owner.getPets().size());
```

### PetControllerTests.java — 14 declarações

🟢 [initCreationForm](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L89)

```java
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"))
.andExpect(model().attributeExists("pet"));
```

🟢 [processCreationFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L97)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processCreationFormWithBlankName](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L110)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "required"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processCreationFormWithDuplicateName](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L123)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "duplicate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processCreationFormWithMissingPetType](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L136)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "type"))
.andExpect(model().attributeHasFieldErrorCode("pet", "type", "required"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processCreationFormWithInvalidBirthDate](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L149)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "birthDate"))
.andExpect(model().attributeHasFieldErrorCode("pet", "birthDate", "typeMismatch.birthDate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processCreationFormWithDataIntegrityViolation](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L165)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "duplicate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [initUpdateForm](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L181)

```java
.andExpect(status().isOk())
.andExpect(model().attributeExists("pet"))
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processUpdateFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L191)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processUpdateFormWithSameName](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L201)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processUpdateFormWithDuplicateName](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L217)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "duplicate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processUpdateFormWithInvalidBirthDate](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L232)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "birthDate"))
.andExpect(model().attributeHasFieldErrorCode("pet", "birthDate", "typeMismatch"))
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processUpdateFormWithBlankName](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L244)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "required"))
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

🟢 [processUpdateFormWithDataIntegrityViolation](../../src/test/java/org/springframework/samples/petclinic/owner/PetControllerTests.java#L256)

```java
.andExpect(model().attributeHasNoErrors("owner"))
.andExpect(model().attributeHasErrors("pet"))
.andExpect(model().attributeHasFieldErrors("pet", "name"))
.andExpect(model().attributeHasFieldErrorCode("pet", "name", "duplicate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdatePetForm"));
```

### PetTypeFormatterTests.java — 3 declarações

🟢 [testPrint](../../src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java#L55)

```java
assertThat(petTypeName).isEqualTo("Hamster");
```

🟢 [shouldParse](../../src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java#L63)

```java
assertThat(petType.getName()).isEqualTo("Bird");
```

🟢 [shouldThrowParseException](../../src/test/java/org/springframework/samples/petclinic/owner/PetTypeFormatterTests.java#L70)

```java
Assertions.assertThrows(ParseException.class, () -> {
petTypeFormatter.parse("Fish", Locale.ENGLISH);
```

### PetValidatorTests.java — 7 declarações

🟢 [supportsPetClass](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L65)

```java
assertTrue(petValidator.supports(Pet.class));
```

🟢 [doesNotSupportNonPetClass](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L70)

```java
assertFalse(petValidator.supports(String.class));
```

🟢 [validate](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L75)

```java
assertFalse(errors.hasErrors());
```

🟢 [validateWithInvalidPetName](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L90)

```java
assertTrue(errors.hasFieldErrors("name"));
```

🟢 [validateWithInvalidPetType](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L102)

```java
assertTrue(errors.hasFieldErrors("type"));
```

🟢 [validateWithInvalidBirthDate](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L113)

```java
assertTrue(errors.hasFieldErrors("birthDate"));
```

🟢 [validateWithLongPetName](../../src/test/java/org/springframework/samples/petclinic/owner/PetValidatorTests.java#L125)

```java
assertTrue(errors.hasFieldErrors("name"));
```

### VisitControllerTests.java — 5 declarações

🟢 [initNewVisitForm](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L68)

```java
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdateVisitForm"));
```

🟢 [processNewVisitFormSuccess](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L75)

```java
.andExpect(status().is3xxRedirection())
.andExpect(view().name("redirect:/owners/{ownerId}"));
```

🟢 [processNewVisitFormHasErrors](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L86)

```java
.andExpect(model().attributeHasErrors("visit"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdateVisitForm"));
```

🟢 [processNewVisitFormHasErrorsWhenVisitDateIsNotInFuture](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L96)

```java
.andExpect(model().attributeHasFieldErrors("visit", "date"))
.andExpect(model().attributeHasFieldErrorCode("visit", "date", "typeMismatch.visitDate"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdateVisitForm"));
```

🟢 [processNewVisitFormHasErrorsWhenDescriptionIsTooLong](../../src/test/java/org/springframework/samples/petclinic/owner/VisitControllerTests.java#L109)

```java
.andExpect(model().attributeHasFieldErrors("visit", "description"))
.andExpect(status().isOk())
.andExpect(view().name("pets/createOrUpdateVisitForm"));
```

🟢 CONFIRMADO — Total: **49 declarações @Test em seis arquivos do pacote owner**. Isso não equivale a asserções, casos executados ou percentual de cobertura.

## O que os testes próprios demonstram e o que não demonstram

- 🟢 **OwnerControllerTests:** cadastro válido verifica apenas 3xx; cadastro inválido verifica campos/Size e formulário. Busca testa lista, vazio, resultado único, strip e páginas 0/2 com URL exata preservando filtro. Edição sem campos novos espera sucesso usando objeto carregado. Detalhe verifica dados no model e pets/visits presentes. Mismatch é montado com repository divergente e flashAttr; não prova substituição de ID por campo HTTP. Não há verificação explícita de save/never save nesses métodos.
- 🟢 **OwnerTests:** adiciona pet persistido e adiciona a mesma instância duas vezes. Não testa duas instâncias com mesmo ID, null ou dois objetos novos distintos.
- 🟢 **PetControllerTests:** cobre criação/edição válidas, nome branco/duplicado, tipo ausente, nascimento futuro na criação, formato inválido na edição e mensagem de duplicidade mockada. O teste de data inválida na edição usa 2015/02/12 e verifica typeMismatch: não é CA-20 de nascimento futuro. Alguns testes omitem tipo e verificam um campo específico, sem provar exclusividade do erro. initUpdateForm está aninhado na classe de erros de criação, embora seja GET de edição. Os testes de sucesso não verificam campos efetivamente gravados.
- 🟢 **PetTypeFormatterTests:** imprime Hamster, converte Bird de catálogo Dog/Bird e lança ParseException para Fish ausente. Não testa nome null no print nem variante de caixa do mesmo nome.
- 🟢 **PetValidatorTests:** supports para Pet/String; objeto válido; nome vazio, tipo null em novo, nascimento null e nome de 31 caracteres. As falhas verificam presença de erro no campo, sem afirmar o código de rejeição. Não testa tipo null em persistido ou fronteira exata de 30.
- 🟢 **VisitControllerTests:** GET verifica view/status; sucesso com amanhã verifica redirect; hoje verifica date/typeMismatch.visitDate; descrição de 256 verifica erro de campo. POST genérico inválido omite descrição e data, portanto não resolve D-01 sobre data vazia/omitida com descrição válida. Não verifica amanhã/minVisitDate do model, vínculo inválido ou chamadas de save.

## Testes transversais

### ClinicServiceTests — persistência

🟢 CONFIRMADO — Usa DataJpaTest e AutoConfigureTestDatabase(replace=NONE), com OwnerRepository, PetTypeRepository e VetRepository injetados. Consultas usam Pageable.unpaged; não caracterizam página de 5 do controller. Métodos de escrita possuem Transactional; não se executou rollback nesta análise. O nome da classe não comprova existência de camada service em produção.

🟢 [ClinicServiceTests.java](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java) — 12 declarações:

- 🟢 [shouldFindOwnersByLastName](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L89).
- 🟢 [shouldFindSingleOwnerWithPet](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L98).
- 🟢 [shouldInsertOwner](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L109).
- 🟢 [shouldUpdateOwner](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L128).
- 🟢 [shouldFindAllPetTypes](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L147).
- 🟢 [shouldInsertPetIntoDatabaseAndGenerateId](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L157).
- 🟢 [shouldUpdatePetName](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L185).
- 🟢 [shouldFindVets](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L206).
- 🟢 [shouldAddNewVisitForPet](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L217).
- 🟢 [shouldFindVisitsByPetId](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L237).
- 🟢 [shouldFailToInsertDuplicatePetNameForSameOwner](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L253).
- 🟢 [shouldAllowSamePetNameForDifferentOwners](../../src/test/java/org/springframework/samples/petclinic/service/ClinicServiceTests.java#L279).

🟢 [PetClinicConcurrencyTests.java](../../src/test/java/org/springframework/samples/petclinic/PetClinicConcurrencyTests.java) — 1 declarações:

- 🟢 [testDuplicatePetNameRaceConditionIsBlocked](../../src/test/java/org/springframework/samples/petclinic/PetClinicConcurrencyTests.java#L40).

🟢 [ValidatorTests.java](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java) — 2 declarações:

- 🟢 [shouldNotValidateWhenFirstNameEmpty](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L48).
- 🟢 [shouldNotValidateWhenLastNameEmpty](../../src/test/java/org/springframework/samples/petclinic/model/ValidatorTests.java#L64).

🟢 CONFIRMADO — ClinicServiceTests tem 12 métodos, 11 relacionados a responsáveis/tipos/pets/visitas e um a veterinários. Testa prefixo Davis/Daviss, seed de Owner e tipos, inserção/edição de Owner/Pet, visitas e unicidade. A duplicidade usa nomes com diferença de caixa e espera DataIntegrityViolationException no flush, sem validar mensagem. Permite nomes equivalentes para responsáveis diferentes. Consultas de atualização ocorrem no mesmo contexto sem clear explícito; não afirmar round trip independente. shouldFindVets pertence à unit veterinarios.

### Concorrência HTTP — PetClinicConcurrencyTests

🟢 CONFIRMADO — SpringBootTest(RANDOM_PORT), repository e RestTemplate; um teste envia dois POSTs concorrentes com nome ConcurrencyTestPet para owner 1/tipo cat. Latches coordenam o início e aguardam até 10 segundos para prontidão e conclusão. Depende de seed e de ausência prévia do nome. Não há cleanup explícito nem Transactional de classe/método.

🟢 CONFIRMADO — Classifica sucesso como resposta 2xx com body null ou sem texto “is already in use”; outros resultados/exceções contam como falha. Afirma successCount=1, total de pets anterior+1 e um nome equivalente ignorando caixa. failureCount é impresso, sem assert de igualdade a 1.

🟡 INFERIDO — Classificador depende de texto/idioma e do comportamento de redirect do cliente; falha contada não prova o motivo esperado. Asserções do banco complementam o sinal. 🔴 Não há execução por perfil, comprovação de mensagem MySQL ou garantia medida de simultaneidade no flush nesta extração.

### Validação herdada — model/ValidatorTests

🟢 CONFIRMADO — LocalValidatorFactoryBean e Locale.ENGLISH; dois testes verificam firstName/lastName vazios com uma violação, caminho correto e mensagem “must not be blank”. Não testam Owner completo, limites 30/31 ou HTTP.

## Correspondência e lacunas

| Área / cenários | Evidência no código de teste | Validação adicional proposta |
|---|---|---|
| 🟢 CA-01/02/09/10 | Cadastro/edição e campos inválidos | 🟡 TT-01: fronteiras completas e chamadas de persistência |
| 🟢 CA-03–07 | Busca/strip/lista/vazio/único e detalhe | 🟡 TT-02: Pageable e model completos; 🔴 filtro nos links RESP-D-07 |
| 🟢 CA-11 | Fixture de mismatch via MockMvc | 🟡 TT-06: distinguir de CA-25 com campos suprimidos |
| 🟢 CA-12/13 | Bird válido e Fish ausente | 🟡 TT-03: variante só de caixa e print null |
| 🟢 CA-14–19 | Fluxos de pet e erro SQL mockado | 🔴 D-02/TT-07: mensagem/collation reais por perfil |
| 🔴 CA-20 | Futuro na edição não isolado no teste de formato inválido | 🟡 TT-03/08: testar futuro na edição separadamente |
| 🟢 CA-21/22/23 | View GET, amanhã válido e hoje inválido | 🟡 TT-05: model/persistência; 🔴 D-01/TT-08: data vazia/omitida |
| 🔴 CA-08/24/25 | Ausência de Owner, vínculo inválido e supressão de IDs não comprovados pelos testes próprios lidos | 🟡 TT-06/08: caracterizar sem inventar status HTTP |
| 🟢 EC-11/18 | Mesma instância de Pet é testada; Set de Visit é regra de código | 🟡 TT-04/05: identidades distintas e repetição HTTP |
| 🔴 EC-17/20/21 | Fuso, script de feedback e custo EAGER não medidos | 🟡 Caracterizar conforme design/edge-cases antes de propor correções |

🟡 INFERIDO — As lacunas desta tabela referem-se às nove classes lidas, não à ausência universal de testes em todo repositório. Testes adicionais devem ser escolhidos conforme risco/mudança, sem repetir a implementação por rotina.

## Situação da validação

🟢 CONFIRMADO — Foram verificados inventário de métodos, referências locais e checkpoint documental. Não se executou Maven/Gradle, servidor HTTP, Docker/Testcontainers ou navegador. Nenhum resultado de aprovação foi presumido.

🟡 ORIENTAÇÃO — Em futura execução, registrar comando, perfil, ambiente, resultado e limites. replace=NONE pode usar base do perfil; identificar o ambiente antes de executar. Contagem de métodos ou um perfil aprovado não encerram D-01/02/03/05/06. A política de produto permanece registrada em [questions.md](questions.md).
