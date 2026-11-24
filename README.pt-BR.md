# Diretrizes para Claude Code inspiradas em Karpathy

> Conheça meu novo projeto [Multica](https://github.com/multica-ai/multica) — uma plataforma open source para executar e gerenciar agentes de código com skills reutilizáveis.
>
> Siga-me no X: [https://x.com/jiayuan_jy](https://x.com/jiayuan_jy)

Um único arquivo `CLAUDE.md` para melhorar o comportamento do Claude Code, derivado das [observações de Andrej Karpathy](https://x.com/karpathy/status/2015883857489522876) sobre armadilhas de LLMs em programação.

[English](./README.md) | [简体中文](./README.zh.md) | Português

## Os Problemas

Do post de Andrej:

> "Os modelos fazem suposições erradas em seu nome e seguem em frente com elas sem verificar. Não gerenciam a própria confusão, não pedem esclarecimentos, não apontam inconsistências, não apresentam trade-offs, não questionam quando deveriam."

> "Eles gostam muito de complicar código e APIs, inflar abstrações, não limpam código morto... implementam uma construção inchada de mais de 1000 linhas quando 100 bastariam."

> "Às vezes ainda alteram/removem comentários e código que não entendem bem, como efeito colateral, mesmo quando isso é ortogonal à tarefa."

## A Solução

Quatro princípios em um único arquivo que tratam diretamente esses problemas:

| Princípio | Trata |
|-----------|-------|
| **Pensar Antes de Codar** | Suposições erradas, confusão oculta, trade-offs não apresentados |
| **Simplicidade Primeiro** | Complicação excessiva, abstrações inchadas |
| **Mudanças Cirúrgicas** | Edições ortogonais, mexer em código que não deveria ser tocado |
| **Execução Orientada a Objetivos** | Alavancagem via testes primeiro, critérios de sucesso verificáveis |

## Os Quatro Princípios em Detalhe

### 1. Pensar Antes de Codar

**Não presuma. Não esconda confusão. Apresente trade-offs.**

LLMs muitas vezes escolhem uma interpretação em silêncio e seguem com ela. Este princípio força um raciocínio explícito:

- **Declare suposições explicitamente** — Se estiver em dúvida, pergunte em vez de adivinhar
- **Apresente múltiplas interpretações** — Não escolha em silêncio quando há ambiguidade
- **Questione quando justificado** — Se existe uma abordagem mais simples, diga isso
- **Pare quando estiver confuso** — Nomeie o que não está claro e peça esclarecimento

### 2. Simplicidade Primeiro

**O mínimo de código que resolve o problema. Nada especulativo.**

Combata a tendência à engenharia excessiva:

- Nenhuma funcionalidade além do que foi pedido
- Nenhuma abstração para código de uso único
- Nenhuma "flexibilidade" ou "configurabilidade" que não foi solicitada
- Nenhum tratamento de erro para cenários impossíveis
- Se 200 linhas podem virar 50, reescreva

**O teste:** um engenheiro sênior diria que isso está complicado demais? Se sim, simplifique.

### 3. Mudanças Cirúrgicas

**Toque apenas no que é necessário. Limpe apenas a sua própria bagunça.**

Ao editar código existente:

- Não "melhore" código, comentários ou formatação adjacentes
- Não refatore coisas que não estão quebradas
- Respeite o estilo existente, mesmo que você fizesse diferente
- Se notar código morto não relacionado, mencione — não apague

Quando suas mudanças criarem órfãos:

- Remova imports/variáveis/funções que SUAS mudanças deixaram sem uso
- Não remova código morto pré-existente, a menos que seja pedido

**O teste:** toda linha alterada deve rastrear diretamente até o pedido do usuário.

### 4. Execução Orientada a Objetivos

**Defina critérios de sucesso. Itere até verificar.**

Transforme tarefas imperativas em objetivos verificáveis:

| Em vez de... | Transforme em... |
|--------------|------------------|
| "Adicione validação" | "Escreva testes para entradas inválidas e faça-os passar" |
| "Corrija o bug" | "Escreva um teste que reproduza o bug e faça-o passar" |
| "Refatore X" | "Garanta que os testes passem antes e depois" |

Para tarefas com múltiplos passos, declare um plano breve:

```
1. [Passo] → verificar: [checagem]
2. [Passo] → verificar: [checagem]
3. [Passo] → verificar: [checagem]
```

Critérios de sucesso fortes permitem que o LLM itere de forma independente. Critérios fracos ("faça funcionar") exigem esclarecimentos constantes.

## Instalação

**Opção A: Plugin do Claude Code (recomendado)**

Dentro do Claude Code, primeiro adicione o marketplace:
```
/plugin marketplace add forrestchang/andrej-karpathy-skills
```

Depois instale o plugin:
```
/plugin install andrej-karpathy-skills@karpathy-skills
```

Isso instala as diretrizes como plugin do Claude Code, disponibilizando a skill em todos os seus projetos.

**Opção B: CLAUDE.md (por projeto)**

Projeto novo:
```bash
curl -o CLAUDE.md https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

Projeto existente (acrescentar ao final):
```bash
echo "" >> CLAUDE.md
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md >> CLAUDE.md
```

## Usando com Cursor

Este repositório inclui uma regra de projeto do Cursor versionada ([`.cursor/rules/karpathy-guidelines.mdc`](.cursor/rules/karpathy-guidelines.mdc)), para que as mesmas diretrizes se apliquem ao abrir o projeto no Cursor. Veja **[CURSOR.md](CURSOR.md)** para a configuração, o uso da regra em outros projetos e a relação com o Claude Code.

## Insight Principal

Do Andrej:

> "LLMs são excepcionalmente bons em iterar até atingir objetivos específicos... Não diga o que fazer; dê critérios de sucesso e observe."

O princípio "Execução Orientada a Objetivos" captura isso: transforme instruções imperativas em objetivos declarativos com ciclos de verificação.

## Como Saber que Está Funcionando

Estas diretrizes estão funcionando se você observar:

- **Menos mudanças desnecessárias nos diffs** — Apenas as alterações pedidas aparecem
- **Menos reescritas por complicação excessiva** — O código já sai simples na primeira vez
- **Perguntas de esclarecimento antes da implementação** — Não depois de erros
- **PRs limpos e mínimos** — Sem refatorações oportunistas ou "melhorias" extras

## Personalização

Estas diretrizes foram feitas para ser combinadas com instruções específicas do projeto. Adicione-as ao seu `CLAUDE.md` existente ou crie um novo.

Para regras específicas do projeto, adicione seções como:

```markdown
## Diretrizes Específicas do Projeto

- Use TypeScript em modo strict
- Todos os endpoints de API devem ter testes
- Siga os padrões de tratamento de erro existentes em `src/utils/errors.ts`
```

## Nota sobre Trade-offs

Estas diretrizes privilegiam **cautela em vez de velocidade**. Para tarefas triviais (correções de digitação simples, one-liners óbvios), use o bom senso — nem toda mudança precisa de todo o rigor.

O objetivo é reduzir erros custosos em trabalho não trivial, não desacelerar tarefas simples.

## Licença

MIT
