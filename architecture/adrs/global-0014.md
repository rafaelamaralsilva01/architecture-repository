## Context

O **Archonex** é uma plataforma de repositório de arquitetura orientada a **Architecture Decision Records (ADRs)**. No escopo atual, ela oferece autoria, revisão, aprovação formal, trilha de auditoria e publicação opcional de documentação em repositórios Git (por exemplo GitHub), com **multi-tenant** desde o início.

**O que a ferramenta faz, de ponta a ponta:**

- **API central (ASP.NET Core)** expõe o domínio (ADRs, projetos, aprovações, políticas, tokens MCP, integrações), persiste em **PostgreSQL** com isolamento por `tenant_id`, e segue organização por **feature folders**.
- **Autenticação e autorização** via JWT (modo desenvolvimento com HS256 ou **OIDC/Keycloak** no MVP), com papéis como Author, Reviewer e Admin.
- **Portal web (React/Vite)** é o canal para leitura confortável, gestão, fluxo de aprovação/rejeição/superseding e administração operacional.
- **Servidor MCP (stdio, Node)** conecta IDEs (Cursor, VS Code) à API em `/api/v1/mcp/*`, oferecendo contexto para escrita, geração de esqueleto **Nygard**, validação de frontmatter e markdown, criação e atualização de rascunhos atribuídos ao dono do token, consulta de status e sugestões de superseding. A submissão ao fluxo de aprovação permanece no portal, alinhada à governança humana.
- **Modelo de documento:** conteúdo em Markdown com **YAML frontmatter** e corpo no template **Nygard** (Context, Decision, Consequences).

Sem uma decisão explícita sobre **onde mora a verdade** do ciclo de vida do ADR, times tendem a fragmentar governança entre Git, wikis e ferramentas de IA, perdendo rastreabilidade e uma única visão auditável.

**Nota:** na sessão de autoria via MCP do IDE, a chamada `adr.get_context` pode falhar por ausência de credenciais (`HTTP 401`); o rascunho segue com base na análise do repositório e da documentação de produto.

## Decision

1. A **API Archonex e o banco relacional** são a **fonte da verdade** para ADRs (identificador, status, conteúdo, relacionamentos, aprovações e auditoria).
2. **Repositório Git** (ex.: GitHub) é tratado como **canal de publicação e visibilidade**, não como sistema de registro primário das decisões.
3. O **MCP** é o **canal principal de autoria assistida** no fluxo do desenvolvedor/arquiteto (incluindo agentes de IA no IDE); o portal complementa com fluxos humanos de aprovação e gestão.

## Consequences

**Positivas:** governança única e auditável; modelo de domínio estável; integração nativa com IDEs e automação; possibilidade de evoluir regras e políticas sem depender de layout de pastas no Git.

**Negativas / custos:** dependência de infraestrutura da API e do banco; necessidade de sincronizar publicação Git com o estado aprovado; operação e rotação de tokens MCP; curva de adoção do MCP nas equipes.

## Alternatives

| Opção | Descrição | Por que não foi escolhida para o Archonex |
|-------|-----------|------------------------------------------|
| **Git-first** | ADRs apenas como arquivos no repositório; workflow via PR/MR. | Prioriza o repo como SoT; dificulta modelo de aprovação nominal, auditoria central e multi-tenant sem camadas adicionais. |
| **Wiki / documentação como SoT** | Confluence, Notion ou wiki genérico como registro principal. | Pouca estrutura de domínio (status, aprovadores, superseding); integração fraca com fluxo de engenharia e IA no IDE. |
| **Somente portal web** | Autoria exclusivamente no browser, sem MCP. | Reduz aderência ao fluxo do desenvolvedor e limita assistência por agentes no ambiente local. |
