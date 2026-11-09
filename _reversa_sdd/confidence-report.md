# Relatório de confiança — spring-petclinic

Revisão documental encerrada em 2026-11-09T23:35:26.356360+00:00. Quatro units, 12 canônicos, 15 opcionais e três globais gerados pelo Redator; 14 respostas recebidas/processadas. Nenhum arquivo legado foi alterado e nenhum teste de aplicação ou deploy foi executado.

## Resumo Geral

| Nível | Blocos anotados | Percentual |
|---|---:|---:|
| 🟢 CONFIRMADO | 969 | 61.0% |
| 🟡 INFERIDO | 266 | 16.8% |
| 🔴 LACUNA | 353 | 22.2% |
| Total | 1588 | 100% |

**Índice editorial de confiança: 69.4%**, pela fórmula (verde + metade do amarelo) / total. Não é probabilidade de correção, cobertura de código/testes ou percentual do sistema validado em execução.

### Método e limites da contagem

Contados os 30 arquivos do plano do Redator após incorporar as decisões. Em Markdown: um parágrafo/bloco anotado ou uma linha de tabela anotada; blocos de código são excluídos. Numa unidade com múltiplas cores, vale a mais conservadora. No OpenAPI em JSON/YAML 1.2, cada description anotada é uma unidade. Não há deduplicação semântica entre arquivos; repetições e blocos históricos entram na contagem. Blocos de decisão 🟢 são confirmação do usuário, distintos de implementação comprovada no código. Esta é uma métrica editorial reproduzível e conservadora, não uma contagem perfeita de afirmações independentes.

## Por Spec

| Spec | 🟢 | 🟡 | 🔴 | Índice |
|---|---:|---:|---:|---:|
| [responsaveis/requirements.md ](responsaveis/requirements.md) | 35 | 7 | 4 | 83.7% |
| [responsaveis/design.md ](responsaveis/design.md) | 47 | 5 | 19 | 69.7% |
| [responsaveis/tasks.md ](responsaveis/tasks.md) | 22 | 12 | 16 | 56.0% |
| [responsaveis/contracts.md ](responsaveis/contracts.md) | 61 | 11 | 12 | 79.2% |
| [responsaveis/edge-cases.md ](responsaveis/edge-cases.md) | 16 | 5 | 19 | 46.2% |
| [responsaveis/questions.md ](responsaveis/questions.md) | 9 | 10 | 7 | 53.8% |
| [responsaveis/legacy-mapping.md ](responsaveis/legacy-mapping.md) | 46 | 22 | 7 | 76.0% |
| [responsaveis/tests.md ](responsaveis/tests.md) | 73 | 7 | 8 | 86.9% |
| [veterinarios/requirements.md ](veterinarios/requirements.md) | 26 | 6 | 5 | 78.4% |
| [veterinarios/design.md ](veterinarios/design.md) | 45 | 0 | 11 | 80.4% |
| [veterinarios/tasks.md ](veterinarios/tasks.md) | 11 | 8 | 17 | 41.7% |
| [veterinarios/contracts.md ](veterinarios/contracts.md) | 36 | 4 | 14 | 70.4% |
| [veterinarios/edge-cases.md ](veterinarios/edge-cases.md) | 15 | 3 | 16 | 48.5% |
| [veterinarios/questions.md ](veterinarios/questions.md) | 10 | 10 | 6 | 57.7% |
| [veterinarios/legacy-mapping.md ](veterinarios/legacy-mapping.md) | 38 | 8 | 7 | 79.2% |
| [modelo/requirements.md ](modelo/requirements.md) | 30 | 6 | 3 | 84.6% |
| [modelo/design.md ](modelo/design.md) | 42 | 1 | 2 | 94.4% |
| [modelo/tasks.md ](modelo/tasks.md) | 10 | 11 | 13 | 45.6% |
| [modelo/edge-cases.md ](modelo/edge-cases.md) | 18 | 5 | 9 | 64.1% |
| [modelo/legacy-mapping.md ](modelo/legacy-mapping.md) | 32 | 9 | 3 | 83.0% |
| [sistema/requirements.md ](sistema/requirements.md) | 36 | 13 | 5 | 78.7% |
| [sistema/design.md ](sistema/design.md) | 44 | 1 | 14 | 75.4% |
| [sistema/tasks.md ](sistema/tasks.md) | 10 | 8 | 20 | 36.8% |
| [sistema/contracts.md ](sistema/contracts.md) | 49 | 5 | 17 | 72.5% |
| [sistema/edge-cases.md ](sistema/edge-cases.md) | 10 | 2 | 22 | 32.4% |
| [sistema/questions.md ](sistema/questions.md) | 9 | 7 | 6 | 56.8% |
| [sistema/legacy-mapping.md ](sistema/legacy-mapping.md) | 43 | 9 | 7 | 80.5% |
| [openapi/petclinic.yaml ](openapi/petclinic.yaml) | 31 | 34 | 54 | 40.3% |
| [user-stories/operacao-clinica.md ](user-stories/operacao-clinica.md) | 21 | 21 | 10 | 60.6% |
| [traceability/code-spec-matrix.md ](traceability/code-spec-matrix.md) | 94 | 16 | 0 | 92.7% |

## Respostas e reclassificações

As 14 respostas estão preservadas em [questions.md](questions.md); interpretação/efeito detalhados em [validated-decisions.md](validated-decisions.md). Não houve promoção de runtime desconhecido a comprovado.

| Item antes aberto | Situação após resposta | Origem |
|---|---|---|
| D-01 | 🟢 Visita sempre deve conter data futura. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| D-02 | 🟢 Manter somente PostgreSQL. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| D-03 | 🟢 Sistema somente em rede local. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| D-05 | 🟢 São Paulo, Brasil. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| D-06 | 🟢 Não há contrato de erro conhecido. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| RESP-D-07 | 🟢 Não preservar filtro ao trocar páginas. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| D-04 | 🟢 Não há política de cache. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| VET-D-01 | 🟢 Somente a aplicação consome; campos desnecessários podem ser removidos numa refatoração. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| VET-D-02 | 🟢 Não há contrato de erro definido. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| VET-D-03 | 🟢 Ordem alfabética. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| VET-INV-01 | 🟢 Não é necessário preservar dados. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| SIS-D-02 | 🟢 Português e inglês. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| SIS-D-03 | 🟢 Página genérica de erro sem expor informações. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |
| SIS-D-04 | 🟢 Kubernetes; Docker local se possível. 🔴 Residual técnico/refinamento mantido no registro quando aplicável. | Usuário, pergunta consolidada correspondente |

🟢 Histórico adicional de correções: R-01 delimitou Dockerfile de aplicação versus Dockerfile de desenvolvimento/Gitpod; R-02 expandiu referência compacta de requisito para rastreabilidade. As decisões humanas acrescentam requisitos/contexto de destino e preservam os trechos históricos como extração, sem reescrever o código.

## Lacunas Pendentes

[gaps.md](gaps.md) lista apenas refinamentos/verificações ainda abertos, categorizados por severidade proposta. Priorizar: integridade de unicidade PostgreSQL; proteção efetiva do cluster local; forma concreta de ordenação alfabética; representação/erros HTTP; cache/fallback de idioma e artefato de implantação. Sem contrato definido não se inventou status; sem política de cache não se removeu cache.

As 14 perguntas foram respondidas. Uma resposta pode fechar a intenção de produto e deixar um detalhe técnico não executado: isso é conclusão da extração com limitações explícitas, não aprovação de implementação. Não há obrigação de preservar bancos H2/MySQL ou migrar dados históricos do legado conforme o escopo informado.

## Validações realizadas

- Presença de quatro units e canônicos, IDs de requisitos em design/tasks e conclusão dos 30 arquivos do plano.
- OpenAPI corresponde a 17 operações dos controllers; JSON válido como subconjunto YAML 1.2, refs e parâmetros verificados. Sem validador OpenAPI externo ou chamada de endpoints.
- Matriz enumera 132 arquivos rastreados, 101 associados e 31 n/a; 76,5% é associação de arquivo, não cobertura semântica/de teste.
- Referências Markdown locais, cercas de código e persistência dos checkpoints verificadas. Diagramas Mermaid não renderizados visualmente.
- Nenhuma alteração dos arquivos rastreados do legado. Política allowLegacyEdits continua false e não foi modificada.

## Recomendações

- Usar requisitos/decisões validadas como entrada para ciclo forward, distinguindo evolução de comportamento antigo.
- Refinar campo/desempate da ordem alfabética e contrato de erro/fallback quando a feature correspondente for planejada; não exigir esses detalhes para fingir certeza na extração.
- Implementar data futura obrigatória, PostgreSQL e erro genérico apenas no ciclo autorizado; hoje são decisões documentadas, não código entregue.
- Planejar Kubernetes e avaliar Docker local conforme preferência. Não presumir imagem externa correspondente ao checkout.
- Escolher verificações de runtime proporcionais às mudanças; não executar seeds/DDL em dados existentes por rotina.

## Estado final

Revisão concluída com limitações documentadas. Relatório não declara testes de aplicação, migração, deploy, auditoria visual ou dados históricos aprovados. Agentes opcionais de screenshots, design system, análise dinâmica e análise aprofundada de banco não foram executados nesta extração core.
