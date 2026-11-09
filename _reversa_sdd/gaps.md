# Lacunas finais após validação do usuário

🟢 As 14 perguntas foram respondidas e processadas; decisões de escopo/contexto estão em [validated-decisions.md](validated-decisions.md). Esta lista contém apenas refinamentos ou verificações técnicas remanescentes. Não há solicitação pendente para concluir a extração; estes pontos deverão ser tratados conforme o contexto da evolução.

🟡 Severidade é proposta de priorização, não defeito operacional comprovado. Decisão de destino e comportamento efetivo do código são evidências distintas. Nenhum teste de aplicação/deploy foi executado.

| Severidade proposta | Item | Decisão recebida | Residual |
|---|---|---|---|
| 🔴 Crítico para implantação | D-03 / SIS-D-01 | Rede local | ACL/proxy, exposição de /oups, Actuator e probes no cluster não comprovados; rede local não define autenticação |
| 🔴 Crítico para integridade | D-02 | Somente PostgreSQL no destino | Mensagem real de unicidade e concorrência no PostgreSQL; equivalência MySQL deixa de ser obrigação futura |
| 🔴 Moderado | D-01 | Visita sempre com data futura | Binding null/vazio/omitido atual; definição de validação no destino não foi implementada |
| 🔴 Moderado | D-04 | Sem política de cache | Provedor, tamanho/expiração/invalidação atuais; sem política não implica remover cache |
| 🔴 Moderado | D-05 | São Paulo, mapeado a America/Sao_Paulo | Fuso JVM atual e referência única de dia não comprovados/decididos |
| 🔴 Moderado | D-06 / VET-D-02 | Sem contrato de erro conhecido/definido | Status/body atuais; nenhum contrato novo de API definido |
| 🔴 Moderado | VET-D-01 | Aplicação como único consumidor, simplificação permitida após análise | Campos realmente usados, shape completo e conversores; nenhum campo concreto removido |
| 🔴 Moderado | VET-D-03 | Ordem alfabética desejada | Campo principal, desempate, collation e consistência HTML/body no destino |
| 🔴 Moderado | VET-INV-01 | Dados históricos não precisam ser preservados | Validação de dados novos/inconsistências não definida; sem migração obrigatória e sem exclusão executada |
| 🔴 Moderado | SIS-D-02 | Português e inglês | Fallback/default desejado, variante regional, lang inválido e sessão; default inglês atual preservado como histórico |
| 🔴 Moderado | SIS-D-03 | Página genérica sem detalhes internos | Implementação e status/body de API ainda não definidos; teste ALWAYS não é alvo de produção |
| 🔴 Moderado | SIS-D-04 | Kubernetes, Docker local se viável | Imagem/tag/digest, service binding, probes e arranjo local ainda não validados |
| 🔴 Moderado, condicional | MOD-INV-01 | Sem preservação de dados históricos | Serialização histórica deixa de ser obrigação de migração; suporte nativo não foi exigido, caracterizar somente se necessário |
| 🔴 Moderado, condicional | SIS-INV-01–05 | Destino definido em alto nível | Falhas de view/locale/cache/startup/banco por ambiente, sem execução |
| 🟡 Medição técnica | RESP-D-08 / volume vet | Sem SLA novo | Custo EAGER/catálogo completo e ordem efetiva não medidos |
| 🟡 Cosmético/funcional a caracterizar | EC-20 de responsáveis | Sem pedido de correção de UI | Script de feedback sem guarda null e flash sem redirect: efeito visual não executado |

## Decisões encerradas sem pendência de produto nesta extração

- 🟢 RESP-D-07: “Não” aplicado à preservação de filtro na mudança de página; não há exigência de manter filtro. A interpretação está registrada; links atuais ficam como evidência e nenhuma correção automática é exigida.
- 🟢 Escopo de banco: PostgreSQL. H2/MySQL são contexto do legado, não perfis obrigatórios do destino.
- 🟢 Não migrar dados históricos; tasks de migração permanecem condicionais/não obrigatórias. Isso não autoriza apagar dados existentes.

🟢 Os 31 arquivos sem spec funcional individual continuam enumerados na [matriz](traceability/code-spec-matrix.md). Associação de 101/132 arquivos (76,5%) não é cobertura de testes ou auditoria integral de SCSS/binários/traduções/ambiente de desenvolvimento.

## Refinamento adicional — stack da reescrita

🟢 TARGET-STACK-01: Next.js em monorepo Nx confirmado. 🔴 O desenho de destino deve definir versões, organização apps/libs, fronteiras de servidor, acesso PostgreSQL, contratos, testes e build/deploy Kubernetes. Não reutilizar automaticamente tarefas Java/Spring como plano de implementação. Esses refinamentos não reabrem a extração do legado.
