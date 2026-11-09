# ADR 003 — Normalizar navegação de página fora de faixa

## Status

Retrospectivo, extraído; não é aprovação arquitetural nova. Implementação 🟢 CONFIRMADA; justificativas adicionais permanecem 🟡 INFERIDAS.

## Evidências

Commits locais: `500158f`. Fontes: `owner/OwnerController.java; vet/VetController.java`. Caminhos Java relativos a `src/main/java/org/springframework/samples/petclinic/`.

## Contexto

🟡 INFERIDO — Página fornecida pelo cliente pode estar abaixo de 1 ou além do total.

## Decisão observada

🟢 CONFIRMADO — Consulta usa no mínimo página 1 e desvio posterior redireciona à primeira página em responsáveis e veterinários; tamanho 5.

## Alternativas consideradas

🟡 INFERIDO — Retornar lista vazia, 400 ou 404 seriam alternativas; discussão de escolha não está registrada nos diffs consultados.

## Consequências

🟢 CONFIRMADO — Filtro não vazio de responsável é preservado no redirect; consulta ocorre antes da validação de limite superior. Não há ordenação explícita.
