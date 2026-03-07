# Guia de Uso de Skills em LLMs
### Como usar Skills no OpenAI Codex CLI, Claude e Google Gemini CLI

> **Versão:** 1.0 · **Compatibilidade:** Codex CLI · Claude Code · Gemini CLI

---

## 📋 Sumário

1. [O que é um Skill?](#1-o-que-é-um-skill)
2. [Anatomia de um Skill](#2-anatomia-de-um-skill)
3. [Como criar seu próprio Skill](#3-como-criar-seu-próprio-skill)
4. [Técnica Universal — System Prompt Manual](#4-técnica-universal--system-prompt-manual)
5. [OpenAI Codex CLI](#5-openai-codex-cli)
6. [Claude Code e claude.ai](#6-claude-code-e-claudeai)
7. [Google Gemini CLI e Gemini app](#7-google-gemini-cli-e-gemini-app)
8. [Usando Skills via API](#8-usando-skills-via-api)
9. [Comparativo entre plataformas](#9-comparativo-entre-plataformas)
10. [Boas práticas](#10-boas-práticas)
11. [Solução de Problemas](#11-solução-de-problemas)

---

## 1. O que é um Skill?

Um **Skill** é um pacote portátil de instruções especializadas que um agente de IA carrega dinamicamente quando precisa executar uma tarefa específica. Pense nele como um "manual de especialista" que o modelo consulta sob demanda — sem precisar repetir as instruções em toda conversa.

```
Sem Skill                          Com Skill
─────────────────────────────      ─────────────────────────────────────
Você → explica o contexto          Você → faz a pergunta diretamente
Você → descreve as convenções      Agente → consulta o Skill automaticamente
Você → lista as regras             Agente → responde com conhecimento especializado
Você → faz a pergunta
Agente → responde
```

### Por que usar Skills?

- **Consistência** — o modelo sempre segue as mesmas convenções, independente da conversa
- **Economia de tokens** — você não repete instruções longas a cada sessão
- **Portabilidade** — um único arquivo `SKILL.md` funciona no Codex CLI, Claude Code e Gemini CLI
- **Composabilidade** — múltiplos skills podem ser carregados juntos para tarefas complexas

### O formato SKILL.md é um padrão aberto

Desenvolvido originalmente pela Anthropic e adotado por OpenAI (Codex CLI) e Google (Gemini CLI), o formato SKILL.md funciona nos três ambientes sem modificações. O mesmo arquivo que você cria hoje para o Claude Code funciona amanhã no Codex CLI ou Gemini CLI.

---

## 2. Anatomia de um Skill

Um skill é uma pasta com a seguinte estrutura mínima:

```
meu-skill/
├── SKILL.md                ← Obrigatório — instruções principais + frontmatter YAML
└── references/             ← Opcional — arquivos de referência adicionais
    ├── exemplos.md
    ├── templates.md
    └── erros-comuns.md
```

### O arquivo SKILL.md

Todo `SKILL.md` começa com um bloco **frontmatter YAML** obrigatório, delimitado por `---`:

```markdown
---
name: meu-skill
description: >
  Descrição clara e específica de quando este skill deve ser ativado.
  Mencione palavras-chave que o usuário provavelmente usará ao pedir
  ajuda com esse tema. Seja específico — esta descrição é o que o
  modelo lê para decidir se deve carregar o skill automaticamente.
---

# Título do Skill

Conteúdo das instruções em Markdown...
```

### Os dois campos obrigatórios do frontmatter

| Campo | Função | Dica |
|---|---|---|
| `name` | Identificador único usado para invocação explícita (`$meu-skill`, `@meu-skill`, `/meu-skill`) | Use kebab-case, sem espaços |
| `description` | Texto que o modelo lê para decidir se ativa o skill automaticamente | Inclua palavras-chave que o usuário usaria ao pedir ajuda |

### Como o carregamento progressivo funciona

Os CLIs implementam um sistema de três camadas para não desperdiçar tokens:

```
Camada 1 — Sempre em contexto (~100 palavras)
└── frontmatter: name + description

Camada 2 — Carregado quando o skill é ativado (< 500 linhas ideal)
└── corpo do SKILL.md

Camada 3 — Carregado sob demanda (tamanho ilimitado)
└── references/*.md  →  scripts, templates, docs de referência
```

O modelo só lê a Camada 3 quando o SKILL.md instrui explicitamente a consultar aquele arquivo.

---

## 3. Como criar seu próprio Skill

### Passo 1 — Crie a estrutura de diretórios

```bash
mkdir -p meu-skill/references
```

### Passo 2 — Escreva o SKILL.md

Modelo de partida comentado:

```markdown
---
name: meu-skill
description: >
  [Descreva em 2-4 linhas O QUE o skill faz e QUANDO deve ser ativado.
  Use as mesmas palavras que um usuário usaria ao pedir ajuda.
  Ex: "Use quando o usuário mencionar X, Y, Z ou pedir ajuda com..."]
---

# Nome do Skill

Breve introdução (1-2 frases sobre o domínio coberto).

---

## Contexto e Convenções

[Explique as regras, padrões e convenções que o modelo deve seguir]

## Padrões de Código / Exemplos

[Blocos de código com os padrões corretos e incorretos]

## Armadilhas Comuns

[Tabela ou lista de erros frequentes e como evitá-los]

## Referências Adicionais

[Instrua o modelo a ler references/templates.md para casos avançados]

## Checklist

[Lista de verificação que o modelo deve seguir antes de responder]
```

### Passo 3 — Adicione arquivos de referência (opcional)

Para conteúdo extenso que só é necessário em casos específicos, crie arquivos em `references/`:

```bash
# Exemplo: templates avançados que só são carregados quando necessário
touch meu-skill/references/templates.md
touch meu-skill/references/erros-comuns.md
```

No `SKILL.md`, instrua o modelo quando carregar cada arquivo:

```markdown
## Referências

Para templates avançados, consulte `references/templates.md`.
Para lista completa de erros e soluções, consulte `references/erros-comuns.md`.
```

### Passo 4 — Empacote como .skill (opcional)

O formato `.skill` é um arquivo ZIP renomeado, usado para distribuição e instalação via interface web (claude.ai):

```bash
# No diretório pai do skill
zip -r meu-skill.skill meu-skill/

# Verifique o conteúdo
unzip -l meu-skill.skill
```

---

## 4. Técnica Universal — System Prompt Manual

> **Funciona em qualquer LLM** — ChatGPT, Claude, Gemini, Mistral, LLaMA, APIs em geral. Não requer nenhuma instalação.

Esta é a técnica mais simples e portátil. Consiste em copiar o conteúdo do `SKILL.md` e enviá-lo como **contexto inicial** da conversa, envolto em tags XML.

### Como aplicar

**Passo 1** — Abra o arquivo `meu-skill/SKILL.md` e copie todo o conteúdo.

**Passo 2** — Inicie uma nova conversa com o modelo. Envie como **primeira mensagem**:

```
<skill name="meu-skill">
[COLE AQUI TODO O CONTEÚDO DO SKILL.md]
</skill>

A partir de agora, use essas instruções para responder minhas perguntas sobre [domínio].
```

**Passo 3** — O modelo confirmará o carregamento. Use normalmente a partir daí.

### Por que as tags XML funcionam

Todos os LLMs modernos são treinados para reconhecer estrutura XML como delimitadores semânticos. As tags `<skill>` sinalizam ao modelo que aquele bloco contém "instruções de sistema" — não conteúdo da conversa — e deve ser tratado com maior peso e fidelidade.

### Exemplo completo

```
<skill name="revisor-de-codigo-python">
---
name: revisor-de-codigo-python
description: Especialista em revisão de código Python seguindo PEP 8,
  boas práticas de tipagem, segurança e performance.
---

# Revisor de Código Python

## Convenções obrigatórias
- Sempre use type hints em funções e métodos
- Prefira f-strings a .format() ou concatenação
- Use `with` para gerenciar arquivos e conexões
- Nunca use `except Exception` sem logar o erro

## Checklist de revisão
1. O código tem type hints completos?
2. Há algum risco de SQL injection ou path traversal?
3. Os loops poderiam ser substituídos por list comprehensions?
4. As docstrings estão no formato Google Style?
</skill>

Revise o código abaixo e aponte melhorias:

def calcular_desconto(preco, percentual):
    resultado = preco - (preco * percentual / 100)
    return resultado
```

### Quando incluir arquivos de referência

Se o skill tem `references/`, você pode incluir arquivos relevantes na mesma mensagem:

```
<skill name="meu-skill">
[conteúdo do SKILL.md]
</skill>

<skill-reference name="templates">
[conteúdo do references/templates.md]
</skill-reference>

Minha pergunta: [...]
```

### Limitações desta técnica

| Limitação | Impacto | Mitigação |
|---|---|---|
| Contexto não persiste entre conversas | Você precisa colar o skill a cada nova sessão | Salve o bloco `<skill>` num snippet / atalho de teclado |
| Consome tokens em todo o histórico | Custo levemente maior em conversas longas | Inicie nova conversa quando o histórico estiver extenso |
| Sem ativação automática | O skill está sempre ativo (não seletivo) | Geralmente desejável para uso focado |
| Sem lazy loading de references | Você decide quais referências incluir | Inclua apenas o necessário |

---

## 5. OpenAI Codex CLI

### Suporte nativo: ✅ SKILL.md sem modificações

O Codex CLI implementa o padrão aberto de Agent Skills. Qualquer skill com um `SKILL.md` válido é detectado e carregado automaticamente.

### Instalação

#### Opção A — Skill global (disponível em todos os projetos)

```bash
# Crie o diretório de skills do usuário (se não existir)
mkdir -p ~/.codex/skills/meu-skill

# Copie os arquivos do skill
cp -r /caminho/para/meu-skill/* ~/.codex/skills/meu-skill/
```

#### Opção B — Skill de projeto (apenas no repositório atual)

```bash
# Dentro da raiz do seu projeto/repositório
mkdir -p .agents/skills/meu-skill
cp -r /caminho/para/meu-skill/* .agents/skills/meu-skill/
```

#### Opção C — Apontar para um caminho customizado

```toml
# ~/.codex/config.toml
[[skills]]
path = "/caminho/absoluto/para/meu-skill/SKILL.md"
```

### Ativar o recurso de Skills (se necessário)

Em algumas versões do Codex CLI, skills podem estar atrás de uma feature flag:

```bash
codex --enable skills
```

### Verificar se o skill foi detectado

Dentro de uma sessão do Codex CLI, execute:

```
/skills
```

Você verá a lista de skills disponíveis com nome e descrição.

### Formas de invocar o skill

**Invocação implícita** — o Codex detecta automaticamente quando a sua tarefa corresponde à `description` do skill. Basta descrever o que você quer:

```
Preciso criar um módulo que valida CPF e CNPJ em Python.
```
> ↳ Se você tem um skill `validacao-documentos-brasil`, ele será ativado automaticamente.

**Invocação explícita com `$`** — chame o skill pelo nome:

```
$meu-skill Como devo estruturar o retorno desta função?
```

**Invocação explícita com `@`** — sintaxe alternativa:

```
@meu-skill Revise esse trecho de código e aponte problemas.
```

### Estrutura de diretórios e precedência

O Codex busca skills nas seguintes localizações, **nesta ordem** (maior para menor precedência):

```
./                           ← Diretório atual (menor precedência)
<git-root>/.agents/skills/   ← Skills do projeto ✅ recomendado para equipes
~/.codex/skills/             ← Skills globais do usuário ✅ recomendado para uso pessoal
~/.codex/skills/.system/     ← Skills built-in do sistema (maior precedência)
```

### Integração com AGENTS.md

O arquivo `AGENTS.md` na raiz do projeto fornece contexto persistente ao Codex. Use-o para referenciar skills obrigatórios no projeto:

```markdown
# AGENTS.md

## Sobre este projeto
Sistema de e-commerce em Node.js + TypeScript.

## Skills obrigatórios
Sempre use o skill `$meu-skill` ao trabalhar com código de pagamentos.
Sempre use `$typescript-conventions` ao gerar ou revisar TypeScript.

## Regras gerais
- Nunca commite credenciais
- Toda função pública deve ter JSDoc
- Use sempre os tipos do domínio em `src/types/`
```

### Desativar um skill temporariamente

```toml
# ~/.codex/config.toml
[[skills.config]]
path = "/caminho/para/meu-skill/SKILL.md"
enabled = false
```

---

## 6. Claude Code e claude.ai

O ecossistema Claude oferece **três ambientes distintos** para usar skills, cada um com seu próprio método de instalação.

---

### 6.1 Claude Code CLI — Suporte nativo: ✅

O Claude Code é a CLI oficial da Anthropic para uso em terminal. Implementa skills nativamente com o mesmo formato SKILL.md.

#### Instalação

```bash
# Skill global — disponível em todos os projetos
mkdir -p ~/.claude/skills/meu-skill
cp -r /caminho/para/meu-skill/* ~/.claude/skills/meu-skill/

# Skill de projeto — apenas no projeto atual (recomendado para equipes)
mkdir -p .claude/skills/meu-skill
cp -r /caminho/para/meu-skill/* .claude/skills/meu-skill/
```

#### Verificar disponibilidade

```
/skills
```

O skill `meu-skill` deve aparecer na lista com nome e descrição.

#### Formas de invocar o skill

**Implícita** — o modelo detecta automaticamente pela `description`:
```
Crie uma função que valida e normaliza números de telefone brasileiros.
```

**Explícita com `/`** — invocação direta pelo nome:
```
/meu-skill Como devo tratar erros neste caso?
```

#### Diferença técnica do Claude Code

No Claude Code, o skill é ativado via uma ferramenta interna chamada `Skill`, que injeta o conteúdo do `SKILL.md` na janela de contexto no momento exato em que é necessário (lazy loading real). A descoberta de skills disponíveis é feita na inicialização, lendo apenas o frontmatter de cada `SKILL.md` — sem carregar o corpo completo.

#### Integração com CLAUDE.md

O arquivo `CLAUDE.md` é o equivalente do `AGENTS.md` no ecossistema Claude:

```markdown
# CLAUDE.md

## Projeto
API REST em Python + FastAPI para gestão de contratos.

## Skills ativos neste projeto
- `meu-skill` — use sempre ao trabalhar com lógica de contratos
- `fastapi-conventions` — use sempre ao criar ou modificar endpoints

## Padrões do projeto
- Toda rota deve ter validação Pydantic
- Use sempre `async def` para handlers de rota
- Erros devem usar as classes em `app/exceptions/`
```

---

### 6.2 claude.ai (interface web) — Upload de .skill

Para quem usa o Claude via navegador, skills são instalados como arquivos `.skill`.

#### Como instalar

1. Acesse [claude.ai](https://claude.ai)
2. Vá em **Settings** → **Skills**
3. Clique em **+ Adicionar Skill** ou **Upload Skill**
4. Faça upload do arquivo `meu-skill.skill`
5. O skill ficará disponível em **todas as suas conversas**

> O arquivo `.skill` é um ZIP renomeado. Para criar um a partir da pasta do skill:
> ```bash
> zip -r meu-skill.skill meu-skill/
> ```

#### Como usar após instalação

O skill é ativado automaticamente quando você menciona tarefas relacionadas. Você também pode referenciar explicitamente:

```
Use o skill meu-skill para revisar esse código antes de eu fazer o commit.
```

---

### 6.3 Claude via API (Anthropic SDK)

Para integrar skills em aplicações que consomem a API da Anthropic diretamente:

**Python:**

```python
import anthropic
from pathlib import Path

# Carrega o conteúdo do skill
skill_content = Path("meu-skill/SKILL.md").read_text(encoding="utf-8")

client = anthropic.Anthropic()  # usa ANTHROPIC_API_KEY do ambiente

response = client.messages.create(
    model="claude-sonnet-4-6",
    max_tokens=2048,
    system=f"""Você é um assistente especializado. Siga as instruções do skill abaixo:

<skill name="meu-skill">
{skill_content}
</skill>

Aplique rigorosamente as convenções do skill em todas as respostas.""",
    messages=[
        {"role": "user", "content": "Minha pergunta ou tarefa aqui..."}
    ]
)

print(response.content[0].text)
```

**Node.js / TypeScript:**

```typescript
import Anthropic from "@anthropic-ai/sdk";
import { readFileSync } from "fs";

const skillContent = readFileSync("meu-skill/SKILL.md", "utf-8");
const client = new Anthropic(); // usa ANTHROPIC_API_KEY do ambiente

const response = await client.messages.create({
  model: "claude-sonnet-4-6",
  max_tokens: 2048,
  system: `Você é um assistente especializado. Siga as instruções do skill abaixo:

<skill name="meu-skill">
${skillContent}
</skill>

Aplique rigorosamente as convenções do skill em todas as respostas.`,
  messages: [
    { role: "user", content: "Minha pergunta ou tarefa aqui..." }
  ],
});

console.log(response.content[0].text);
```

**Carregando múltiplos skills:**

```python
from pathlib import Path

def carregar_skill(nome: str) -> str:
    """Carrega o conteúdo de um skill pelo nome."""
    caminho = Path(f"skills/{nome}/SKILL.md")
    if not caminho.exists():
        raise FileNotFoundError(f"Skill '{nome}' não encontrado em {caminho}")
    return caminho.read_text(encoding="utf-8")

# Compor system prompt com múltiplos skills
skills_ativos = ["validacao-dados", "formatacao-relatorio", "regras-negocio"]

system_prompt = "Você é um assistente especializado. Siga as instruções dos skills abaixo:\n\n"
for skill_nome in skills_ativos:
    conteudo = carregar_skill(skill_nome)
    system_prompt += f'<skill name="{skill_nome}">\n{conteudo}\n</skill>\n\n'

# Usar na chamada da API normalmente
```

---

## 7. Google Gemini CLI e Gemini app

O Google adotou o padrão aberto SKILL.md no Gemini CLI. O mesmo arquivo funciona sem alterações.

---

### 7.1 Gemini CLI — Suporte nativo: ✅

#### Instalação

```bash
# Skill global — disponível em todos os projetos
mkdir -p ~/.gemini/skills/meu-skill
cp -r /caminho/para/meu-skill/* ~/.gemini/skills/meu-skill/

# Skill de projeto — apenas no projeto atual
mkdir -p .gemini/skills/meu-skill
cp -r /caminho/para/meu-skill/* .gemini/skills/meu-skill/
```

#### Localização dos skills (precedência)

```
<projeto>/.gemini/skills/    ← Skills do projeto (maior precedência) ✅
~/.gemini/skills/            ← Skills globais do usuário ✅
```

#### Formas de invocar o skill

**Implícita** — detecção automática pela `description`:
```
Preciso escrever um módulo de autenticação JWT em Go.
```

**Explícita com `@`** — invocação direta:
```
@meu-skill Qual o padrão correto para esta operação?
```

#### Integração com GEMINI.md

O arquivo `GEMINI.md` é o equivalente do `AGENTS.md` / `CLAUDE.md` para o Gemini CLI:

```markdown
# GEMINI.md

## Sobre este projeto
Microserviço de notificações em Go + gRPC.

## Skills ativos
Ao trabalhar com lógica de envio de mensagens, use sempre `@meu-skill`.
Ao revisar código Go, use sempre `@golang-conventions`.

## Restrições
- Nunca gere código com goroutines sem context.Context
- Toda função exportada deve ter godoc
```

---

### 7.2 Gemini app (gemini.google.com) — Gems

O Gemini app na web não suporta SKILL.md nativamente. O equivalente funcional são os **Gems** — personas customizadas com instruções persistentes.

#### Como criar um Gem a partir de um Skill

1. Acesse [gemini.google.com](https://gemini.google.com)
2. No menu lateral, clique em **Explorar Gems** → **Novo Gem**
3. Dê o mesmo `name` do skill como nome do Gem
4. No campo **Instruções**, cole o conteúdo do `SKILL.md` **sem o bloco frontmatter YAML** (remova as linhas entre `---` e `---`)
5. Faça upload dos arquivos de `references/` como documentos anexos (opcional)
6. Clique em **Salvar**

> O Gem ficará disponível como um "modo" de conversa no painel lateral. Basta clicar nele para ativar uma conversa já com o skill carregado.

#### Diferença entre Gem e SKILL.md nativo

| | SKILL.md (CLI nativo) | Gem (app web) |
|---|---|---|
| Ativação automática | ✅ | ✅ (sempre ativo ao abrir o Gem) |
| Lazy loading de referencias | ✅ | ❌ (inclua tudo nas instruções) |
| Compartilhamento com equipe | Via arquivo .skill | Via link do Gem (feature paga) |
| Invocação explícita | `@meu-skill` | Abrir o Gem na barra lateral |

---

### 7.3 Gemini via API (Google AI / Vertex AI)

**Python:**

```python
from google import genai
from google.genai.types import GenerateContentConfig
from pathlib import Path

skill_content = Path("meu-skill/SKILL.md").read_text(encoding="utf-8")

client = genai.Client()  # usa GOOGLE_API_KEY do ambiente

response = client.models.generate_content(
    model="gemini-2.5-flash",
    contents="Minha pergunta ou tarefa aqui...",
    config=GenerateContentConfig(
        system_instruction=f"""Você é um assistente especializado.
Siga rigorosamente as instruções do skill abaixo:

<skill name="meu-skill">
{skill_content}
</skill>"""
    )
)

print(response.text)
```

**Node.js:**

```javascript
const { GoogleGenAI } = require("@google/genai");
const { readFileSync } = require("fs");

const skillContent = readFileSync("meu-skill/SKILL.md", "utf-8");

// ATENÇÃO: use uma variável de ambiente, nunca hardcode a chave
const client = new GoogleGenAI({ apiKey: process.env.GOOGLE_API_KEY });

const response = await client.models.generateContent({
  model: "gemini-2.5-flash",
  contents: "Minha pergunta ou tarefa aqui...",
  config: {
    systemInstruction: `Você é um assistente especializado.
Siga rigorosamente as instruções do skill abaixo:

<skill name="meu-skill">
${skillContent}
</skill>`,
  },
});

console.log(response.text);
```

---

## 8. Usando Skills via API

Esta seção consolida os padrões para uso via API, independente de plataforma.

### Regra fundamental: skill no `system`, não no `user`

```python
# ✅ CORRETO — skill no system prompt
response = client.messages.create(
    system=f"<skill name='meu-skill'>{skill_content}</skill>",
    messages=[{"role": "user", "content": "Minha pergunta"}]
)

# ❌ EVITE — skill como mensagem de usuário (perde fidelidade)
response = client.messages.create(
    messages=[
        {"role": "user", "content": f"<skill>{skill_content}</skill>\nMinha pergunta"}
    ]
)
```

### Utilitário reutilizável

```python
# skill_loader.py — utilitário genérico para qualquer projeto

from pathlib import Path
from typing import Union

SKILLS_DIR = Path(__file__).parent / "skills"

def carregar_skill(nome: str, incluir_references: list[str] | None = None) -> str:
    """
    Carrega um skill pelo nome e opcionalmente seus arquivos de referência.
    
    Args:
        nome: Nome da pasta do skill (ex: "meu-skill")
        incluir_references: Lista de arquivos em references/ para incluir
                           (ex: ["templates.md", "erros-comuns.md"])
    
    Returns:
        String formatada com o skill pronto para uso no system prompt
    
    Exemplo:
        skill = carregar_skill("meu-skill", incluir_references=["templates.md"])
        system = f"Você é um especialista. {skill}"
    """
    skill_dir = SKILLS_DIR / nome
    skill_md = skill_dir / "SKILL.md"

    if not skill_md.exists():
        raise FileNotFoundError(
            f"Skill '{nome}' não encontrado.\n"
            f"Esperado em: {skill_md}\n"
            f"Skills disponíveis: {[d.name for d in SKILLS_DIR.iterdir() if d.is_dir()]}"
        )

    conteudo = skill_md.read_text(encoding="utf-8")
    resultado = f'<skill name="{nome}">\n{conteudo}\n</skill>'

    if incluir_references:
        for ref_nome in incluir_references:
            ref_path = skill_dir / "references" / ref_nome
            if ref_path.exists():
                ref_conteudo = ref_path.read_text(encoding="utf-8")
                resultado += f'\n\n<skill-reference name="{ref_nome}">\n{ref_conteudo}\n</skill-reference>'

    return resultado


def system_prompt_com_skills(papel: str, skills: list[str]) -> str:
    """
    Monta um system prompt completo com múltiplos skills.
    
    Args:
        papel: Descrição do papel do assistente
        skills: Lista de nomes de skills a incluir
    
    Exemplo:
        system = system_prompt_com_skills(
            papel="Você é um engenheiro de software sênior.",
            skills=["python-conventions", "seguranca-api", "testes-unitarios"]
        )
    """
    blocos = [papel, ""]
    for skill_nome in skills:
        try:
            blocos.append(carregar_skill(skill_nome))
        except FileNotFoundError as e:
            print(f"⚠️  Aviso: {e}")
    return "\n".join(blocos)
```

---

## 9. Comparativo entre plataformas

| Característica | Codex CLI | Claude Code | claude.ai web | Gemini CLI | Gemini app | Via API |
|---|:---:|:---:|:---:|:---:|:---:|:---:|
| Suporte nativo a SKILL.md | ✅ | ✅ | ✅ `.skill` | ✅ | ❌ Gems | ❌ Manual |
| Ativação automática (implícita) | ✅ | ✅ | ✅ | ✅ | ✅ | Manual |
| Invocação explícita | `$nome` `@nome` | `/nome` | menção por nome | `@nome` | — | — |
| Skills globais (usuário) | `~/.codex/skills/` | `~/.claude/skills/` | Upload Settings | `~/.gemini/skills/` | Gems | System Prompt |
| Skills de projeto | `.agents/skills/` | `.claude/skills/` | — | `.gemini/skills/` | — | — |
| Arquivo de contexto do projeto | `AGENTS.md` | `CLAUDE.md` | — | `GEMINI.md` | — | — |
| Lazy loading de references | ✅ | ✅ | ✅ | ✅ | ❌ | ❌ |
| Múltiplos skills simultâneos | ✅ | ✅ | ✅ | ✅ | ❌ | ✅ manual |
| Técnica Universal funciona | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |
| Compartilhar com equipe via repo | ✅ | ✅ | ❌ | ✅ | ❌ | ✅ |

---

## 10. Boas Práticas

### Escrevendo uma boa `description`

A `description` é o componente mais crítico do skill — é o único texto que o modelo lê **antes** de decidir se ativa o skill. Uma descrição ruim faz com que o skill nunca seja ativado.

```yaml
# ❌ Descrição ruim — vaga demais
description: Ajuda com código Python.

# ❌ Descrição ruim — técnica demais, sem palavras-chave do usuário
description: Implementa padrões PEP8, type hints e docstrings Google Style.

# ✅ Descrição boa — menciona contextos, palavras-chave do usuário e casos de uso
description: >
  Especialista em Python para APIs REST. Use quando o usuário pedir ajuda com
  código Python, revisão de funções, criação de endpoints FastAPI/Flask,
  validação de dados, tratamento de erros ou boas práticas de tipagem.
  Também ative quando mencionar PEP8, type hints, pytest, Pydantic ou mypy.
```

### Tamanho ideal do SKILL.md

```
< 200 linhas   → Ideal. Cabe confortavelmente em qualquer janela de contexto.
200–500 linhas → Aceitável. Considere mover detalhes para references/.
> 500 linhas   → Dividir. Mova exemplos longos e tabelas para references/.
```

### Estrutura recomendada de conteúdo

1. **Contexto e escopo** — o que o skill cobre e o que está fora do escopo
2. **Regras obrigatórias** — convenções inegociáveis (use linguagem direta: "sempre", "nunca")
3. **Padrões com exemplos** — blocos de código correto × incorreto
4. **Armadilhas comuns** — erros frequentes e como evitá-los
5. **Referências** — ponteiros para `references/*.md` quando necessário
6. **Checklist** — lista de verificação antes de entregar a resposta

### Versionamento de skills

Trate skills como código — coloque-os no controle de versão:

```
projeto/
├── .agents/skills/        ← Para Codex CLI
│   └── meu-skill/
├── .claude/skills/        ← Para Claude Code
│   └── meu-skill/         (pode ser symlink de .agents/skills/meu-skill)
├── .gemini/skills/        ← Para Gemini CLI
│   └── meu-skill/         (pode ser symlink de .agents/skills/meu-skill)
└── skills/                ← Fonte canônica (usada nas APIs)
    └── meu-skill/
        ├── SKILL.md
        └── references/
```

Com symlinks, você mantém um único arquivo e todos os CLIs usam a mesma versão:

```bash
# Cria a fonte canônica
mkdir -p skills/meu-skill

# Cria os symlinks para cada CLI
mkdir -p .agents/skills .claude/skills .gemini/skills
ln -s ../../skills/meu-skill .agents/skills/meu-skill
ln -s ../../skills/meu-skill .claude/skills/meu-skill
ln -s ../../skills/meu-skill .gemini/skills/meu-skill
```

---

## 11. Solução de Problemas

### O skill não é ativado automaticamente

**Sintoma:** O modelo responde sem aplicar as convenções do skill, mesmo quando a tarefa é claramente relacionada.

**Diagnóstico:**
1. Execute `/skills` (Codex/Claude) ou `@skills` (Gemini) para confirmar que o skill está listado
2. Verifique se a `description` contém palavras-chave que você está usando na pergunta

**Soluções:**
- Use invocação explícita: `$meu-skill`, `/meu-skill` ou `@meu-skill`
- Melhore a `description` com mais palavras-chave do vocabulário natural do usuário
- Mencione explicitamente o domínio na sua pergunta

---

### O skill não aparece em `/skills`

**Diagnóstico e soluções:**

```bash
# 1. Verifique se o arquivo existe no lugar certo
ls ~/.codex/skills/meu-skill/SKILL.md        # Codex
ls ~/.claude/skills/meu-skill/SKILL.md       # Claude Code
ls ~/.gemini/skills/meu-skill/SKILL.md       # Gemini CLI

# 2. Verifique se o frontmatter YAML é válido
head -5 ~/.codex/skills/meu-skill/SKILL.md
# Deve mostrar: ---
#               name: meu-skill
#               description: ...

# 3. Reinicie o CLI após instalar um novo skill
# O CLI lê os skills na inicialização — novos skills requerem reinício
```

---

### O modelo ignora as instruções do skill a meio da conversa

**Causa:** "Context rot" — em conversas longas, instruções do início perdem peso relativo à medida que o histórico cresce.

**Soluções:**
- Inicie uma nova conversa
- Adicione um lembrete explícito: `"Lembre-se de seguir as instruções do skill meu-skill ao responder."`
- Para APIs: coloque o skill no `system` prompt (não como mensagem de usuário)
- Para conversas longas: resuma e recarregue o skill a cada 20-30 mensagens

---

### Conflito entre múltiplos skills

**Sintoma:** Com dois ou mais skills ativos, o modelo mistura convenções de ambos de forma incoerente.

**Soluções:**
- Use invocação explícita para indicar qual skill aplicar em cada mensagem
- Adicione uma seção `## Escopo` em cada skill, definindo claramente o que está fora do escopo
- Ordene os skills no system prompt do mais específico para o mais geral

---

### Como atualizar um skill instalado

```bash
# Atualize os arquivos no diretório de instalação
cp -r /caminho/para/meu-skill-atualizado/* ~/.codex/skills/meu-skill/

# Reinicie o CLI para recarregar o skill
# Codex: ctrl+c e abra nova sessão
# Claude Code: /exit e abra nova sessão
# Gemini CLI: ctrl+c e abra nova sessão
```

Se você estiver usando symlinks para a fonte canônica, basta editar o arquivo original — todos os CLIs verão a versão atualizada na próxima sessão.

---

## Referências

- [Agent Skills — Anthropic Engineering Blog](https://www.anthropic.com/engineering/equipping-agents-for-the-real-world-with-agent-skills)
- [Claude Code Skills — Documentação oficial](https://code.claude.com/docs/en/skills)
- [OpenAI Codex CLI Skills — Documentação oficial](https://developers.openai.com/codex/skills/)
- [Using Agent Skills in Gemini CLI — Google Cloud Blog](https://medium.com/google-cloud/beyond-prompt-engineering-using-agent-skills-in-gemini-cli-04d9af3cda21)
- [Anthropic SDK — Python](https://github.com/anthropic/anthropic-sdk-python)
- [Anthropic SDK — Node.js](https://github.com/anthropic/anthropic-sdk-typescript)
- [Google GenAI SDK — Python](https://github.com/google/generative-ai-python)
- [Google GenAI SDK — Node.js](https://github.com/google/generative-ai-js)

---

## 👤 Autor | Author

<table>
  <tr>
    <td>
      <strong>Bruno Pelatieri Goulart</strong><br/>
      <em>Pesquisador em IA & Automação · AI Researcher & Automation Engineer</em>
    </td>
  </tr>
</table>

| | |
|---|---|
| 🌐 **Site** | [brunogoulart.com.br](https://brunogoulart.com.br) |
| 💼 **LinkedIn** | [bruno-pelatieri-goulart](https://www.linkedin.com/in/bruno-pelatieri-goulart) |
| 🐙 **GitHub** | [brunopelatieri](https://github.com/brunopelatieri) |
| 📧 **Email** | [brunopelatieri@gmail.com](mailto:brunopelatieri@gmail.com) |
| 💬 **WhatsApp** | [+55 (19) 9 9924-9658](https://wa.me/5519999249658) |

---

> *"Construindo pontes entre inteligência artificial e o mundo real."*
> *"Building bridges between artificial intelligence and the real world."*

---

## 📦 Sobre este repositório | About this repository

> Tutorial completo sobre uso de Skills em LLMs (Codex CLI, Claude Code e Gemini CLI) — padrão aberto SKILL.md com exemplos práticos para n8n JavaScript (Code Node v2.9.x+)
>
> Complete tutorial on using Skills in LLMs (Codex CLI, Claude Code and Gemini CLI) — open SKILL.md standard with practical examples for n8n JavaScript (Code Node v2.9.x+)

**Tags:**

`llm` `ai-skills` `skill-md` `codex-cli` `claude` `gemini` `n8n` `javascript` `automation` `prompt-engineering` `agents` `ai-tools` `workflow` `openai` `anthropic` `google-gemini`

---