# 🧠 Karpathy Skills — Quick Guide para Claude Code

> **65 linhas que mudam como agentes de IA escrevem código — e uma análise honesta de onde o impacto é real**

[![Stars](https://img.shields.io/badge/GitHub%20Stars-100k+-yellow?style=flat-square&logo=github)](https://github.com/forrestchang/andrej-karpathy-skills)
[![Claude Code](https://img.shields.io/badge/Claude%20Code-Impacto%20Parcial-blueviolet?style=flat-square&logo=anthropic)](.)
[![Cursor](https://img.shields.io/badge/Cursor-Impacto%20Alto-black?style=flat-square)](https://cursor.sh)
[![Copilot](https://img.shields.io/badge/GitHub%20Copilot-Impacto%20Alto-blue?style=flat-square&logo=github)](.)
[![AGENTS.md](https://img.shields.io/badge/AGENTS.md-Compatible-green?style=flat-square)](.)
[![Pareto](https://img.shields.io/badge/Regra%20de%20Pareto-20%25%20→%2080%25-orange?style=flat-square)](.)
[![Avaliação](https://img.shields.io/badge/Avaliação-Honesta%20%26%20Técnica-red?style=flat-square)](.)

---

## 📋 Índice

- [Origem e Contexto](#-origem-e-contexto)
- [O Problema Real](#-o-problema-real)
- [Os 4 Princípios](#-os-4-princípios)
- [O CLAUDE.md Original](#-o-claudemd-original-na-íntegra)
- [Instalação](#️-instalação)
- [Antes e Depois](#-antes-e-depois)
- [Integração com Cursor](#-integração-com-cursor)
- [Extensões da Comunidade](#-extensões-da-comunidade)
- [Honestidade sobre o Impacto Real por Ferramenta](#️-honestidade-sobre-o-impacto-real-por-ferramenta)
- [TL;DR](#-tldr)

---

## 🎯 Origem e Contexto

Este não é um projeto de Andrej Karpathy — **o arquivo foi criado por [Forrest Chang](https://github.com/forrestchang)** em 27 de janeiro de 2026, um dia após Karpathy publicar um post no X descrevendo os erros sistêmicos que ele observou ao migrar de ~80% de codificação manual para ~80% de codificação orientada a agentes.

Karpathy identificou os problemas. Chang codificou as soluções em um único arquivo markdown de 65 linhas.

O repositório `forrestchang/andrej-karpathy-skills` acumulou mais de 100 mil stars, tornando-se um dos repositórios mais estrelados da história do GitHub apesar de conter apenas um único arquivo markdown.

### Quem é Andrej Karpathy?

- Co-fundador da **OpenAI** e ex-diretor de IA da **Tesla** (Autopilot)
- Criador do curso **[Neural Networks: Zero to Hero](https://karpathy.ai/zero-to-hero.html)** — referência global para quem quer entender LLMs de verdade
- Fundador da **Eureka Labs**, empresa de educação com IA
- Cunhou o termo "vibe coding" no início de 2025 para descrever um estilo onde você diz ao AI o que quer e avalia o resultado sem ler o código

---

## 🔴 O Problema Real

Karpathy identificou **3 falhas recorrentes** de LLMs ao programar em modo agêntico, com base em sua experiência real de transição:

**Premissas silenciosas:** "The models make wrong assumptions on your behalf and just run along with them without checking. They don't manage their confusion, don't seek clarifications, don't surface inconsistencies, don't present tradeoffs, don't push back when they should."

**Supercomplexidade:** "They really like to overcomplicate code and APIs, bloat abstractions, don't clean up dead code... implement a bloated construction over 1000 lines when 100 would do."

**Dano colateral:** "They still sometimes change/remove comments and code they don't sufficiently understand as side effects, even if orthogonal to the task."

Uma quarta observação gerou o princípio de execução:

> *"LLMs are exceptionally good at looping until they meet specific goals... Don't tell it what to do, give it success criteria and watch it go."*
> — Andrej Karpathy, X post, 26 jan 2026

### Mapeamento direto: falha → princípio

| Falha observada | Princípio que corrige |
|---|---|
| Assume interpretações sem perguntar | **Think Before Coding** |
| Gera código desnecessariamente complexo | **Simplicity First** |
| Altera código adjacente ao solicitado | **Surgical Changes** |
| Executa sem critério verificável de sucesso | **Goal-Driven Execution** |

---

## 🧩 Os 4 Princípios

### 1. Think Before Coding — Pensar Antes de Codar

**Regra central:** Não assuma. Não esconda confusão. Exponha trade-offs.

Antes de implementar qualquer coisa:

- Declare premissas explicitamente — se incerto, pergunte
- Se múltiplas interpretações existirem, apresente todas — não escolha silenciosamente
- Se uma abordagem mais simples existir, diga — questione quando necessário
- Se algo estiver confuso, pare — nomeie o que está confuso e pergunte

**Teste de self-check:** se incerto → pergunte. Se múltiplas interpretações → apresente-as.

**Exemplo de contraste:**

```
❌ Sem o princípio:
Usuário: "Adicione exportação de dados."
Claude: [escreve função que exporta tudo em JSON e CSV, escolhe campos
         sozinho, define localização do arquivo, sem nenhuma pergunta]

✅ Com o princípio:
Claude: "Antes de implementar, preciso confirmar 4 pontos:
  1. Escopo: todos os registros ou um subconjunto filtrado?
  2. Formato: download no browser, job em background ou endpoint de API?
  3. Campos: quais podem conter dados sensíveis?
  4. Volume esperado: afeta a estratégia de streaming."
```

---

### 2. Simplicity First — Simplicidade Primeiro

**Regra central:** Código mínimo que resolve o problema. Nada especulativo.

O que é proibido por padrão:

- Features além do que foi pedido
- Abstrações para código de uso único
- "Flexibilidade" ou "configurabilidade" não solicitadas
- Error handling para cenários impossíveis

**Self-check interno:** *"Um engenheiro sênior diria que isso está complicado demais? Se sim, simplifique."*

**Exemplo clássico do repo:**

```python
# ❌ Sem o princípio — pedido: "função para calcular desconto"
class DiscountStrategy(ABC):
    @abstractmethod
    def apply(self, amount: float) -> float: ...

class PercentageDiscount(DiscountStrategy):
    def __init__(self, config: DiscountConfig): ...

class DiscountCalculator:
    def __init__(self, strategy: DiscountStrategy): ...
# → 30+ linhas de setup para uma multiplicação

# ✅ Com o princípio
def calculate_discount(amount: float, percent: float) -> float:
    return amount * (percent / 100)
```

> 💡 Quando adicionar complexidade: apenas quando você realmente precisar dela. Se o requisito aparecer depois, refatore então.

---

### 3. Surgical Changes — Mudanças Cirúrgicas

**Regra central:** Toque apenas o que deve. Limpe apenas a própria bagunça.

Ao editar código existente:

- Não "melhore" código adjacente, comentários ou formatação
- Não refatore o que não está quebrado
- Mantenha o estilo existente, mesmo que você faria diferente
- Se notar dead code não relacionado, **mencione** — não delete

Ao criar orphans com suas mudanças:

- Remova imports/variáveis/funções que **suas** mudanças tornaram desnecessários
- Não remova dead code pré-existente sem ser solicitado

**O teste:** cada linha alterada deve ter rastreabilidade direta ao pedido do usuário.

**Exemplo real:**

```
❌ Sem o princípio — pedido: "corrija o crash com e-mail vazio"
- reformata aspas em todo o arquivo
- adiciona type hints em todas as funções
- acrescenta docstring
- adiciona validação de tamanho de username (não pedida)
- adiciona validação alfanumérica (não pedida)

✅ Com o princípio — diff de 2 linhas:
+ if not email:
+     return False, "Email cannot be empty"
```

> ⚠️ Esse princípio é crítico em projetos com módulos legados em produção. Uma mudança colateral em código que você não entende completamente pode quebrar dependências invisíveis.

---

### 4. Goal-Driven Execution — Execução Orientada a Metas

**Regra central:** Defina critérios de sucesso. Loop até verificar.

Transforme tarefas em metas verificáveis:

```
"Adicione validação"   →  "Escreva testes para inputs inválidos, depois os faça passar"
"Corrija o bug"        →  "Escreva um teste que o reproduza, depois o faça passar"
"Refatore X"           →  "Garanta que os testes passam antes e depois"
```

Para tarefas multi-passo, declare o plano:

```
1. [Passo] → verificar: [checagem]
2. [Passo] → verificar: [checagem]
3. [Passo] → verificar: [checagem]
```

**Critérios fortes** permitem o modelo iterar de forma independente.
**Critérios fracos** ("faça funcionar") exigem intervenção a cada passo.

---

## 📄 O `CLAUDE.md` Original na Íntegra

Este é o arquivo completo de 65 linhas que você instala no seu projeto:

```markdown
# CLAUDE.md

Behavioral guidelines to reduce common LLM coding mistakes.
Merge with project-specific instructions as needed.

**Tradeoff:** These guidelines bias toward caution over speed.
For trivial tasks, use judgment.

## 1. Think Before Coding

**Don't assume. Don't hide confusion. Surface tradeoffs.**

Before implementing:
- State your assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them - don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

Ask yourself: "Would a senior engineer say this is overcomplicated?"
If yes, simplify.

## 3. Surgical Changes

**Touch only what you must. Clean up only your own mess.**

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it - don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

The test: Every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]

Strong success criteria let you loop independently.
Weak criteria ("make it work") require constant clarification.

---

**These guidelines are working if:** fewer unnecessary changes in diffs,
fewer rewrites due to overcomplication, and clarifying questions come
before implementation rather than after mistakes.
```

---

## ⚙️ Instalação

### Opção A — Plugin Global (Claude Code Marketplace)

Aplica as diretrizes em **todos os projetos** de uma vez:

```bash
# Adiciona ao marketplace
/plugin marketplace add forrestchang/andrej-karpathy-skills

# Instala o plugin
/plugin install andrej-karpathy-skills@karpathy-skills
```

> Claude Code lê o arquivo no início de cada sessão como contexto comportamental para toda a conversa naquele diretório.

---

### Opção B — Por Projeto (Mais Simples)

**Projeto novo** — cria o `CLAUDE.md` do zero:

```bash
curl -o CLAUDE.md \
  https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md
```

**Projeto existente** — faz append no `CLAUDE.md` atual:

```bash
echo "" >> CLAUDE.md && \
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md \
  >> CLAUDE.md
```

> Para outros agentes como **OpenCode** ou **Hermes**, renomeie para `AGENTS.md`.

---

### Opção C — Integração Manual no `AGENTS.md`

Se você já tem um `CLAUDE.md` ou `AGENTS.md` estruturado no projeto, adicione como seção dedicada:

```markdown
## Karpathy Coding Guidelines

Antes de qualquer implementação:

1. **Declare premissas** — liste o que você está assumindo;
   se ambíguo, pergunte antes de avançar
2. **Código mínimo** — nada especulativo, nada além do pedido
3. **Toque só o necessário** — sem refatorações colaterais,
   sem "melhorias" não solicitadas
4. **Critério de sucesso** — como saberemos que funcionou?
   Defina antes de executar

Se ambíguo: pergunte. Nunca assuma e avance.
```

---

## 🔄 Antes e Depois

| Situação | ❌ Sem a skill | ✅ Com a skill |
|---|---|---|
| Tarefa ambígua | Claude escolhe uma interpretação e executa | Claude apresenta as interpretações e pergunta |
| Feature simples | Gera abstrações, classes, configurabilidade extra | Escreve o mínimo que resolve |
| Bug fix em código legado | Pode alterar formatação, refatorar adjacências | Toca apenas as linhas causadoras do bug |
| Tarefa vaga ("faça funcionar") | Executa sem critério, você descobre o erro no review | Define critério verificável antes de executar |
| Migration de banco de dados | Pode adicionar índices ou colunas "úteis" não pedidas | Declara exatamente o que vai alterar e o que vai preservar |
| Código em produção | Pode gerar regressão silenciosa | Cada linha alterada rastreável ao pedido |

---

## 🖱️ Integração com Cursor

O repositório inclui uma regra de projeto Cursor comprometida em `.cursor/rules/karpathy-guidelines.mdc`, de modo que as mesmas diretrizes se aplicam quando você abre o projeto no Cursor.

Para usar em outros projetos no Cursor:

```bash
# Cria a estrutura de regras do Cursor no projeto
mkdir -p .cursor/rules

# Baixa a regra formatada para Cursor
curl -o .cursor/rules/karpathy-guidelines.mdc \
  https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/.cursor/rules/karpathy-guidelines.mdc
```

---

## 🌱 Extensões da Comunidade

A comunidade construiu extensões sobre o padrão original. O mais notável:

**Karpathy-skills v2** — adiciona regras para sistemas de AI pipeline:

- **LLM apenas para julgamento** — fetching, filtering, routing e persistência devem usar código determinístico, não LLM
- **Token budgets** — cada passo de AI roda sob orçamento configurável; exceder o limite encerra o pipeline e loga a violação
- **Sem overruns silenciosos** — qualquer estouro de budget é surfaceado ao operador, nunca silenciado

```yaml
# Exemplo de configuração de budget (v2)
budgets:
  per_step_tokens: 2048
  per_pipeline_tokens: 10000
  per_day_tokens: 100000
```

> Ref: [Karpathy-skills CLAUDE.md v2](https://gist.github.com/renezander030/2898eb5f0100688f4197b5e493e156a2)

---

## ✅ Como saber que está funcionando

O próprio arquivo define os sinais de que as diretrizes estão tendo efeito:

```
✅ Diffs com menos mudanças desnecessárias
✅ Menos rewrites por complicação excessiva
✅ Perguntas de clarificação ANTES da implementação, não depois
✅ Plano declarado antes de tarefas multi-passo
✅ Critério de sucesso definido antes de executar
```

---

## ⚠️ Honestidade sobre o Impacto Real por Ferramenta

> Entusiasmo não ajuda aqui. A resposta honesta é: **a skill agrega muito — mas de formas diferentes dependendo da ferramenta que você usa.**

---

### O que realmente muda em cada agente

A skill Karpathy é frequentemente vendida como uma solução universal. Na prática, ela resolve problemas reais — mas o quanto resolve depende diretamente de **qual agente está lendo o arquivo**.

---

### Para Cursor, GitHub Copilot e outros agentes de IDE

**Aqui o impacto é real e direto.**

Cursor, GitHub Copilot (workspace mode), Cline, Windsurf e ferramentas similares **não embalam nenhuma diretriz comportamental equivalente** às regras Karpathy em seus system prompts. Quando eles leem um `.cursor/rules/karpathy-guidelines.mdc` ou um `AGENTS.md`, estão recebendo instruções que genuinamente não estavam lá antes.

O Cursor, em particular, indexa o projeto localmente e usa `.cursorrules` / `.cursor/rules/` para comportamento específico de projeto. Adicionar as regras Karpathy ali é uma mudança de comportamento real — o agente vai literalmente ter instruções diferentes sobre como tratar código adjacente, complexidade e ambiguidade.

| Ferramenta | Tem diretrizes nativas equivalentes? | Impacto da skill |
|---|---|---|
| **Cursor Agent** | ❌ Não | 🟢 Alto — muda comportamento real |
| **GitHub Copilot** | ❌ Não | 🟢 Alto — especialmente no workspace mode |
| **Cline / OpenCode** | ❌ Não | 🟢 Alto — lê `AGENTS.md` diretamente |
| **Windsurf** | ❌ Parcial | 🟡 Médio — algumas diretrizes embutidas |
| **Claude Code** | ✅ Parcialmente | 🟡 Baixo a médio — veja abaixo |

---

### Para Claude Code — a conversa mais honesta

O Claude Code **já embute no seu system prompt** muitas das diretrizes que a skill Karpathy define explicitamente. Isso não é visível para o usuário, mas está lá:

| Princípio Karpathy | Equivalente no Claude Code nativo |
|---|---|
| **Simplicity First** | "Don't add abstractions beyond what the task requires" — embutido no `claude_code` preset |
| **Surgical Changes** | "Don't refactor surrounding code" / "Match existing style" — presentes como instrução de sistema |
| **Goal-Driven Execution** | Parcialmente — Claude Code tem comportamento cauteloso nativo, mas sem o loop de verificação explícito |
| **Think Before Coding** | Parcialmente — Claude Code pede confirmação antes de ações destrutivas, mas não declara premissas sistematicamente |

O que isso significa na prática: para o **Claude Code**, adicionar o `CLAUDE.md` Karpathy funciona mais como um **reforço explícito** do que como uma mudança de comportamento nova. É útil — especialmente porque a pesquisa mostra que modelos conseguem seguir de 150 a 200 instruções distintas por contexto, e diretrizes explícitas no `CLAUDE.md` funcionam como um lembrete de alta prioridade via `<system-reminder>`. Mas não espere uma transformação dramática de comportamento.

> 💡 **O CLAUDE.md é injetado como `<system-reminder>` nas mensagens, não no system prompt fixo.** O system prompt do Claude Code é imutável e cacheado. As instruções do seu `CLAUDE.md` chegam como contexto de alta prioridade na conversa — o modelo as trata com seriedade, mas o system prompt nativo tem precedência em caso de conflito.

#### Por que ainda vale para Claude Code

Mesmo com sobreposição parcial, há valor real em dois cenários:

**1. Contrato explícito para tarefas de risco** — quando você manda o Claude Code editar um módulo legado crítico, ter a regra de Surgical Changes documentada explicitamente no `AGENTS.md` funciona como um contrato formal. O modelo tem o contexto escrito de que não deve tocar nos demais nós ou módulos. Isso reduz o risco de dano colateral em sistemas onde uma regressão tem custo alto.

**2. Equipes com múltiplos agentes** — se o projeto usa Claude Code + Cursor + GitHub Copilot em paralelo (o padrão mais comum entre devs sêniores em 2026), um único `AGENTS.md` com as regras Karpathy cria um **comportamento consistente entre ferramentas**. O que o Cursor entende é o mesmo que o Claude Code entende.

---

### O que tem mais impacto do que a skill em si

Se a pergunta é "onde investir tempo em governança de AI no meu projeto", o retorno tende a ser maior em:

```
1. Testes de regressão documentados para módulos críticos
   → hoje a verificação de "funcionou?" costuma ser manual
   → testes automatizados dão ao agente critério de sucesso real

2. Contratos de interface explícitos no AGENTS.md
   → o que cada workflow pode e não pode modificar
   → quais arquivos são "intocáveis" sem confirmação explícita

3. Exemplos de tarefas perigosas no AGENTS.md
   → "ao editar migrations, sempre declare o schema antes e depois"
   → "ao modificar workflows de produção, liste os nós afetados primeiro"

4. Critérios de sucesso por tipo de tarefa
   → "feature nova = testes passando + sem warnings no lint"
   → "bug fix = teste que reproduz o bug + teste que confirma a correção"
```

As diretrizes Karpathy são boas e você deve instalar. Mas o gargalo real na maioria dos projetos não é o comportamento do AI — é a ausência de critérios objetivos de sucesso para as tarefas que você manda executar.

---

### Resumo de decisão

```
Usa Cursor / Copilot / Cline como agente principal?
   → Instale imediatamente. Impacto real e direto.

Usa Claude Code como agente principal?
   → Instale como contrato explícito para tarefas críticas.
     Não espere transformação dramática, mas o reforço tem valor.

Usa múltiplos agentes no mesmo projeto?
   → Instale no AGENTS.md. Cria consistência entre ferramentas,
     que é o benefício mais subestimado da skill.
```

---

## ⚡ TL;DR

```bash
# Instala em 30 segundos — projeto novo
curl -o CLAUDE.md \
  https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md

# Instala em 30 segundos — projeto existente
echo "" >> CLAUDE.md && \
curl https://raw.githubusercontent.com/forrestchang/andrej-karpathy-skills/main/CLAUDE.md \
  >> CLAUDE.md
```

**Os 4 princípios em uma linha cada:**

```
1. Think Before Coding  →  declare premissas, pergunte o ambíguo
2. Simplicity First     →  mínimo que resolve, nada especulativo
3. Surgical Changes     →  toque só o pedido, limpe só sua bagunça
4. Goal-Driven          →  critério de sucesso antes de executar
```

> **Regra de Pareto aplicada:** esses 4 princípios são os 20% que resolvem 80% dos problemas com LLMs gerando código desnecessariamente complexo, tocando o que não devem ou assumindo sem perguntar.

---

<div align="center">

[![Repositório Original](https://img.shields.io/badge/Repositório%20Original-forrestchang%2Fandrej--karpathy--skills-black?style=for-the-badge&logo=github)](https://github.com/forrestchang/andrej-karpathy-skills)
[![Fork Multica](https://img.shields.io/badge/Fork%20-multica--ai%2Fandrej--karpathy--skills-grey?style=for-the-badge&logo=github)](https://github.com/multica-ai/andrej-karpathy-skills)
[![Plugin Hub](https://img.shields.io/badge/Claude%20Plugin%20Hub-Ver%20Plugin-blueviolet?style=for-the-badge&logo=anthropic)](https://www.claudepluginhub.com/plugins/forrestchang-andrej-karpathy-skills)

*65 linhas. 4 princípios. Um agente mais disciplinado.*

</div>
