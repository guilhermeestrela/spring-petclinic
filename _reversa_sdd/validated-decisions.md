# Decisões validadas pelo usuário

Processadas em 2026-11-09T23:33:55.027602+00:00, a partir das 14 respostas de [questions.md](questions.md).

🟢 **CONFIRMADO PELO USUÁRIO** significa requisito/contexto informado, não comportamento já implementado ou teste executado. O código legado e os diagramas de extração continuam retratando o estado atual. As decisões abaixo orientam o destino/evolução; não autorizam implementação, deploy ou exclusão de dados nesta sessão.

As seções de lacunas anteriores nas specs preservam o histórico da descoberta. Para intenção de produto e escopo de destino, este registro e os blocos de validação das units têm precedência; pendências técnicas continuam marcadas explicitamente.

| ID | Decisão confirmada | Aplicação / interpretação | Residual técnico ou refinamento |
|---|---|---|---|
| D-01 | 🟢 Visita sempre deve conter data futura. | Requisito desejado confirmado; rejeitar data null/vazia e hoje/passado no destino. Padrão amanhã pode continuar na abertura, sem dispensar validação. Omissão que mantém o default não comprova confirmação explícita de data pelo usuário. | 🔴 Binding atual e forma exata de entrada omitida ainda não executados. |
| D-02 | 🟢 Manter somente PostgreSQL. | Escopo de banco do destino é PostgreSQL; H2/MySQL permanecem evidência histórica, não obrigação futura. Preservar a regra extraída de unicidade por responsável sem assumir necessidade de equivalência MySQL. | 🔴 Confirmar tratamento de violação e concorrência no PostgreSQL; nenhum dado foi removido. |
| D-03 | 🟢 Sistema somente em rede local. | Contexto de uso em rede local informado pelo usuário. Não inferir autenticação, papéis, ausência de ameaças ou configuração real de ACL/proxy. Aplicar contexto às operações de todas as units. | 🔴 Exposição e restrições concretas de /oups, Actuator e probes no cluster não foram informadas. |
| D-05 | 🟢 São Paulo, Brasil. | Adotar America/Sao_Paulo como identificador técnico correspondente ao local informado. Não usar offset UTC fixo nem inferir que JVM atual já está nesse fuso. | 🔴 Referência única de dia por requisição não foi decidida; implementação temporal atual não alterada. |
| D-06 | 🟢 Não há contrato de erro conhecido. | Confirmada ausência de contrato conhecido pelo usuário. Não impor 404/409 ou body específico como regra existente; definir contrato na evolução se necessário. | 🔴 Status/conteúdo efetivos ainda exigem caracterização quando relevantes. |
| RESP-D-07 | 🟢 Não preservar filtro ao trocar páginas. | Resposta Não interpretada para a primeira pergunta direta: filtro não precisa permanecer ativo. Comportamento atual dos links pode ser preservado, sem correção automática. | 🔴 A resposta não define outras mudanças de navegação; interpretação explicitada para futura revisão. |
| D-04 | 🟢 Não há política de cache. | Confirmada ausência de política operacional a preservar. Não significa remover cache nem definir TTL=0; manter declaração atual e escolher política em evolução se necessária. | 🔴 CacheManager efetivo, capacidade, expiração e invalidação não comprovados. |
| VET-D-01 | 🟢 Somente a aplicação consome; campos desnecessários podem ser removidos numa refatoração. | Não há consumidor externo informado. Simplificação de campos é permitida no planejamento de refatoração após verificar uso; nenhum campo concreto foi declarado desnecessário nem removido. | 🔴 Shape completo, campos usados e necessidade de XML não determinados pela resposta. |
| VET-D-02 | 🟢 Não há contrato de erro definido. | Não há contrato conhecido a preservar para falhas da consulta; não atribuir status novo como comportamento do legado. | 🔴 Conversão/erros de banco/cache continuam não executados. |
| VET-D-03 | 🟢 Ordem alfabética. | Ordenação alfabética passa a ser requisito desejado confirmado para veterinários; é diferença em relação a findAll/PageRequest sem Sort explícito no legado. | 🔴 Campo principal (nome/sobrenome), desempate, collation e consistência entre HTML/body ainda precisam de definição no ciclo de evolução; não inventados. |
| VET-INV-01 | 🟢 Não é necessário preservar dados. | Dispensada migração/preservação de dados históricos no destino. Tarefas de migração de domínio deixam de ser obrigatórias; permite planejamento de instalação nova, sem autorização de exclusão de base existente. | 🔴 Integridade de novos dados e política para Specialty null continuam dependentes de implementação; dispensa de migração não define tratamento de null. |
| SIS-D-02 | 🟢 Português e inglês. | Idiomas requeridos do destino: português e inglês. Outros bundles do legado não são obrigatórios por esta resposta; não removidos. Default inglês do código fica como referência atual, sem escolha nova de default. | 🔴 Fallback, lang inválido, variante regional e duração desejada de preferência não respondidos. |
| SIS-D-03 | 🟢 Página genérica de erro sem expor informações. | Requisito desejado confirmado: página genérica sem detalhes internos de exceção/configuração. Override ALWAYS e mensagem demonstrativa dos testes são históricos, não alvo de implantação. | 🔴 Status/código e eventual body de erro da API não foram definidos; detalhes podem ser registrados em logs operacionais apropriados sem serem expostos na página. |
| SIS-D-04 | 🟢 Kubernetes; Docker local se possível. | Destino prioritário é Kubernetes. Docker para desenvolvimento local é preferência condicional a viabilidade, não promessa de ambiente pronto ou deploy autorizado. | 🔴 Imagem/tag/digest, service binding, probes e desenho do ambiente local ainda não validados; suporte nativo não foi exigido. |

## Critérios de destino adicionais

🟢 Estes critérios refletem as respostas e não foram executados. As divergências em relação ao código atual são intencionais e devem ser implementadas apenas em ciclo forward autorizado.

```gherkin
Cenário VAL-01: Data de visita obrigatória
Dado um formulário de visita com data vazia ou null e descrição válida
Quando o destino valida a visita
Então rejeita a ausência de data e não salva visita sem uma data futura

Cenário VAL-02: Banco de destino
Dado um ambiente da versão de destino
Quando a persistência é configurada
Então PostgreSQL é o banco suportado exigido, sem obrigação de manter os perfis H2 e MySQL

Cenário VAL-03: Erro genérico na interface
Dado uma falha interna no destino
Quando a página de erro é apresentada ao usuário
Então mostra mensagem genérica sem detalhes internos da exceção ou configuração

Cenário VAL-04: Ordem desejada de veterinários
Dado uma lista de veterinários no destino e o critério alfabético refinado no plano de evolução
Quando a lista é consultada
Então segue a ordem alfabética definida, distinguindo esse requisito do legado sem Sort explícito
```

## Limites preservados

- 🟢 Rede local não equivale a login/papéis ou controle de acesso comprovado.
- 🟢 Sem política de cache não equivale a cache desabilitado.
- 🟢 Dispensa de migração não autoriza apagar dados existentes.
- 🟢 Permissão para remover campos desnecessários não identifica automaticamente quais campos podem ser retirados.
- 🔴 Fallback de idioma, parâmetros completos de ordenação, imagem de implantação e respostas HTTP de falha permanecem refinamentos para o ciclo de evolução; não impedem concluir a extração com lacunas declaradas.

## Decisão adicional de destino — TARGET-STACK-01

🟢 Confirmado pelo usuário na conversa: a reescrita usará **Next.js em um monorepo Nx**. O fork informado é https://github.com/guilhermeestrela/spring-petclinic (referência fornecida pelo usuário, sem verificação remota nesta atualização).

Esta decisão complementa as 14 respostas anteriores. PostgreSQL, Kubernetes, preferência por Docker local e demais regras validadas continuam aplicáveis. A análise Java/Spring/Thymeleaf, os diagramas C4 e os contratos extraídos descrevem o legado; não constituem o desenho técnico da reescrita.

O planejamento de destino deverá definir a divisão de aplicações e bibliotecas Nx, responsabilidades de interface e servidor, persistência PostgreSQL, contratos, testes e pipeline de build/implantação. Versões, gerenciador de pacotes, ORM, uso de API separada e estratégia de renderização ainda não foram escolhidos. Next.js e Nx, isoladamente, não determinam essas escolhas. As tarefas ligadas a JPA, Thymeleaf, Maven/Gradle e Spring devem ser traduzidas para o destino antes da implementação, preservando regras e critérios funcionais aplicáveis.
