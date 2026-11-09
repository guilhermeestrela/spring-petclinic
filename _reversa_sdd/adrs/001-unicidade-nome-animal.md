# ADR 001 — Unicidade de nome de animal por responsável

## Status

Retrospectivo, extraído; não é aprovação arquitetural nova. Implementação 🟢 CONFIRMADA; justificativas adicionais permanecem 🟡 INFERIDAS.

## Evidências

Commits locais: `e0db9b1; 88e37c1`. Fontes: `owner/PetController.java; src/main/resources/db/{h2,mysql,postgres}/schema.sql`. Caminhos Java relativos a `src/main/java/org/springframework/samples/petclinic/`.

## Contexto

🟡 INFERIDO — A verificação de nome em memória não protege, sozinha, duas submissões concorrentes.

## Decisão observada

🟢 CONFIRMADO — A aplicação verifica nome e o banco aplica unicidade por responsável; criação/edição usam saveAndFlush para capturar violação nomeada.

## Alternativas consideradas

🟡 INFERIDO — Apenas validar na interface ou apenas no controller seriam alternativas técnicas; não há ata de comparação dessas opções.

## Consequências

🟢 CONFIRMADO — H2/PostgreSQL explicitam comparação sem caixa; MySQL depende de collation não declarada e mensagem de erro cuja compatibilidade é lacuna.
