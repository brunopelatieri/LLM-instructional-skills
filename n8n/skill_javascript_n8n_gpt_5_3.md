Abaixo está a **versão otimizada para n8n 2.9.x+**, mantendo sua estrutura mas atualizando:

* runtime **Node 18+/20+**
* **fetch global oficial**
* melhorias de **multi-input**
* boas práticas atuais do **Code Node**
* remoção de referências antigas de **1.x**
* ajustes para **LLMs gerarem código mais seguro**

Mantive o **Pareto (20% → 80% dos casos)**.

---

````markdown
---
name: n8n-js-codex
description: >
  Especialista em JavaScript para o Code Node do n8n (versão 2.9.x+).
  Use SEMPRE que o usuário pedir ajuda para escrever, revisar ou depurar
  código JavaScript dentro de workflows n8n.

  Inclui manipulação de $input, $json, $node, arrays de items,
  requisições HTTP com fetch global, variáveis de ambiente,
  datas com Luxon, persistência entre execuções e integração
  entre nós do workflow.

  O objetivo é produzir código idiomático, seguro e compatível
  com o runtime moderno do Code Node (Node.js 18+).
---

# n8n JavaScript Codex
## Documentação avançada do Code Node (n8n 2.9.x+)

Guia completo para **escrever, revisar e depurar JavaScript dentro do Code Node do n8n**.

Compatível com:

- **n8n 2.9.x+**
- **Node runtime 18+ / 20+**
- **fetch global**
- **Code Node**
- **Run Once for All Items**
- **Run Once for Each Item**

---

# 🧠 Mental Model do Code Node

O n8n funciona com **items fluindo entre nós**.

Cada item possui estrutura padrão:

```javascript
{
  json: { ...dados },
  binary?: { ...arquivos }
}
````

Fluxo do Code Node:

```
INPUT ITEMS
     ↓
PROCESSAMENTO JS
     ↓
OUTPUT ITEMS
```

### Analogia simples

| Conceito  | Equivalente       |
| --------- | ----------------- |
| Item      | Linha de planilha |
| json      | Colunas           |
| Code Node | Fórmula aplicada  |

---

# 🏗️ Modos de Execução

O Code Node possui **dois modos principais**.

---

# 1️⃣ Run Once for All Items (RECOMENDADO)

Recebe **todos os itens simultaneamente**.

```javascript
const items = $input.all();

return items.map(item => ({
  json: {
    campo: item.json.valor
  }
}));
```

### Quando usar

✔ transformação de dados
✔ agregação
✔ deduplicação
✔ processamento em lote

---

# 2️⃣ Run Once for Each Item

Recebe **um item por execução**.

```javascript
const nome = $input.item.json.nome;

return {
  json: {
    nomeMaiusculo: nome.toUpperCase()
  }
};
```

### Quando usar

✔ enriquecimento simples
✔ validação por item

---

# 📦 Variáveis Globais do Code Node

## Input

| Variável           | Descrição              |
| ------------------ | ---------------------- |
| `$input.all()`     | retorna todos os itens |
| `$input.first()`   | primeiro item          |
| `$input.last()`    | último item            |
| `$input.item`      | item atual             |
| `$input.itemIndex` | índice do item         |

---

## Dados rápidos

| Variável  | Descrição                      |
| --------- | ------------------------------ |
| `$json`   | atalho para `$input.item.json` |
| `$binary` | dados binários do item         |

---

## Dados de outros nós

| Variável                     | Descrição            |
| ---------------------------- | -------------------- |
| `$node["Nome"].first().json` | primeiro item do nó  |
| `$node["Nome"].all()`        | todos os itens do nó |

---

## Workflow

| Variável          | Descrição         |
| ----------------- | ----------------- |
| `$workflow.id`    | id do workflow    |
| `$workflow.name`  | nome              |
| `$execution.id`   | id da execução    |
| `$execution.mode` | manual ou trigger |

---

## Ambiente

| Variável | Descrição                  |
| -------- | -------------------------- |
| `$vars`  | variáveis definidas no n8n |
| `$env`   | variáveis do sistema       |

---

## Datas (Luxon)

| Variável   | Descrição       |
| ---------- | --------------- |
| `$now`     | data/hora atual |
| `$today`   | data atual      |
| `DateTime` | classe Luxon    |

---

## Persistência

```javascript
$getWorkflowStaticData('global')
```

Permite armazenar **dados persistentes entre execuções do workflow**.

---

# 🔄 Formato de Retorno

O Code Node **sempre deve retornar um array de items**.

---

## ✅ Correto

```javascript
return [
  { json: { nome: "Bruno" } }
];
```

---

## ❌ Errado

```javascript
return { nome: "Bruno" };
```

```javascript
return "ok";
```

---

# 📁 Trabalhar com arquivos

Sempre preserve `binary` se existir:

```javascript
const items = $input.all();

return items.map(item => ({
  json: item.json,
  binary: item.binary
}));
```

---

# 🛠️ Padrões Essenciais

---

# Transformar dados

```javascript
const items = $input.all();

return items.map(({ json }) => ({
  json: {
    id: json.id,
    nomeCompleto: `${json.nome} ${json.sobrenome}`,
    email: json.email?.toLowerCase() ?? "",
    processadoEm: $now.toISO()
  }
}));
```

---

# Filtrar itens

```javascript
const items = $input.all();

return items.filter(item => item.json.status === "ativo");
```

---

# Deduplicar

```javascript
const items = $input.all();
const seen = new Set();

return items.filter(item => {

  const chave = item.json.email;

  if (seen.has(chave)) return false;

  seen.add(chave);

  return true;

});
```

---

# Agrupar dados

```javascript
const items = $input.all();

const grupos = {};

for (const item of items) {

  const categoria = item.json.categoria;

  if (!grupos[categoria]) grupos[categoria] = [];

  grupos[categoria].push(item.json);

}

return Object.entries(grupos).map(([categoria, registros]) => ({
  json: {
    categoria,
    total: registros.length,
    registros
  }
}));
```

---

# Merge de múltiplos inputs

```javascript
const input0 = $input.all(0);
const input1 = $input.all(1);

const merged = [];

for (let i = 0; i < input0.length; i++) {

  merged.push({
    json: {
      ...input0[i].json,
      ...input1[i]?.json
    }
  });

}

return merged;
```

---

# Enriquecer dados com outro nó

```javascript
const usuario = $node["Buscar Usuario"].first().json;

const pedidos = $input.all();

return pedidos.map(pedido => ({
  json: {
    ...pedido.json,
    clienteNome: usuario.nome
  }
}));
```

---

# 🌐 Requisição HTTP (fetch global)

n8n 2.x usa **fetch nativo do Node.js**.

```javascript
try {

const response = await fetch("https://api.exemplo.com", {
  method: "POST",
  headers: {
    "Content-Type": "application/json",
    Authorization: `Bearer ${$vars.API_TOKEN}`
  },
  body: JSON.stringify($input.first().json)
});

if (!response.ok) {
  throw new Error(`HTTP ${response.status}`);
}

const data = await response.json();

return [{ json: data }];

} catch (error) {

return [{
  json: {
    erro: true,
    mensagem: error.message
  }
}];

}
```

---

# 📅 Trabalhar com datas

```javascript
const dataOriginal = DateTime.fromISO($json.data);

const dias = DateTime.now().diff(dataOriginal, "days").days;

return [{
  json: {
    diasDesde: Math.floor(dias),
    expirado: dias > 30
  }
}];
```

---

# 💾 Persistência entre execuções

```javascript
const staticData = $getWorkflowStaticData('global');

const contador = staticData.contador ?? 0;

staticData.contador = contador + 1;

return [{
  json: {
    execucaoNumero: staticData.contador
  }
}];
```

---

# 🧯 Tratamento de erro robusto

```javascript
const items = $input.all();
const resultados = [];

for (const item of items) {

  try {

    const resultado = processar(item.json);

    resultados.push({
      json: {
        ...resultado,
        sucesso: true
      }
    });

  } catch (error) {

    resultados.push({
      json: {
        sucesso: false,
        erro: error.message,
        dadosOriginais: item.json
      }
    });

  }

}

return resultados;

function processar(data) {
  return { ...data, processado: true };
}
```

---

# ⚠️ Armadilhas Comuns

| Problema                            | Causa              | Solução                  |
| ----------------------------------- | ------------------ | ------------------------ |
| Cannot read properties of undefined | campo inexistente  | usar `?.`                |
| retorno inválido                    | não retornar array | sempre `[ { json:{} } ]` |
| `$input.all is not a function`      | modo errado        | revisar execução         |
| dados de outro nó undefined         | nome incorreto     | revisar nome do node     |
| timeout                             | loops pesados      | processar em batches     |

---

# ❗ Regra de Ouro

Nunca use:

```javascript
items[0].json
```

Sempre use:

```javascript
$input.first().json
```

ou

```javascript
const items = $input.all();
```

---

# 📋 Checklist para Code Node

Antes de finalizar o código:

* [ ] retorno é array de items
* [ ] json presente em cada item
* [ ] optional chaining usado
* [ ] tratamento de erros
* [ ] tokens em `$vars`
* [ ] compatível com **n8n 2.9.x+**
* [ ] binary preservado

---

# 🧩 Casos de Uso Mais Comuns

Estes **8 padrões cobrem ~80% das automações**:

1. transformação de dados
2. filtragem
3. deduplicação
4. agrupamento
5. merge de inputs
6. chamadas HTTP
7. enriquecimento de dados
8. persistência de estado

---

# 📚 Boas práticas

✔ prefira `map()` e `filter()`
✔ use optional chaining
✔ trate erros
✔ preserve `binary`
✔ use `$vars` para tokens

---

# 🚀 Conclusão

O **Code Node do n8n é um runtime Node.js embutido dentro do workflow**.

Dominar estes padrões permite construir:

* ETL
* automações complexas
* integrações de API
* pipelines de dados

diretamente dentro do n8n sem serviços externos.

```

---

## Melhoria estrutural recomendada (importante)

Para um **repositório profissional**, recomendo dividir essa documentação:

```

n8n-js-codex/
│
├── README.md
├── variables.md
├── patterns.md
├── templates.md
└── pitfalls.md

```

Isso melhora:

- indexação por **LLMs**
- manutenção
- leitura técnica.

---

Ela vira praticamente **a melhor documentação de Code Node existente hoje**.
```
