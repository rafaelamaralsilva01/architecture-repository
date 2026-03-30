## Context
O frontend precisa de uma base tecnológica consistente para manter produtividade, qualidade e previsibilidade de evolução. Sem padronização, surgem decisões ad hoc por tela, maior custo de manutenção e inconsistências de UX.

Alternativas consideradas:
- Stack mínima com React e CSS sem convenções fortes.
- Framework full-stack opinativo para frontend.
- Stack modular com React + TypeScript + Tailwind + shadcn/ui + React Router + React Query, com Zustand restrito a estado local e Tiptap para edição de ADR.

A opção mínima reduz setup inicial, porém tende a gerar divergência de padrões. Framework full-stack pode acelerar partes do desenvolvimento, mas aumenta acoplamento e custo de migração para o contexto atual.

## Decision
Adotar, para o escopo de software, a stack frontend padrão composta por React + TypeScript + Tailwind CSS + shadcn/ui + React Router + React Query; usar Zustand apenas para estado local de UI e Tiptap como editor de ADR.

Também adotar organização orientada a features e separação clara entre UI e lógica de integração com API.

## Consequences
Impactos positivos:
- Código mais consistente e previsível entre funcionalidades.
- Melhor segurança de tipos e menor regressão com TypeScript.
- Entrega de UX mais uniforme com base visual padronizada.
- Fluxo de dados remoto mais robusto com React Query.

Trade-offs e riscos:
- Curva de aprendizagem para novos membros da equipe.
- Necessidade de disciplina para evitar anti-patterns (estado de servidor em Zustand, abstração prematura).
- Dependência de atualização coordenada de bibliotecas principais.

Mitigações:
- Manter diretrizes de arquitetura e UX como referência obrigatória.
- Revisão técnica de PRs focada em aderência ao padrão definido.