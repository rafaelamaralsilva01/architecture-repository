## Context

O time usa o Archonex com **MCP** no Cursor para redigir ADRs sem abandonar o IDE. Já existe ADR aprovada (**GLOBAL-0014**) que posiciona a API como fonte da verdade e o MCP como canal principal de autoria. Ainda assim, fluxos com muitas tools (`get_context`, `get_authoring_skills`, `validate`, `create_draft`, `update_draft`) aumentam custo cognitivo do modelo e risco de scripts ad hoc HTTP no repositório. A API passou a **alinhar automaticamente** a linha `id:` do YAML ao `code` gerado na criação do rascunho, e o servidor MCP expõe **`adr.create_draft_validated`** que encadeia validação e criação.

**Alternativas consideradas:** (a) manter apenas tools granulares e documentar a sequência para o agente; (b) obrigar PATCH manual após cada create para alinhar id; (c) **combinar** tool composta no MCP + alinhamento na API como padrão de autoria assistida.

## Decision

Adotar como **fluxo preferencial** para novos rascunhos via MCP: **`adr.get_context`** e **`adr.get_authoring_skills`** quando o contexto ou o texto integral das diretrizes forem necessários; em seguida **`adr.create_draft_validated`** para validar e persistir num único passo, usando o `yamlFrontmatter` devolvido na resposta para sincronizar ficheiros locais. Manter **`adr.create_draft`** e **`adr.update_draft`** para refinamento e cenários manuais. Submissão e aprovação continuam no **portal** ou REST, conforme workflow Archonex — não via MCP.

## Consequences

- **Positivo:** menos passos explícitos para o agente; contrato alinhado à API (sem campo `code` fantasma no create); consistência do `id:` persistido com o `code` canônico.
- **Positivo:** o smoke/CI HTTP continua útil para diagnóstico, sem ser o fluxo diário do Cursor.
- **Negativo:** o corpo markdown enviado pode ainda conter frontmatter embutido desalinhado se o autor duplicar YAML — convém um único bloco YAML coerente com o campo `yamlFrontmatter` ou corpo só Nygard.
- **Risco:** modelos antigos ou skills desatualizadas podem ainda sugerir `validate`+`create`+`PATCH`; mitigar com skills do tenant e instruções do servidor MCP atualizadas.