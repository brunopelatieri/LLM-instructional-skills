---
name: n8n-js-codex
description: >
  Especialista em JavaScript para n8n Code Node (nó de código). Use SEMPRE que o usuário
  pedir ajuda com código JavaScript dentro do n8n, como: escrever ou depurar um nó Code,
  transformar dados de itens ($input, $json), usar variáveis de ambiente n8n, chamar APIs
  dentro do Code Node, manipular arrays de itens, trabalhar com expressões n8n, integrar
  com outros nós via $node[], ou qualquer tarefa de scripting dentro de um workflow n8n.
  Também use quando o usuário mencionar "Code Node", "Function Node", "n8n script",
  "expressão n8n", "$input.all()", "$json", "$workflow", "itens n8n" ou similares.
  Este skill garante código idiomático, correto e seguindo as convenções do n8n 1.x.
---

# n8n JavaScript Codex 5.3

Skill para escrever, revisar e depurar JavaScript dentro do **Code Node** do n8n (versão 1.x+).

---

## 🏗️ Estrutura do Code Node

O n8n tem **dois modos** de execução no Code Node:

### Modo: Run Once for All Items (padrão recomendado)
```js
// Recebe TODOS os itens de uma vez
const items = $input.all();

// Processar e retornar array de itens
return items.map(item => ({
  json: {
    // seus campos aqui
    campo: item.json.valorOriginal,
  }
}));
```

### Modo: Run Once for Each Item
```js
// Recebe um item por vez — $input.item é o item atual
const nome = $input.item.json.nome;

return {
  json: {
    resultado: nome.toUpperCase(),
  }
};
```

---

## 📦 Variáveis Globais Disponíveis

| Variável | Descrição |
|---|---|
| `$input.all()` | Retorna todos os itens de entrada como array |
| `$input.first()` | Retorna o primeiro item |
| `$input.last()` | Retorna o último item |
| `$input.item` | Item atual (modo Run Once for Each Item) |
| `$json` | Atalho para `$input.item.json` |
| `$node["NomeDoNó"].json` | Dados de outro nó pelo nome |
| `$node["NomeDoNó"].all()` | Todos os itens de outro nó |
| `$workflow.id` | ID do workflow atual |
| `$workflow.name` | Nome do workflow |
| `$execution.id` | ID da execução atual |
| `$execution.mode` | `"manual"` ou `"trigger"` |
| `$vars` | Variáveis definidas no n8n (environment) |
| `$env` | Variáveis de ambiente do sistema |
| `$now` | Data/hora atual como objeto Luxon |
| `$today` | Data de hoje como objeto Luxon |
| `DateTime` | Classe Luxon para manipulação de datas |
| `$getWorkflowStaticData('global')` | Dados persistentes entre execuções |

---

## 🔄 Padrões de Retorno

O Code Node **sempre** deve retornar um dos seguintes formatos:

```js
// ✅ Retorno correto — array de itens
return [
  { json: { campo: "valor" } },
  { json: { campo: "outro" } },
];

// ✅ Retorno com binário (arquivo)
return [
  {
    json: { nome: "arquivo.pdf" },
    binary: {
      data: {
        data: base64String,
        mimeType: "application/pdf",
        fileName: "arquivo.pdf",
      }
    }
  }
];

// ❌ ERRADO — nunca retorne objeto simples
return { campo: "valor" }; // vai quebrar!

// ❌ ERRADO — nunca retorne string
return "ok"; // vai quebrar!
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
const items = $input.all();

return items.filter(({ json }) => json.status === "ativo");
```

### Agrupar itens por campo
```js
const items = $input.all();

const grupos = {};
for (const item of items) {
  const chave = item.json.categoria;
  if (!grupos[chave]) grupos[chave] = [];
  grupos[chave].push(item.json);
}

return Object.entries(grupos).map(([categoria, registros]) => ({
  json: { categoria, registros, total: registros.length }
}));
```

### Fazer requisição HTTP dentro do Code Node
```js
// n8n injeta fetch globalmente
const response = await fetch("https://api.exemplo.com/dados", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    "Authorization": `Bearer ${$vars.API_TOKEN}`,
  },
  body: JSON.stringify({ payload: $input.first().json }),
});

if (!response.ok) {
  throw new Error(`HTTP ${response.status}: ${await response.text()}`);
}

const data = await response.json();

return [{ json: data }];
```

### Trabalhar com datas (Luxon)
```js
const { json } = $input.first();

const dataOriginal = DateTime.fromISO(json.dataStr);
const dataFormatada = dataOriginal.setLocale("pt-BR").toFormat("dd/MM/yyyy");
const diffDias = DateTime.now().diff(dataOriginal, "days").days;

return [{
  json: {
    dataFormatada,
    diasDesde: Math.floor(diffDias),
    expirado: diffDias > 30,
  }
}];
```

### Usar dados de outro nó
```js
// Pega dados do nó chamado "Buscar Usuário"
const usuario = $node["Buscar Usuário"].first().json;
const pedidos = $input.all();

return pedidos.map(({ json: pedido }) => ({
  json: {
    ...pedido,
    nomeCliente: usuario.nome,
    emailCliente: usuario.email,
  }
}));
```

### Dados estáticos persistentes entre execuções
```js
const staticData = $getWorkflowStaticData('global');

// Lê contador anterior (ou 0 se não existir)
const contador = staticData.contador ?? 0;

// Incrementa e salva
staticData.contador = contador + 1;

return [{ json: { execucaoNumero: staticData.contador } }];
```

### Tratar erros com elegância
```js
const items = $input.all();
const resultados = [];

for (const item of items) {
  try {
    const processado = processarItem(item.json); // sua lógica aqui
    resultados.push({ json: { ...processado, erro: null } });
  } catch (err) {
    resultados.push({
      json: {
        dadosOriginais: item.json,
        erro: err.message,
        sucesso: false,
      }
    });
  }
}

return resultados;

function processarItem(dados) {
  // lógica de processamento
  return { ...dados, processado: true };
}
```

---

## ⚠️ Armadilhas Comuns

| Problema | Causa | Solução |
|---|---|---|
| `Cannot read properties of undefined` | Acesso a campo inexistente | Use optional chaining: `json.campo?.subcampo ?? "default"` |
| Retorno vazio quebra workflow | Retornar `[]` ou `undefined` | Sempre retorne ao menos `[{ json: {} }]` |
| `$input.all is not a function` | Modo errado selecionado | Verifique o modo no topo do Code Node |
| Timeout em loops grandes | Loop síncrono pesado | Use processamento em batches ou `Promise.all` |
| Variável não encontrada | Nome errado em `$vars` | Confira em Settings > Variables no n8n |
| `fetch is not defined` | n8n < 0.227 | Atualize o n8n ou use o nó HTTP Request |

---

## 🧩 Templates Prontos

Leia o arquivo de referência quando precisar de templates mais completos:
- **`references/templates.md`** — Templates para casos de uso avançados (paginação, batch processing, merge de arrays, enriquecimento de dados, parsing de CSV/XML)
- **`references/expressions.md`** — Guia de expressões n8n fora do Code Node (`{{ }}`)

---

## ✅ Checklist ao Gerar Código n8n

1. [ ] O retorno é sempre um **array de objetos `{ json: {...} }`**?
2. [ ] Campos opcionais usam **optional chaining** (`?.`) e **nullish coalescing** (`??`)?
3. [ ] Operações assíncronas usam **`async/await`** corretamente?
4. [ ] O modo de execução (all items vs each item) está correto para o caso de uso?
5. [ ] Variáveis sensíveis usam **`$vars`** em vez de hardcode?
6. [ ] Erros são tratados com **try/catch**?
7. [ ] O código é compatível com **n8n 1.x** (sem `items[0].json` direto no topo)?