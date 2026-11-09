# ADR 002 — Visitas como agendamentos futuros

## Status

Retrospectivo, extraído; não é aprovação arquitetural nova. Implementação 🟢 CONFIRMADA; justificativas adicionais permanecem 🟡 INFERIDAS.

## Evidências

Commits locais: `753d35c`. Fontes: `owner/Visit.java; owner/VisitController.java; templates/fragments/inputField.html`. Caminhos Java relativos a `src/main/java/org/springframework/samples/petclinic/`.

## Contexto

🟡 INFERIDO — O formulário precisa impedir datas passadas ou de hoje.

## Decisão observada

🟢 CONFIRMADO — Nova Visit recebe amanhã; controller rejeita datas não nulas até hoje e publica minVisitDate.

## Alternativas consideradas

🟡 INFERIDO — Permitir registro histórico seria alternativa de produto; não há evidência de que foi discutida ou rejeitada formalmente.

## Consequências

🟢 CONFIRMADO — Regra depende de LocalDate.now. 🔴 LACUNA — null não é rejeitado explicitamente e fuso não está configurado.
