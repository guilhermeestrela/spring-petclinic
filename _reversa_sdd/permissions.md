# Permissões e controles de acesso

## Validação posterior do usuário

🟢 As 14 respostas foram processadas em [decisões validadas](validated-decisions.md). Destino: PostgreSQL, rede local, fuso America/Sao_Paulo, português/inglês, ordem alfabética de veterinários e erro genérico; migração de dados históricos dispensada. Kubernetes é alvo prioritário e Docker local preferência condicional. Esses requisitos/contextos não alteram o retrato do legado abaixo nem provam runtime. Sem política de cache não significa removê-lo; sem contrato de erro não define status automaticamente.


🟢 CONFIRMADO — Não foram encontrados Spring Security, SecurityFilterChain, @PreAuthorize, @Secured ou @RolesAllowed nos manifests e fontes principais pesquisados. Os controllers examinados não verificam identidade autenticada ou papel. A matriz descreve controles locais observados; acesso público real depende da implantação.

| Operação | Papel implementado | Controle local observado |
|---|---|---|
| Buscar/criar/editar responsável | nenhum identificado | validação de campos, bloqueio de id e *.id; comparação ID de URL na edição |
| Criar/editar animal | nenhum identificado | carregamento via ownerId, catálogo de tipos e validações de domínio |
| Criar visita | nenhum identificado | petId deve pertencer a ownerId; descrição/data validadas conforme código |
| Consultar veterinários HTML/body | nenhum identificado | paginação apenas no HTML; consultas readOnly com cache |
| Alterar idioma via lang | nenhum identificado | preferência por sessão, sem autenticação local |
| /oups e / | nenhum identificado | exceção demonstrativa e página inicial |
| Actuator | nenhum identificado | exposure.include=* em application.properties; acesso efetivo não testado |

🟢 CONFIRMADO — Pertencimento do animal ao responsável é verificação de integridade do relacionamento, não autorização de um usuário para acessar esse responsável. SessionLocaleResolver preserva idioma, não credenciais. Não há isolamento por tenant no modelo examinado.

🔴 LACUNA — Confirmar autenticação/ACL do proxy, restrições dos endpoints operacionais e papéis desejados antes de reimplementar uma versão com requisitos de acesso. Não introduzir papéis na spec como se já existissem.
