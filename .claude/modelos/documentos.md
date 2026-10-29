# Modelos de documentos

Todos estes documentos ficam em `Projeto_Documento/`, nunca em `Projeto/`. Mantenha cada um curto: objetivo e arquitetura são carregados em toda sessão e o espaço custa.

## Seção Projeto do CLAUDE.md

```markdown
# Projeto

**Nome:** <nome>
**Resumo:** <uma linha>
**Stack:** <ex.: .NET 10 Worker Service, PostgreSQL, Dapper>
**Repositório:** <URL do GitHub de Projeto/, ou "ainda não publicado">

Objetivo e escopo: @Projeto_Documento/objetivo.md
Arquitetura: @Projeto_Documento/arquitetura.md
Roadmap e etapas: `Projeto_Documento/roadmap.md` e `Projeto_Documento/etapas/` (lidos pelo `/etapa`, não carregados em toda sessão)
```

## Projeto_Documento/objetivo.md

```markdown
# Objetivo

Status: rascunho | requisitos pendentes | aprovado (AAAA-MM-DD)

## Objetivo
<um parágrafo: o que o sistema faz, para quem e por quê>

## O que o projeto faz
- ...

## O que o projeto NÃO faz
- ...

## Critério de sucesso
- <como saber que o projeto cumpre o objetivo>

## Requisitos
- R1. ...
- R2. <não funcional sempre com número e unidade, ex.: "processa 1.000 itens em menos de 1 min">

## Em aberto
- <dúvida> — <por que ficou em aberto>
```

## Projeto_Documento/arquitetura.md

```markdown
# Arquitetura

Status: rascunho | aprovado (AAAA-MM-DD)

## Visão geral
<2 a 4 frases: como o sistema funciona de ponta a ponta>

## Execução e disponibilidade
- Onde roda:
- Como fica no ar e se recupera de falhas:
- Ciclo de trabalho / agendamento:

## Stack
| Item | Escolha | Decisão |
|------|---------|---------|
| Runtime | ... | 001 |

## Componentes
- <Nome>: <responsabilidade> — <pasta em Projeto/>

## Dados
- Banco:
- Tabelas principais: <nome — o que guarda>

## Integrações externas
| Serviço | Para quê | Se estiver fora do ar |
|---------|----------|------------------------|

## Configuração e segredos
| Segredo | Variável / origem em tempo de execução | Arquivo real em Projeto_Documento/sensivel/ |
|---------|----------------------------------------|--------------------------------------|

## Logs e monitoramento
- ...

## Em aberto
- ...
```

## Projeto_Documento/roadmap.md

```markdown
# Roadmap

Status: rascunho | aprovado (AAAA-MM-DD)

| # | Etapa | Status |
|---|-------|--------|
| 1 | <nome> | pendente |

Status possíveis: pendente → planejada → em andamento → concluída → revisada

## Etapa 1 — <nome>
**Objetivo:** ...

**Entregáveis:**
- ...

**Critérios de aceite:**
- [ ] build sem erros (`BUILD` de `.claude/comandos.conf`)
- [ ] <comando, comportamento observável ou chamada real>

**Fora desta etapa:** ...

## Fora do escopo e ideias
- <ideia> — <origem e data> — avaliar | virou etapa N | descartada
```

## Projeto_Documento/etapas/NN-nome.md

```markdown
# Etapa N — <nome>

Status: planejada | em andamento | concluída | revisada

## Respostas da sessão de perguntas
- P: ... — R: ...

## Padrões a seguir
- <arquivo existente em Projeto/ a imitar> — <por quê> (ou "nenhum: primeira etapa")

## Plano
- [ ] <passo> — arquivos: <caminhos em Projeto/>

## Verificação dos critérios de aceite
- [ ] <critério> — <evidência: comando executado e trecho da saída>

## Notas da execução
- <desvios, problemas encontrados e como foram resolvidos>

## Revisão
<preenchido no início da próxima /etapa>
```

## Projeto_Documento/decisoes/NNN-titulo-curto.md

```markdown
# NNN — <título>

Data: AAAA-MM-DD · Status: aceita | substituída por NNN

## Contexto
<o problema, em 2 ou 3 frases>

## Decisão
<o que foi decidido>

## Alternativas consideradas
- <alternativa> — <por que não>

## Consequências
- ...
```
