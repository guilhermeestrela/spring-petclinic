# Plano de geração de specs

Status: geração concluída; 30/30 arquivos. Revisão pendente. Nível detalhado; module; Português.

Mapeamento: owner → responsaveis; vet → veterinarios; model → modelo; system → sistema.

Um arquivo por interação, com checkpoint ao concluir. Arquivos existentes serão preservados. OpenAPI terá contratos HTTP comprovados e lacunas explícitas; não presumirá 404/409 para exceções não mapeadas.

## Arquivos

- [x] 1. `_reversa_sdd/responsaveis/requirements.md`
- [x] 2. `_reversa_sdd/responsaveis/design.md`
- [x] 3. `_reversa_sdd/responsaveis/tasks.md`
- [x] 4. `_reversa_sdd/responsaveis/contracts.md`
- [x] 5. `_reversa_sdd/responsaveis/edge-cases.md`
- [x] 6. `_reversa_sdd/responsaveis/questions.md`
- [x] 7. `_reversa_sdd/responsaveis/legacy-mapping.md`
- [x] 8. `_reversa_sdd/responsaveis/tests.md`
- [x] 9. `_reversa_sdd/veterinarios/requirements.md`
- [x] 10. `_reversa_sdd/veterinarios/design.md`
- [x] 11. `_reversa_sdd/veterinarios/tasks.md`
- [x] 12. `_reversa_sdd/veterinarios/contracts.md`
- [x] 13. `_reversa_sdd/veterinarios/edge-cases.md`
- [x] 14. `_reversa_sdd/veterinarios/questions.md`
- [x] 15. `_reversa_sdd/veterinarios/legacy-mapping.md`
- [x] 16. `_reversa_sdd/modelo/requirements.md`
- [x] 17. `_reversa_sdd/modelo/design.md`
- [x] 18. `_reversa_sdd/modelo/tasks.md`
- [x] 19. `_reversa_sdd/modelo/edge-cases.md`
- [x] 20. `_reversa_sdd/modelo/legacy-mapping.md`
- [x] 21. `_reversa_sdd/sistema/requirements.md`
- [x] 22. `_reversa_sdd/sistema/design.md`
- [x] 23. `_reversa_sdd/sistema/tasks.md`
- [x] 24. `_reversa_sdd/sistema/contracts.md`
- [x] 25. `_reversa_sdd/sistema/edge-cases.md`
- [x] 26. `_reversa_sdd/sistema/questions.md`
- [x] 27. `_reversa_sdd/sistema/legacy-mapping.md`
- [x] 28. `_reversa_sdd/openapi/petclinic.yaml`
- [x] 29. `_reversa_sdd/user-stories/operacao-clinica.md`
- [x] 30. `_reversa_sdd/traceability/code-spec-matrix.md`

## Critérios

- Canônicos seguem templates do Redator; critérios Dado/Quando/Então, tarefas com origem e pronto.
- Confiança por afirmação; sem transformar decisões pendentes em regras confirmadas.
- Edge cases cobrem ao menos dois casos por unit; questions registra apenas lacunas aplicáveis.
- Legado não será alterado e comandos de build não foram executados.
