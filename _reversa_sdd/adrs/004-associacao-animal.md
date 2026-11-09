# ADR 004 — Preservar animais persistidos na coleção do responsável

## Status

Retrospectivo, extraído; não é aprovação arquitetural nova. Implementação 🟢 CONFIRMADA; justificativas adicionais permanecem 🟡 INFERIDAS.

## Evidências

Commits locais: `676db04`. Fontes: `owner/Owner.java; owner/PetValidator.java`. Caminhos Java relativos a `src/main/java/org/springframework/samples/petclinic/`.

## Contexto

🟡 INFERIDO — A regra anterior de adição de animais exigia revisão para associação de objetos já persistidos.

## Decisão observada

🟢 CONFIRMADO — addPet aceita persistidos e bloqueia objeto já contido ou ID persistido igual; PetValidator limita nome a 30.

## Alternativas consideradas

🟡 INFERIDO — Substituir a coleção por um mapa por ID seria alternativa técnica não documentada no histórico consultado.

## Consequências

🟢 CONFIRMADO — List permanece exposta e busca linear; duplicidade de nome continua responsabilidade de controller/banco.
