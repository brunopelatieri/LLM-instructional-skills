---
name: n8n-js-codex
description: >
  Especialista em JavaScript para n8n Code Node (nó de código) versão 2.9.x+. Use SEMPRE
  que o usuário pedir ajuda com código JavaScript dentro do n8n: escrever ou depurar um nó
  Code, transformar dados de itens ($input, $json), usar variáveis n8n ($vars), chamar
  APIs dentro do Code Node, manipular arrays de itens, trabalhar com expressões n8n,
  integrar com outros nós via $node[], ou qualquer tarefa de scripting em workflows n8n.
  Também use quando mencionar "Code Node", "n8n script", "expressão n8n", "$input.all()",
  "$json", "$workflow", "itens n8n", "task runner", "Draft/Publish" ou similares.
  Este skill garante código idiomático e correto para n8n 2.x com Task Runners ativados.
---

# n8n JavaScript Codex 5.3 — otimizado para n8n 2.9.x+

Skill para escrever, revisar e depurar JavaScript dentro do **Code Node** do n8n v2.9.x+.

---

## 🚨 Mudanças Críticas no n8n 2.x

> **Estas mudanças quebram código escrito para n8n 1.x. Leia antes de codificar.**

### 1. Task Runners — obrigatório por padrão
A partir do n8n 2.0, Task Runners são ativados por padrão. Todo código JavaScript do Code Node roda em processos isolados, não mais dentro do processo principal do n8n. Isso significa:
- ✅ Um crash ou loop infinito no seu código **não derruba o servidor**
- ✅ Melhor desempenho e isolamento de memória
- ⚠️ Módulos Node.js precisam ser autorizados via `NODE_FUNCTION_ALLOW_BUILTIN`

### 2. `process.env` bloqueado por padrão
`process.env.SECRET_KEY` dentro de Code Nodes **não funciona mais** no n8n 2.x. Use `$vars`:

```js
// ❌ QUEBRADO no n8n 2.x
const token = process.env.API_TOKEN;

// ✅ CORRETO — variáveis definidas em Settings → Variables
const token = $vars.API_TOKEN;
```

> Para restaurar o comportamento antigo (não recomendado em produção): `N8N_BLOCK_ENV_ACCESS_IN_NODE=false`

### 3. Draft vs Publish — novo ciclo de vida
- **Save** → salva rascunho sem afetar produção
- **Publish** → envia para produção
- Subworkflows com Wait nodes ou human-in-the-loop precisam estar **publicados** para funcionar corretamente com o workflow pai.

---

## 🏗️ Estrutura do Code Node

O n8n tem **dois modos** de execução:

### Modo: Run Once for All Items (padrão recomendado)
```js
const items = $input.all();

return items.map(item => ({
  json: {
    campo: item.json.valorOriginal,
    processadoEm: $now.toISO(),
  }
}));
```

### Modo: Run Once for Each Item
```js
// $input.item é o item atual neste modo
const nome = $input.item.json.nome;

return {
  json: { resultado: nome.toUpperCase() }
};
```

---

## 📦 Variáveis Globais Disponíveis

| Variável | Descrição | Status 2.x |
|---|---|---|
| `$input.all()` | Todos os itens de entrada | ✅ |
| `$input.first()` | Primeiro item | ✅ |
| `$input.last()` | Último item | ✅ |
| `$input.item` | Item atual (modo each item) | ✅ |
| `$json` | Atalho para `$input.item.json` | ✅ |
| `$node["Nome"].json` | Dados de outro nó | ✅ |
| `$node["Nome"].all()` | Todos os itens de outro nó | ✅ |
| `$workflow.id` | ID do workflow | ✅ |
| `$workflow.name` | Nome do workflow | ✅ |
| `$execution.id` | ID da execução | ✅ |
| `$execution.mode` | `"manual"` ou `"trigger"` | ✅ |
| `$vars` | Variáveis configuradas no n8n | ✅ **Preferido** |
| `$env` | Variáveis de ambiente do sistema | ⚠️ Bloqueado por padrão no 2.x |
| `process.env` | Variáveis de ambiente raw | ❌ **Bloqueado no 2.x** |
| `$now` | Data/hora atual (Luxon) | ✅ |
| `$today` | Data de hoje (Luxon) | ✅ |
| `DateTime` | Classe Luxon | ✅ |
| `$getWorkflowStaticData('global')` | Dados persistentes entre execuções | ✅ |

---

## 🔄 Padrões de Retorno

```js
// ✅ Correto — array de itens
return [
  { json: { campo: "valor" } },
  { json: { campo: "outro" } },
];

// ✅ Com binário (arquivo)
return [{
  json: { nome: "relatorio.pdf" },
  binary: {
    data: {
      data: base64String,
      mimeType: "application/pdf",
      fileName: "relatorio.pdf",
    }
  }
}];

// ❌ ERRADO — objeto simples
return { campo: "valor" };

// ❌ ERRADO — string
return "ok";

// ⚠️ array vazio interrompe o fluxo downstream — use intencionalmente
return [];
```

---

## 🛠️ Padrões Comuns

### Transformar / Mapear itens
```js
const items = $input.all();

return items.map(({ json }) => ({
  json: {
    id: json.id,
    nomeCompleto: `${json.primeiroNome} ${json.sobrenome}`,
    emailLower: json.email?.toLowerCase() ?? "",
    processadoEm: $now.toISO(),
  }
}));
```

### Filtrar itens
```js
return $input.all().filter(({ json }) => json.status === "ativo");
```

### Agrupar itens por campo
```js
const grupos = {};
for (const item of $input.all()) {
  const chave = item.json.categoria;
  if (!grupos[chave]) grupos[chave] = [];
  grupos[chave].push(item.json);
}

return Object.entries(grupos).map(([categoria, registros]) => ({
  json: { categoria, registros, total: registros.length }
}));
```

### Requisição HTTP (com `$vars` — padrão 2.x)
```js
const response = await fetch("https://api.exemplo.com/dados", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${$vars.API_TOKEN}`, // $vars, não process.env
  },
  body: JSON.stringify({ payload: $input.first().json }),
});

if (!response.ok) {
  throw new Error(`HTTP ${response.status}: ${await response.text()}`);
}

return [{ json: await response.json() }];
```

### Trabalhar com datas (Luxon)
```js
const { json } = $input.first();

const dataOriginal = DateTime.fromISO(json.dataStr);
const diffDias = DateTime.now().diff(dataOriginal, "days").days;

return [{
  json: {
    dataFormatada: dataOriginal.setLocale("pt-BR").toFormat("dd/MM/yyyy"),
    diasDesde: Math.floor(diffDias),
    expirado: diffDias > 30,
  }
}];
```

### Usar dados de outro nó
```js
const usuario = $node["Buscar Usuário"].first().json;

return $input.all().map(({ json: pedido }) => ({
  json: { ...pedido, nomeCliente: usuario.nome, emailCliente: usuario.email }
}));
```

### Dados estáticos persistentes entre execuções
```js
const staticData = $getWorkflowStaticData('global');
staticData.contador = (staticData.contador ?? 0) + 1;

return [{ json: { execucaoNumero: staticData.contador } }];
```

### Tratamento de erros robusto (2.x)
```js
// No 2.x com Task Runners, erros não derrubam o servidor,
// mas ainda interrompem o item atual sem try/catch
const resultados = [];

for (const item of $input.all()) {
  try {
    const processado = processarItem(item.json);
    resultados.push({ json: { ...processado, sucesso: true } });
  } catch (err) {
    resultados.push({
      json: { dadosOriginais: item.json, erro: err.message, sucesso: false }
    });
  }
}

return resultados;

function processarItem(dados) {
  return { ...dados, processado: true };
}
```

---

## 🔐 Segredos e Credenciais no n8n 2.x

Hierarquia recomendada (da mais segura para a menos):

1. **Credenciais n8n** (Settings → Credentials) — para autenticação OAuth, API keys de serviços externos. Acesse via nó HTTP Request ou nós específicos, não diretamente no Code Node.
2. **`$vars`** (Settings → Variables) — para configurações não-sensíveis: IDs, URLs base, flags de feature.
3. **External Secrets** (Enterprise) — Vault, AWS Secrets Manager, Azure Key Vault.
4. ❌ `process.env` — bloqueado por padrão no 2.x. Não use.

---

## ⚠️ Armadilhas Comuns (n8n 2.x)

| Problema | Causa | Solução |
|---|---|---|
| `process.env.X` retorna `undefined` | Bloqueado por padrão no 2.x | Use `$vars.X` |
| Módulo Node.js não encontrado | Task Runner bloqueia módulos não autorizados | Adicione em `NODE_FUNCTION_ALLOW_BUILTIN` |
| `Cannot read properties of undefined` | Campo inexistente | `json.campo?.subcampo ?? "default"` |
| Retorno vazio interrompe fluxo | `return []` para nós downstream | Use `[{ json: {} }]` se precisar continuar |
| `$input.all is not a function` | Modo errado selecionado | Verifique o modo no topo do Code Node |
| Timeout em loops grandes | Loop síncrono pesado | Use batches ou `Promise.all` |
| `$vars.X` undefined | Variável não criada ou nome errado | Confira em Settings → Variables |
| Subworkflow não retorna dados | Workflow em Draft (não Publicado) | Clique em **Publish** no subworkflow |

---

## 🧩 Templates Prontos

- **`references/templates.md`** — paginação de API, batch processing, deduplicação, flatten, parsing de CSV, chamadas paralelas
- **`references/expressions.md`** — expressões n8n fora do Code Node (`{{ }}`)

---

## ✅ Checklist ao Gerar Código n8n 2.x

1. [ ] Retorno é sempre um **array `[{ json: {...} }]`**?
2. [ ] Usa **`$vars`** em vez de `process.env`?
3. [ ] Campos opcionais usam **`?.`** e **`??`**?
4. [ ] Operações assíncronas usam **`async/await`**?
5. [ ] Modo de execução (all vs each item) está correto?
6. [ ] Erros tratados com **`try/catch`**?
7. [ ] Subworkflows com Wait/human-in-the-loop estão **publicados**?
8. [ ] Módulos Node.js customizados estão autorizados em `NODE_FUNCTION_ALLOW_BUILTIN`?